# NestJS CLI

The Nest CLI is a command-line interface tool that helps you to initialize,
develop, and maintain your Nest applications. It assists in multiple ways,
including scaffolding the project, serving it in development mode, and building
and bundling the application for production distribution. It embodies
best-practice architectural patterns to encourage well-structured apps.

The NestJS CLI speeds up development by automating project setup, code
generation, building, and common development tasks, allowing developers to focus
more on application logic.

## nest generate

nest generate is the NestJS CLI command used to automatically create boiler
plate files

`nest generate <schematic> <name>` `nest generate controller test-generate-cont`

Result:

```TS
//test-generate-cont.controller.spec.ts

import { Test, TestingModule } from '@nestjs/testing';
import { TestGenerateContController } from './test-generate-cont.controller';

describe('TestGenerateContController', () => {
  let controller: TestGenerateContController;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      controllers: [TestGenerateContController],
    }).compile();

    controller = module.get<TestGenerateContController>(TestGenerateContController);
  });

  it('should be defined', () => {
    expect(controller).toBeDefined();
  });
});

```

```TS
//test-generate-cont.controller.ts

import { Controller } from '@nestjs/common';

@Controller('test-generate-cont')
export class TestGenerateContController {}

```

## nest build

It compiles your NestJS application so it can be run as a production-ready
application.

`nest build`

It compiles your source code, converts TS to JS if you're using TS, places the
compiled output in the dist, directory by default adn checks for compilation
errors.

```
src/
├── main.ts
├── app.module.ts
└── cats/
    └── cats.service.ts

        ↓ nest build

dist/
├── main.js
├── app.module.js
└── cats/
    └── cats.service.js
```

## How using CLI ensure consistency across the codebase

THe NestJS CLI ensures consistency by generating files using standard NestJS
conventions and predefined templates

The CLI automatically creates the files with the expected structure, naming
conventions, and decorators.

This means:

- Consistent file structure across the project.
- Consistent naming conventions for files and classes.
- Standard boilerplate code is generated automatically.
- Fewer manual errors when creating components.
- Easier collaboration, since developers follow the same patterns.

## How cli support modular architecture

The NestJS CLI helps maintain modular architecture by making it easy to create
and organise the different components of a module.

```
nest g module cats
nest g controller cats
nest g service cats
```

This creates

```
cats/
├── cats.module.js
├── cats.controller.js
└── cats.service.js
```

This helps because: modules are easy to create, related components can be
grouped together, consistent project structure, less manual setup and separation
of concerns.
