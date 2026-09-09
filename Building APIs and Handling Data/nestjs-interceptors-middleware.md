# Middleware

In the context fo web frameworks, middleware functions are functions that have
access to the request(req) and response(res) objects. They provide a way to
execute code before the final request handler is invoked. Middleware can perform
various tasks such as logging, authentication, validation, error handling, and
more.

## Applying middleware

```ts
import { Injectible, NestMiddleware } from "@nestjs/common";
import { Request, Response, NextFuncton } from "express";

@Injecteble()
export class LoggerMiddleWare implements NestMiddleWare {
  use(req: Request, res: Response, next: NextFunction) {
    console.log("Request...");
    next();
  }
}
```

```ts
import { Module, NestModule, MiddlewareConcumer } from "@nestjs/common";
import { LoggerMiddleware } from "./common/middleware/logger.middleware";
import { CatsModule } from "./cats/cats.module";

@Module({
  imports: [CatsModule],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(loggerMiddleware)
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

## Difference from Middleware

Interceptors have access to response/request before and after the route handler
is called.

Middleware is called only before the route handler is called.

### when to use

You would use an interceptor when you want to have access to the
response/request at both time before and after the route handler is called.

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
