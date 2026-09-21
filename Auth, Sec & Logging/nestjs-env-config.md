# Tasks

## Research how NestJS handles configuration using @nestjs/config

Different environment require different configurations. Usually the local
environment relies on specific database credentials, valid only for the local DB
instance. The production environment would use a separate set of DB credentials.
Since config variables changes, best practice is to store configuration
variables in the environment, in an `.env` files.

Nest uses a built-in package `@nestjs/config` which creates a `ConfigModule`
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

`.env.production`

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

## How to validate environment variables in NestJS

It is standard practice to throw an exception during application startup if
required environment variables haven't been provided or if they don't meet
certain validation rules. The `@nestjs/config` package enables two ways to do
this:

1. A compatible schema passed through the `validationSchema` option. Any library
   implementing the specification works: Zod, Valibot, ArkType, and others.
2. A custom `validate()` function which takes the environment variables as
   input.

> **Version note:** Standard Schema support in `validationSchema` requires
> `@nestjs/config` 12 or later. On older versions, `validationSchema` only works
> with Joi, so use the `validate()` function instead.

### Option 1: Zod with `validationSchema`

Install Zod:

```bash
npm install --save zod
```

Configure the app module:

```ts
// app.module.ts
import { Module } from "@nestjs/common";
import { ConfigModule } from "@nestjs/config";
import { z } from "zod";

@Module({
  imports: [
    ConfigModule.forRoot({
      envFilePath: ".env.production",
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

`envFilePath` points to an `.env.production` file. Keep `.env` files out of
version control by listing them in `.gitignore`.

### Option 2: Zod with a `validate` function

This works on any `@nestjs/config` version:

```ts
// env.validation.ts
import { z } from "zod";

const envSchema = z.object({
  NODE_ENV: z
    .enum(["development", "production", "test", "provision"])
    .default("development"),
  PORT: z.coerce.number().default(3000),
});

export function validate(config: Record<string, unknown>) {
  return envSchema.parse(config); // throws on startup if invalid
}
```

```ts
// app.module.ts
import { Module } from "@nestjs/common";
import { ConfigModule } from "@nestjs/config";
import { validate } from "./env.validation";

@Module({
  imports: [ConfigModule.forRoot({ validate })],
})
export class AppModule {}
```

### Reading values with `ConfigService.get()`

```ts
import { Injectable } from "@nestjs/common";
import { ConfigService } from "@nestjs/config";

@Injectable()
export class AppService {
  constructor(private configService: ConfigService) {}

  getPort(): number {
    return this.configService.get<number>("PORT");
  }
}
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

NestJS validates environment variables when `ConfigModule` is initialized. If a
required variable is missing or invalid, the app throws an error and fails to
start.

With `@nestjs/config` 12 or later, you can pass a Zod schema to
`validationSchema`:

```ts
// app.module.ts
import { Module } from "@nestjs/common";
import { ConfigModule } from "@nestjs/config";
import { z } from "zod";

@Module({
  imports: [
    ConfigModule.forRoot({
      validationSchema: z.object({
        NODE_ENV: z
          .enum(["development", "production", "test"])
          .default("development"),
        PORT: z.coerce.number().default(3000),

        DATABASE_HOST: z.string().min(1),
        DATABASE_PORT: z.coerce.number(),
        DATABASE_USER: z.string().min(1),
        DATABASE_PASSWORD: z.string().min(1),
        DATABASE_NAME: z.string().min(1),
      }),
    }),
  ],
})
export class AppModule {}
```

This means required variables must exist and have the expected format. For
example, `PORT` must be a valid number and `DATABASE_PASSWORD` must not be
empty. `z.coerce.number()` is needed because environment variables are always
strings.

On older versions of `@nestjs/config`, `validationSchema` only works with Joi.
For Zod there, use a custom `validate()` function:

```ts
ConfigModule.forRoot({
  validate: (config) => envSchema.parse(config),
});
```

Once validated, values are read with `ConfigService.get()`:

```ts
const port = this.configService.get<number>("PORT");
```

Validating environment variables at startup means a misconfigured app fails
immediately with a clear error. Without it, a missing `DATABASE_PASSWORD` or a
non-numeric `PORT` could cause a confusing bug later, often in production.

Zod lets me define defaults, required values and types in one schema. Passing it
to `validationSchema` needs `@nestjs/config` 12 or later, while a `validate()`
function works on any version. The main lessons are to validate configuration
before the app serves traffic, to keep secrets in `.env` files listed in
`.gitignore`, and to check the library version before following an approach.

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

## Resubmission Reflection

With `@nestjs/config` 12 or later, you can pass a Zod schema to
`validationSchema`, while in older versions of `@nestjs/config`,
`validationSchema` only works with Joi. For Zod, you need to use a custom
`validate()` function.

Previously only the newer version feature was included, now older version option
was included as well.
