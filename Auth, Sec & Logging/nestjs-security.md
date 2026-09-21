# Best Security Practices

## Common security risks in NestJS

Some common security risks in a NestJS backend include:

- Injection attacks: SQL injection, NoSQL injection, or command injection can
  occur when untrusted input is passed directly into queries or commands. Using
  validation, parameterized queries, and ORM/ODM features helps reduce this
  risk.
- CORS misconfiguration: Allowing every origin or using overly broad CORS
  settings can allow unwanted websites to interact with an API.
- Cross-Site Scripting (XSS): Occurs when untrusted data is returned to a
  browser without appropriate escaping or sanitisation.
- Authentication and authorization issues: APIs can be vulnerable if
  authentication is missing or if users can access resources they should not be
  allowed to access.
- Brute-force and denial-of-service attempts: Attackers can send a large number
  of requests to an endpoint. Rate limiting can restrict how frequently clients
  can make requests.
- Exposed secrets: API keys, database passwords, JWT secrets, and other
  credentials should not be hard-coded or committed to Git.
- Insecure HTTP headers: Missing security headers can leave browsers with fewer
  protections against common web attacks.

## How `@fastigy/helmet` helps secure HTTP headers

`@fastify/helmet` adds security-related HTTP headers to a Fastify application.
These headers provide browser-side protections against certain attacks and help
reduce the information an application exposes.

**Installation:** `npm install @fastify/helmet`

In `main.ts`

```ts
//main.ts

import { NestFactory } from "@nestjs/core";
import {
  FastifyAdapter,
  NestFastifyApplication,
} from "@nestjs/platform-fastify";
import helmet from "@fastify/helmet";
import { AppModule } from "./app.module";

async function bootstrap() {
  const app = await NestFactory.create<NestFastifyApplication>(
    AppModule,
    new FastifyAdapter(),
  );

  await app.register(helmet);

  await app.listen(3000, "0.0.0.0");
}

bootstrap();
```

Helmet can configure headers such as Content-Security-Policy,
X-Content-Type-Options, and other security-related headers.

## Implement request rate limiting using @fastify/rate-limit

Installation of the plugin:

```ts
//main.ts

import { NestFactory } from "@nestjs/core";
import {
  FastifyAdapter,
  NestFastifyApplication,
} from "@nestjs/platform-fastify";
import rateLimit from "@fastify/rate-limit";
import helmet from "@fastify/helmet";
import { AppModule } from "./app.module";

async function bootstrap() {
  const app = await NestFactory.create<NestFastifyApplication>(
    AppModule,
    new FastifyAdapter(),
  );

  await app.register(helmet);

  await app.register(rateLimit, {
    max: 100,
    timeWindow: "1 minute",
  });

  await app.listen(3000, "0.0.0.0");
}

bootstrap();
```

The following code:
`await app.register(rateLimit, { max: 100, timeWindow: "1 minute",});` allows a
client to make up to 100 requests per minute before requests are rate limited.

The exact limit should depend on the application. Authentication endpoints, for
example, may need stricter limits than normal API endpoints.

## How to securely handle API keys and environment variables

Sensitive values should not be hard coded: `const apiKey = 'my-secret-api-key';`

Instead, they should be stored in an environment file during development

```
API_KEY=my-secret-api-key
DATABASE_PASSWORD=my-database-password
JWT_SECRET=my-secret
```

The `.env` environment file should be included in `.gitignore`:

```
.env
.env.*
!.env.example
```

For production, secrets should preferably be supplied through the hosting
platform's environment-variable system or a dedicated secrets manager rather
than committing them to the repository.

Also, environment variables should be validated when the application starts so
that missing or invalid configuration is detected early.

# Reflection

## What are the most common security vulnerabilities in a NestJS backend?

Common security vulnerabilities include injection attacks, incorrectly
configured CORS, authentication and authorization problems, XSS, brute-force
attacks, and exposed secrets. Injection attacks can happen when user input is
used unsafely in database queries or system commands. CORS can also become a
security issue when an API allows requests from more origins than necessary.
Authentication and authorization must also be implemented correctly so that
users cannot access resources they are not permitted to use.

## How does `@fastify/helmet` improve application security?

`@fastify/helmet` improves security by adding and configuring security-related
HTTP headers in a Fastify application. These headers provide browsers with
additional protections against certain attacks. For example, Helmet can
configure headers related to content security, MIME-type sniffing, and other
browser security behaviours. This provides an additional layer of protection
without requiring each security header to be configured manually.

## Why is rate limiting important for preventing abuse?

Rate limiting controls how many requests a client can make within a specific
period. Without rate limiting, an attacker could repeatedly send requests to an
API, potentially causing excessive resource usage or attempting brute-force
attacks. Using `@fastify/rate-limit` allows the application to restrict request
frequency. In my implementation, I configured a limit of 100 requests per
minute. Different endpoints may require different limits depending on their
purpose and sensitivity.

## How can sensitive configuration values be protected in a production environment?

Sensitive values such as API keys, database passwords, and JWT secrets should
not be hard-coded in source code or committed to Git. During development, they
can be stored in environment variables and excluded from version control using
`.gitignore`. In production, secrets should be provided through environment
variables or a dedicated secrets-management service provided by the hosting
platform. Configuration should also be validated when the application starts so
that missing or invalid secrets are detected before the application is used.
