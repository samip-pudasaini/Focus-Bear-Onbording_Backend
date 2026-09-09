# Middleware

In the context fo web frameworks, middleware functions are functions that have
access to the request(req) and response(res) objects. They provide a way to
execute code before the final request handler is invoked. Middleware can perform
various tasks such as logging, authentication, validation, error handling, and
more.

## Applying middleware

```ts
import { Injectable, NestMiddleware } from "@nestjs/common";
import { Request, Response, NextFunction } from "express";

@Injectable()
export class LoggerMiddleWare implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log("Request...");
    next();
  }
}
```

```ts
import {
  Module,
  NestModule,
  MiddlewareConsumer,
  RequestMethod,
} from "@nestjs/common";
import { LoggerMiddleWare } from "./common/middleware/logger.middleware";
import { CatsModule } from "./cats/cats.module";

@Module({ imports: [CatsModule] })
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleWare)
      .forRoutes({ path: "cats", method: RequestMethod.GET });
  }
}
```

# Interceptors

Interceptors are the most powerful form of the request-response pipeline. They
have direct access to the request before hitting the route handler. We can
mutate the response after it has passed through the route handler.

Lets take a typical request-response between client and server, where client is
making a request to the Nest API endpoints. The server will process that request
and send back a response to the client. An interceptor is what lies between the
request and the response.

We create an interceptor so that when a client makes a request to the server, it
will be intercepted by the interceptor before the request reaches the server. In
the interceptor, we can do any process and modify the request before it's sent
to the server. We can also set up the interceptor to intercept the response
before being sent back to the client.

## Simple Logging Interceptor

```ts
//logging.interceptor.ts
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
} from "@nestjs/common";
import { Observable } from "rxjs";
import { tap } from "rxjs/operators";

@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();

        const { method, originalUrl, body } = request;
        const now = Date.now();

        console.log("Request:", {
            method,
            url: originalUrl,
            body,
        });

        return next.handle().pipe(
            tap((response) => 
            console.log("Response:", {
                method,
                url: originalUrl,
                body: response,
                duration: `${Date.now() - now}ms`,
            });
            ),
        );F
  }
}
```

## `ClassSerializerInterceptor`

The `ClassSerializerInterceptor` interceptor uses the powerful class-transformer
package to transform/serialize the objects returned by your controller.

Since interceptor controls the response flow, it uses the class-transformer to
transform the returned object before NestJS sents the HTTP response to the
client.

Example: Exclude properties

```ts
import { Exclude } from "class-transformer";

export class UserEntity {
  id: number;
  firstName: string;
  lastName: string;

  @Exclude()
  password: string;

  constructor(partial: Partial<UserEntity>) {
    Object.assign(this, partial);
  }
}
```

Now consider a controller with a method handler that returns an instance of this
class.

```ts
@UseInterceptors(ClassSerializerInterceptor)
@Get()
findOne(): UserEntity {
    return new UserEntity({
        id:1,
        firstName: 'John',
        lastName: 'Doe',
        password: 'password',
    });
}
```

When this endpoint is requested, the client receives the following response:

```json
{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe"
}
```

## `LoggerErrorInterceptor`

A custom interceptor used to catch and log errors that happen while processing a
request.

It works similarly to what you were just describing with
`ClassSerializerInterceptor`, but instead of modifying the response data, it
focuses on errors.

```
Client
  │
  │ Request
  ▼
Interceptor
  │
  ▼
Controller
  │
  ▼
Service
  │
  │ ❌ Error occurs
  ▼
Interceptor catches/logs error
  │
  ▼
Error continues through NestJS
  │
  ▼
Exception handling
  │
  ▼
Response to Client
```

Example:

```ts
@Injectable()
export class LoggerErrorInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler) {
    return next.handle().pipe(
      catchError((error) => {
        console.error("Error:", error);

        throw error;
      }),
    );
  }
}
```

So if your service does: `throw new Error('User not found');`, the interceptor
can log `Error: User not found` then it can `throw error;` passes the error
onward so NestJS can handle it normally.

In simple terms, Something went wrong while processing this request. Let me log
the error before NestJS handles it.

# Reflection

## What is the difference between interceptor and middleware?

Middleware runs before the route handler and primarily works with the request
and response objects. An interceptor wraps around the route handler, allowing it
to execute code both before and after the handler runs. This makes interceptors
more suitable for processing controller responses and handling operations around
the complete request-response cycle.

## When would you use an interceptor instead of middleware?

I would use an interceptor when I need to perform an operation after the
controller has executed, such as logging response data, transforming a response,
measuring execution time, or catching errors. Middleware would be more
appropriate when I only need to process the request before it reaches the
controller.

## How does LoggerErrorInterceptor help?

LoggerErrorInterceptor helps by catching errors that occur during request
processing and logging useful information about them. It then passes the error
back to NestJS so that NestJS's normal exception-handling system can generate
the appropriate response. This makes debugging easier without replacing the
framework's built-in error handling.
