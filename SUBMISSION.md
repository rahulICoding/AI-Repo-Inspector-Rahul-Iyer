# Submission

## What did you investigate first, and why?

What I chose to investigate first was the MCP servers data flow from input to the core logic. I did this because of the happy path problem, signaling that there was some error boundaries or rejections not handled properly.

## What did you choose to implement or fix?

Change 1: In mcp-server.ts there was a input named repoPath which was not named properly. The implementation was meant to read it as repo_path was the original repoPath would be passed as undefined cause it to silently fail.

Change 2: replaced the reject error line which prevents the fail test command from running as the AI would never read the failure logs. I changed the line from that to resolve({ status: "failed", output: ... }) as it would allow the AI to actuall resolve and return an error. I also increased the max buffer to 10MB which would help prevent crashes.

## What did you intentionally not do?
What I intentionally did not do was changing the exec to spawn which I wasnt too sure for this problem would be necessary. The exec would allow for focusing on the core crashing bugs that would occur.

## Interface decision

- Decision: MCP-First
- Primary user and execution environment: AI agents using the MCP protocol. The execution env would be Node.js process exposing the tools to the client.
- Trust boundary and allowed capabilities: Originally it was too trusting as it didnt have a check on the repo name or having any errors ever being revealed
- Reliability, discoverability, latency/context, and output tradeoffs: MCP now will prioritize context over human readabilitiy. The tradeoff is that the outputs are wrapped in JSon and caught in validation which is better than the CLI first option.

- How supported interfaces remain consistent: It keeps the same logic but it is now able to have the CLI wrapper be added later without changing any of the MCPs core logic.

- Evidence that would change this decision: If the choice changed to using the tool in CD pipelines without an Agent than having a CLI first architecture would be better as it would be better for humans to use.


## How did you use an AI coding agent?

I used AI to help with understanding the code and using it to help understand the Node.js edge cases. I also used it to help with understanding the architectural difference between using exec or spawn. 

## Where did you check, correct, or reject an AI suggestion? (required)

AI suggested that creating a buffer for 5MB would be enough however I changed it to 10MB which would help provide a larger safety net for operations like npm install. I also had it help me with correct a syntax error when I was making changed to the validation.ts

## Commands used to verify the result, with outcomes

npm run typecheck - 0 typescript errors
npm test - passed all tests


## A blocker you hit and how you approached it

A blocker I hit was learning about the exec versus spawn. I had to learn the architectural differences and understand that while spawn is better for production, patching the existing exec buffer was the most pragmatic choice given the 90-minute time constraint. I also ran into some starting difficulties as I wasnt sure how to begin however I decided to use AI to help create a overall codebase strcutre to understand how everything worked before I started to make changes.

## Known limitations and the next three things you would do
I need to validate that `repo_path` is a legitimate, safe directory that is there
Add a timeout mechanism to the validation commands so a hanging script doesnt stop the tool from working indefinitely. I would also make sure the validation commands are only limited to a set amount of safe terminal commands so the AI cant execute destructive commands

## Approximate focused-work time

- Start: 1:10pm
- Finish: 2pm.