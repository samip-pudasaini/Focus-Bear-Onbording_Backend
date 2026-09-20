# Tasks

## Research how NestJS handles configuration using @nestjs/config

Different environment require different configurations. Usually the local
environment relies on specific database credentials, valid only for the local DB
instance. The production environment would use a separate set of DB credentials.
Since config variables changes, best practice is to store configuration
variables in the environment, in an `.env` files.

Nest uses a built-in package `@nestjs/congfig` which creates a `ConfigModule`
that exposes a `ConfigService` which loads the appropriate `.env` file

Installation: `npm install @nestjs/config`

## Set up an .env file

Different environments require different configurations, for example,
development and production require different configurations

`.env.development`

```
NODE_ENV=development
PORT=3000
DATABASE_HOST=localhost
DATABASE_PORT=5431
DATABASE_USER=postgres
DATABASE_PASSWORD=development_password
DATABASE_NAME=nestjs_dev
```

`env.production`

```
NODE_ENV=production
PORT=8080
DATABASE_HOST=production-db.example.com
DATABASE_PORT=5432
DATABASE_USER=production_user
DATABASE_PASSWORD=production_secret
DATABASE_NAME=nestjs_production
```

In `app.module.ts`

```ts
ConfigModule.forRoot({
  envFilePath: ".env.development",
});
```

However, production secrets generally shouldn't be committed in .env.production
either. In a deployed application, production environment variables are commonly
supplied by the hosting platform, container environment, or a secrets manager.

Then adding `.env` to `.gitignore` so that the passwords/API keys are not
exposed.

## how to validate environment variables in NestJS

It is standard practice to throw an exception during application startup if
required environment variables haven't been provided or if they don't meet
certain validation rules. The @nestjs/config package enables two different ways
to do this:

A Standard Schema compatible schema passed through the validationSchema option.
Any library implementing the specification works - Zod, Valibot, ArkType, and
others. A custom validate() function which takes environment variables as an
input.

Using Zod `$ npm install --save zod`

Configuring app module

```ts
//app.module.ts

import { z } from "zod";

@Module({
  imports: [
    ConfigModule.forRoot({
      validationSchema: z.object({
        NODE_ENV: z
          .enum(["development", "production", "test", "provision"])
          .default("development"),
        PORT: z.coerce.number().default(3000),
      }),
      validationOptions: {
        libraryOptions: {
          // options specific to your validation library
        },
      },
    }),
  ],
})
export class AppModule {}
```

# Reflection

## How does @nestjs/config help manage environment variables?

The @nestjs/config package provides a structured way to manage configuration and
environment variables in a NestJS application. It can load variables from a .env
file and make them available through the ConfigService.

Instead of hard-coding values such as database credentials, ports, or API keys
directly in the source code, the application can retrieve them from the
environment.

For example:

`const databaseHost = this.configService.get<string>('DATABASE_HOST');`

This makes configuration easier to change between different environments without
modifying the application code.

## Why should secrets such as API keys and database passwords never be stored in source code?

Secrets should not be stored directly in source code because source code is
often committed to version control systems such as Git and shared with other
developers or hosted on platforms such as GitHub.

If a password or API key is committed, removing it from the latest version of
the file does not necessarily remove it from the Git history. Someone who has
access to the repository may still be able to retrieve the secret.

Using environment variables keeps sensitive configuration separate from the
application source code. The `.env` file can also be added to `.gitignore` so
that it is not accidentally committed.

For collaboration, a `.env.example` file can be used to show which variables are
required without containing the real secret values.

## How can you validate environment variables before the app starts?

NestJS allows environment variables to be validated when ConfigModule is
initialized.

For example, Zod can be used to define a validation schema:

For example, Zod can be used to define a validation schema:

```ts
validationSchema: z.object({
  NODE_ENV: z
    .enum(['development', 'production', 'test'])
    .default('development'),

  PORT: z.coerce.number().default(3000),

  DATABASE_HOST: z.string().min(1),
  DATABASE_PORT: z.coerce.number(),
  DATABASE_USER: z.string().min(1),
  DATABASE_PASSWORD: z.string().min(1),
  DATABASE_NAME: z.string().min(1),
}),
```

This means required variables must exist and must have the expected format.

For example, `PORT` must be a valid number and `DATABASE_PASSWORD` must not be
empty.

If validation fails, NestJS prevents the application from bootstrapping
successfully. This is useful because configuration errors are detected when the
application starts rather than causing unexpected errors later.

## How can you separate configuration for different environments?

Different environments can use different configuration values. For example,
development may use a local database while production uses a separate production
database.

Environment-specific files can be used, such as:

- `.env.development`
- `.env.test`
- `.env.production`

The appropriate file can be selected using the envFilePath option in
ConfigModule.

we do that in the app.module by adding code similar to the one below:

```ts
ConfigModule.forRoot({
  envFilePath: [".env.development.local", ".env.development"],
});
```

However, production secrets should not normally be committed to the repository.
Production deployments can provide sensitive environment variables through the
hosting platform, deployment environment, or a dedicated secrets-management
system.

Overall, separating configuration from source code makes the application easier
to configure, safer to share, and easier to deploy across development, testing,
and production environments.
