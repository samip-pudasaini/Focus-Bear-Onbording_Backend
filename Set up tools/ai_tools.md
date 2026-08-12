# Set Up AI tools for Development

## Research
From my previous uses and experience with the AI platform I use the integration of all of the various platforms.
The reason for this is because sometimes different platform have different way of handling things and give different methods for the same problems, which can be compared and integrated as per your own project.
While copilot is directly on your code editor helping you in the same platform and can directly access the file you are using to see if you have made any typos, variable naming error and all. 
ChatGPT and Claude on the other hand can be used more easily for specific tasks with good and precise prompts and also runs less risk of any data leaks.

## Experimentation

###  Generating code snippets

I tested Copilot by writing a comment describing a task I'd already solved manually before 
// Express middleware to verify JWT from HTTP-only cookie, and compared its suggestion to my own implementation from a past project. It got the overall structure right (checking req.cookies, verifying with jsonwebtoken), but it used a generic cookie name (token) instead of matching the one I actually used, and didn't handle the expired-token case as gracefully. I had to add that myself. This showed me that Copilot is great at scaffolding boilerplate quickly, but it doesn't know your project's conventions unless it can see them in open files or existing code, so the output still needs a careful review rather than a blind accept.

### Debugging with AI

For the debugging test, I gave Copilot Chat a bug I'd already fixed before, without telling it the answer: an Express 5 wildcard route (app.get("*", ...)) that broke in production after path-to-regexp was upgraded to v8. It correctly identified that the wildcard syntax was the issue and suggested replacing it with a regex route (app.get(/.*/, ...)), which matched the fix I'd used. It was faster than re-searching GitHub issues, but only because I gave it the exact error message — when I described the symptom vaguely instead, the first suggestion it gave was wrong, which reinforced how much a good prompt matters.

### Asking AI to explain a new concept

I asked ChatGPT and Copilot Chat to both explain idempotency keys, since it's something I hadn't worked with directly. Both gave similar high-level explanations, but ChatGPT's was more thorough with a worked example (a payment retry scenario), while Copilot's answer stayed closer to code — showing how to implement an idempotency check in an Express route. Cross-checking both against the Stripe API docs, the explanations held up accurately. This matched what I noted in my research: different platforms are useful for different angles on the same problem, and comparing them gave me a more complete understanding than relying on just one.