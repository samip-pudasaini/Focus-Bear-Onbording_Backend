# Learning AI Coding Tools

Since Cursor has a free tier, while claude code terminal requires a plan. I went with using Cursor as my AI tool.

## Experimentation

### Understanding the code
When propted with the idea of understanding the existing code, the project structure and main entry points were showcared

RealTimeChat/
├── backend/     Express + Socket.io + MongoDB
├── frontend/    React + Vite + Zustand + Tailwind/DaisyUI
└── package.json Root scripts for build/start

Backend entry point and important codes, alongside descriptions:
- When a user connects, their userId (from the query string) maps to their socketId. That map drives:
  Online presence — broadcasts getOnlineUsers to everyone
  Direct message delivery — getReceiverSocketId() finds the recipient’s socket


Data Models:
| Model   | Fields                                        |
| User    | email, fullName, password, profilePic         |
| Message | senderId, receiverId, text, image (optional)  |


Alongside flow diagrams
![alt text](<Screenshot 2026-08-18 124154.png>)


### Writing Documentation
The documentation process was really useful, It had a really brief method of showcasing the documentation of the project I presented to it.

From features to techstacks used, the brief structure, prerequisites, how to get started with the project locally, what dependencies are required to be installed, the architecture and its flow

## Observations
Cursor was particularly good at understanding existing code and identifying the structure and purpose of different parts of the project. It quickly explained the project architecture, important files, data models, and how the backend and frontend communicated with each other. It was also very effective at writing documentation, including the project features, technology stack, setup instructions, dependencies, and overall system flow. I found it especially useful because it could analyse multiple parts of the project and present the information in a clear and organised way, saving a significant amount of time.

### What Cursor Struggled With
Although Cursor was very effective at understanding the project and generating documentation, it was not always perfect. At times, it made assumptions about how parts of the code worked without completely verifying the implementation. Some explanations were also more detailed than necessary when I was looking for a simple answer. I also found that AI-generated documentation still needs to be checked by the developer, as it can describe what the code is intended to do rather than what it actually does. This showed me that AI coding tools are useful for speeding up development and understanding unfamiliar code, but their output should still be reviewed and verified.
