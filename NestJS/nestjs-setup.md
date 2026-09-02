# Setup

## In default Setup

File structure: **src**

- app.controller.spec.ts
- app.controller.ts
- app.module.ts -app.service.ts
- main.ts

## Main.ts

TO create a Nest application instance, we use the coer `NestFactory` class.
`NestFactory` exposes a few static methods that allow you to create an
application instance.

In the `main.ts`, we start up our HTTP listener, which lets the application
await inbound HTTP requests.

## app module

The use of app module in here is to connect to all the modules in the project,
from imports, controller and providers.

It is the root module that acts as the primary entry point and orchestrator that
NestJS uses to build your entire application graph.

## scalability

Standalone applications in NestJs allows you to create a modular, isolated
services without a full application context. For example, you can spin up
lightweight scripts or specialized services without introducing the overhead of
a complete microservices setup.

It gave us the flexibility to scale specific tasks without overcomplicating
things.

By choosing NestJS’s standalone capability, we:

- Avoid unnecessary architectural overhauls.
- Kept the system clean and maintainable.
- Focused on solving the problem efficiently.
