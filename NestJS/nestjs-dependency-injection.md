# Dependency Injection (DI)

Dependency injection is a IoC technique wherein you delegate instantiation of
dependencies to the IoC container, instead of doing it in your own code
imperatively.

First, we define a provider. The `@Injectable()` decorator marks a class as a
provider

Example

```JS
//cats.service.js
import { Injectable } from '@nestjs/common';

@Injectable()
export class CatsService {
  constructor() {
    this.cats = [];
  }

  findAll() {
    return this.cats;
  }
}

```

Then we request that Nest inject the provider into our controller class

```JS
// cats.controller.js
import { Controller, Get, Bind, Dependencies } from '@nestjs/common';
import { CatsService } from './cats.service.js';

@Controller('cats')
@Dependencies(CatsService)
export class CatsController {
  constructor(catsService) {
    this.catsService = catsService;
  }

  @Get()
  async findAll() {
    return this.catsService.findAll();
  }
}
```

Finally, we register the provider with the Nest IoC container:

```JS
// app.module.js

import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller.js';
import { CatsService } from './cats/cats.service.js';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class AppModule {}
```

SO, in `cots.service.js`, the `@Injectable()` decorator declares the
`CatsService` class as a class that can be managed by the Nest IoC container. In
`cats.controller.js`, `CatsContoller` declares a dependency on the `CatsService`
token with constructor injecton:

```JS
constructor(catsService) {
    this.catsService = catsService;
  }
```

In `app.module.ts`, we associate the token `CatsService` with the class
`CatsService` from the cats.service.ts

In short, `@injectable()` makes it such that the class can become a provider,
i.e. it can be used as a dependency for other classes and injected onto it.

# Provider scopes

| SINGLETON | A single instance of the provider is shared across the entire
application. The instance lifetime is tied directly to the application
lifecycle. Once the application has bootstrapped, all singleton providers have
been instantiated. Singleton scope is used by default | | REQUEST | A new
instance of the provider is created exclusively for each incoming request. The
instance is garbage-collected after the request has completed processing | |
TRANSIENT | Transient providers are not shared across consumers. Each consumer
that injects a transient provider will receive a new, dedicated instance |

## Usage

Specify injection scope by passing the `scope` property to the `@Injectable()`
decorator option object:

```JS
import {Injectable, Scope} from '@nestjs/common';

@Injectable({ scope: Scope.REQUEST })
export class CatsService{}
```

The above one will only be used for incoming request, and each of them will be
created exclusively

For custom providers

```
{
  provide: 'CACHE_MANAGER',
  useClass: CacheManager,
  scope: Scope.TRANSIENT,
}
```

This will inject a provider with a new, dedicated instance and are not shared
across consumers.

# How does NestJS automatically resolve dependencies?

When you mark a class with `@Injectable()`, behind the scene:
- Registers the class in its internal Dependency Injection container.
- Scans constructor parameters to find out what dependencies the class need.
- Resolves those dependencies - either creating or reusing instances
- Injects them automatically when your class is instantiated.
- Manages the lifecycle of each dependency (singleton by default)

NESTJS:
- Recognizes the `class` is needed
- Looks it up in its provider list
- Instantiates it (if it hasn't already)
- Passes it into the constructor