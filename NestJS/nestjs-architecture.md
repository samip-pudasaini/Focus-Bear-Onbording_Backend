# Modules

A module is a class that is annoted with the `@Module` decorator. This decorator
provides metadata that Net uses to organize and manage the application structure
efficiently.

The module encapsulates providers by default, meaning you can only inject
providers that are either part of the current module or explicitly exported from
other imported modules. The exported providers from a module essentially serve
as the module's public interface or API.

# providers

The key idea behind a provider is that it can be injected as a dependency,
allowing objects to form various relationships with each other. The
responsibility of "wiring up" these objects is largely handled by the Nest
runtime system.

Providers are plain JavaScript classes declared as `providers` in a NestJS
module. Controllers should handle HTTP requests and delegate more complex tasks
to `providers`.

# controllers

Controllers are responsible for handling incoming requests and sending responses
back to the client.

A controller's purpose is to handle specific requests for the application. The
routing mechanism determines which controller will handle each request. Often, a
controller has multiple routes, and each route can perform a different action.

To create a basic controller, we use classes and decorators.Decorators link
classes witht teh necessary metadata, allowing Nest to create a routing map that
connects requests to their corresponding controllers.

# WHy DI is useful in NestJS

Using dependency injection you allow your dependency to be easily swapped with
other implementation. This is really helpful when you want to reuse your module.
For example, if your module sends notifications, you can have
NotificationService dependency, which has just one method in the interface -
notify. Then you can pass in different implementations in different places
(maybe even different apps) - email, sms, push, slack, etc. Also, it's very easy
to pass in mock, in unit tests.

# modularity

It has clear separation of responsibilities

src/
├── users/
│   ├── users.module.ts
│   ├── users.controller.ts
│   └── users.service.ts
├── auth/
│   ├── auth.module.ts
│   ├── auth.controller.ts
│   └── auth.service.ts
└── app.module.ts

| Component                     | Responsibility                  |
| ----------------------------- | ------------------------------- |
| **Module**                    | Organises related functionality |
| **Controller**                | Handles incoming requests       |
| **Service**                   | Contains business logic         |
| **Provider**                  | Provides reusable dependencies  |
| **Repository/Database layer** | Handles data access             |

In short: NestJS promotes modulartity by organising application into independent
feature modules, while separatioin of concerns is achieved by keeping HTTP
handling, business logic, and data access in separate components. Its dependency
injection system then connects these components without tightly coupling them.
