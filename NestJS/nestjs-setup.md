# NestJS Setup

## Default Project Setup

I created a new NestJS project using the NestJS CLI. The commands I used were:

```bash
npm i -g @nestjs/cli
nest new nestjs-project
cd nestjs-project
npm run start:dev
```

The NestJS CLI installed the required project dependencies and generated the
default project structure. The main dependencies included NestJS, Express,
TypeScript, and the required testing packages.

The default project structure I received was:

```text
nestjs-project/
├── src/
│   ├── app.controller.spec.ts
│   ├── app.controller.ts
│   ├── app.module.ts
│   ├── app.service.ts
│   └── main.ts
├── test/
│   ├── app.e2e-spec.ts
│   └── jest-e2e.json
├── node_modules/
├── .prettierrc
├── eslint.config.mjs
├── nest-cli.json
├── package-lock.json
├── package.json
├── README.md
├── tsconfig.build.json
└── tsconfig.json
```

## main.ts

To create a NestJS application instance, we use the core `NestFactory` class.
`NestFactory` provides static methods that allow us to create an application
instance.

In `main.ts`, the application is created and the HTTP listener is started. This
allows the application to receive incoming HTTP requests.

## app.module.ts

The `AppModule` is the root module of the NestJS application. It connects the
different parts of the application through its `imports`, `controllers`, and
`providers`.

It acts as the primary entry point and orchestrator that NestJS uses to build
the application's dependency graph.

## Testing the Default Endpoint

I tested the default endpoint provided by NestJS by sending a GET request to:

```text
GET http://localhost:3000/
```

The response was:

```text
Hello World!
```

I also confirmed that the development server started successfully using:

```bash
npm run start:dev
```

The application was available on `localhost:3000`.

## Scalability

NestJS provides a modular structure that makes applications easier to scale as
they become larger. Instead of putting all functionality into one large module,
features can be separated into their own modules, controllers, and providers.

For example, an application could have separate modules for users,
authentication, and products. Each module can contain the controllers and
services related to that feature while still being connected through the root
`AppModule`.

NestJS also supports standalone applications, which allow specific services or
scripts to run without requiring a complete HTTP application. This can be useful
for specialised tasks without introducing unnecessary architectural complexity.

By using NestJS's modular and standalone capabilities, we can:

- Keep different features separated and maintainable.
- Add new modules without significantly changing existing functionality.
- Reuse services through NestJS's dependency injection system.
- Scale specific parts of the application when necessary.
- Avoid unnecessary architectural changes as the project grows.

Overall, NestJS's module-based structure provides a clean foundation for
developing and scaling larger applications.
