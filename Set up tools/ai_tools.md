# Set Up AI tools for Development

## Research

From my previous uses and experience with the AI platform I use the integration
of all of the various platforms. The reason for this is because sometimes
different platform have different way of handling things and give different
methods for the same problems, which can be compared and integrated as per your
needs.

While copilot is directly on your code editor helping you in the same platform
and can directly access the file you are using to see if you have made any typos,
variable naming error and all. ChatGPT and Claude on the other hand can be used
more easily for specific tasks with good and precise prompts and also runs less
risk of any data leaks.

## Experimentation

### Generating code snippets

I tested Copilot by writing a comment describing a task I'd already solved
manually before // Express middleware to verify JWT from HTTP-only cookie, and
compared its suggestion to my own implementation from a past project. It got the
overall structure right (checking req.cookies, verifying with jsonwebtoken), but
missed some edge case handling.

### Debugging with AI

For the debugging test, I gave Copilot Chat a bug I'd already fixed before,
without telling it the answer: an Express 5 wildcard route (app.get("*", ...)) that
broke in production after path-to-regexp was updated. Copilot immediately
identified the issue and explained the fix.

### Asking AI to explain a new concept

I asked ChatGPT and Copilot Chat to both explain idempotency keys, since it's
something I hadn't worked with directly. Both gave similar high-level
explanations, but ChatGPT's was more thorough with examples.
