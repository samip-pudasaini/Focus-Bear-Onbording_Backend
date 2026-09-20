# Tasks

Nest comes with a built-in text-based logger which is used during application
bootstrapping and several other circumstances such as displaying caught
exceptions (i.e., system logging). This functionality is provided via the Logger
class in the @nestjs/common package.

You can also create your own custom implementation, to log your own
application-level events and messages.

If your application requires integration with external logging systems,
automatic file-based logging, or forwarding logs to a centralized logging
service, you can implement a fully custom logging solution using a Node.js
logging library. One popular choice is Pino, known for its high performance and
flexibility.

## Implementing pino

Installation: `$ npm install nestjs-pino`

**ADD:**

In main:

```ts
import { Logger } from "nestjs-pino";

async function bootstrap() {
  const app = await NestFactory.create(MyModule, {
    bufferLogs: true,
  });

  app.useLogger(app.get(Logger));

  await app.listen(3000);
}

app.useLogger(app.get(Logger));
```

Controller:

```ts
import { NestFactory } from "@nestjs/core";
import { Controller, Get, Module } from "@nestjs/common";
import { LoggerModule, Logger } from "nestjs-pino";

@Controller()
export class AppController {
  constructor(private readonly logger: Logger) {}

  @Get()
  getHello() {
    this.logger.log("something");
    return `Hello world`;
  }
}

@Module({
  controllers: [AppController],
  imports: [LoggerModule.forRoot()],
})
class MyModule {}

async function bootstrap() {
  const app = await NestFactory.create(MyModule);
  await app.listen(3000);
}
bootstrap();
```

Service:

```ts
import { Injectable } from "@nestjs/common";
import { Logger } from "nestjs-pino";

@Injectable()
export class UsersService {
  constructor(private readonly logger: Logger) {}

  createUser() {
    this.logger.log("Creating a new user");

    return {
      message: "User created successfully",
    };
  }
}
```

In app module

```ts
import { Module } from "@nestjs/common";
import { LoggerModule } from "nestjs-pino";
import { UsersController } from "./users.controller";
import { UsersService } from "./users.service";

@Module({
  imports: [LoggerModule.forRoot()],
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}
```

When you run it and visit: http://localhost:3000/

we'll get two kinds of output

One for browser and for the logs, nestjs-pino automatically logs the HTTP
request. You should see something roughly like:

```json
{
  "level": 30,
  "time": 1758380000000,
  "pid": 12345,
  "hostname": "YOUR-PC",
  "req": {
    "method": "GET",
    "url": "/"
  },
  "res": {
    "statusCode": 200
  },
  "responseTime": 5
}
```

we'll also get the message from `this.logger.log("something");`.

## Implementing a custom handling in NestJS

```ts
import {
  ArgumentsHost,
  Catch,
  ExceptionFilter,
  HttpException,
  HttpStatus,
} from "@nestjs/common";

@Catch()
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const context = host.switchToHttp();
    const response = context.getResponse();
    const request = context.getRequest();

    let status = HttpStatus.INTERNAL_SERVER_ERROR;
    let message = "Internal server error";

    if (exception instanceof HttpException) {
      status = exception.getStatus();

      const exceptionResponse = exception.getResponse();

      if (typeof exceptionResponse === "string") {
        message = exceptionResponse;
      } else if (
        typeof exceptionResponse === "object" &&
        exceptionResponse !== null &&
        "message" in exceptionResponse
      ) {
        message = (exceptionResponse as { message: string | string[] }).message;
      }
    }

    response.status(status).json({
      statusCode: status,
      message,
      path: request.url,
      timestamp: new Date().toISOString(),
    });
  }
}
```

`@Catch()` tells NestJS that this filter should catch exceptions.
`const context = host.switchToHttp();` gets the HTTP context.
`context.getResponse(); context.getRequest();`provides access to Express
response and request objects.

`if (exception instanceof HttpException)` checks whether the error is a NestJS
`HTTPException`. If it is, we get its HTTP status
`status = exception.getStatus();`

When you deliberately throw an exception in the controller:

```ts
import { Controller, Get, NotFoundException } from "@nestjs/common";

@Controller()
export class AppController {
  @Get("error")
  testError() {
    throw new NotFoundException("User was not found");
  }
}
```

and start the application. Using the Get request on bruno with `GET
http://localhost:3000/error.

Our response will be:

```JSON
{
  "statusCode": 404,
  "message": "User was not found",
  "path": "/error",
  "timestamp": "...",
  ///
}
```

# Reflection

## What are the benefits of using `nestjs-pino` for logging?

`nestjs-pino` provides a structured and efficient logging system for a NestJS
application. It integrates Pino with NestJS and can automatically log HTTP
requests and responses.

One benefit of structured logging is that information such as the request
method, URL, status code, and response time can be stored as separate fields
instead of being part of one plain-text message. This makes logs easier to
search, filter, and analyse.

It also allows application code to use NestJS's `Logger` while Pino handles the
underlying logging. This provides a consistent logging approach throughout the
application.

## How does global exception handling improve API consistency?

Global exception handling allows errors from different controllers and services
to be handled in a consistent way.

A custom exception filter can format errors using the same structure, for
example:

```json
{
  "statusCode": 404,
  "message": "User was not found",
  "path": "/users/123",
  "timestamp": "2026-09-21T..."
}
```

This makes it easier for clients consuming the API because they can expect
errors to contain the same types of information regardless of which endpoint
generated the error.

It also avoids having to implement separate error formatting logic in every
controller.

## What is the difference between a logging interceptor and an exception filter?

A logging interceptor and an exception filter have different responsibilities.

A logging interceptor operates around the request and response lifecycle. It can
be used to log information such as the incoming request, response status, and
how long the request took.

An exception filter is specifically responsible for handling exceptions. It can
catch an exception and return a consistent HTTP error response to the client.

For example, the logging interceptor might record that a request to `/users`
returned a `404` response, while the exception filter determines the format of
that `404` response.

Therefore, the interceptor is mainly concerned with observing and logging the
request/response process, while the exception filter is concerned with handling
and formatting errors.

## How can logs be structured to provide useful debugging information?

Logs should contain enough contextual information to understand what happened
during a request.

Useful information can include:

- Timestamp
- Log level
- HTTP method
- Request URL
- HTTP status code
- Response time
- Controller or service name
- Request or correlation ID
- Error message
- Stack trace when an error occurs

For example:

```json
{
  "level": 30,
  "method": "POST",
  "path": "/users",
  "statusCode": 201,
  "responseTime": 45,
  "context": "UsersController",
  "message": "User created successfully"
}
```

Structured logs are more useful for debugging because individual fields can be
searched and filtered.

Sensitive information such as passwords, API keys, authentication tokens, and
other secrets should not be included in logs.

Overall, `nestjs-pino` provides structured logging for monitoring and debugging,
while a global exception filter provides a consistent format for API errors.
Using both makes it easier to understand what is happening inside the
application and gives API clients predictable error responses.
