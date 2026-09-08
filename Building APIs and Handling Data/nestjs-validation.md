# Pipes

A pipe is a class annoted with the `@Injectable()` decorator, which implements
the `PipeTransform` interface.

Pipes in NestJS are functions or classes that intercept data as it flows through
the request lifecycle. They allow you to perform various operations on incoming
data, such as validation, transformation, and error handling, before it reaches
your route handlers or controllers. Pipes are a fundamental building block of
NestJS applications and are used to enforce consistency, reliability, and
security in your API endpoints.

Two use cases:

- transformation: transform data to a desired form
- validation: evaluate input data and if valid, simply pass it through
  unchanged; otherwise, throw an exception

Pipes operate as `arguments` being processed by controller route handler. Nest
puts a pipe just before a method is invoked, so it gets the arguments first and
operates on them. Any transformation or validation operation takes place at that
time, after which the route handler is invoked with any potentially transformed
arguments.

## Applying Pipes

### Parameter level:

```ts
@Get(':id')
   getOne(@Param('id', ParseIntPipe) id: number) {
     // Controller logic
   }

```

### Route Level:

```ts
@UsePipes(MyCustomPipe)
@Controller("items")
export class ItemsController {
  // Controller methods
}
```

### Global level:

```ts
@UsePipes(MyCustomPipe)
@Controller("items")
export class ItemsController {
  // Controller methods
}
```

## Built-in-pipes

Nest comes with several pipes available out-of-the-box:

- ValidationPipe
- StandardSchemaValidationPipe
- ParseIntPipe
- ParseFloatPipe
- PrseBoolPipe
- ParseArrayPipe
- ParseUUIDPipe
- ParseEnumPipe
- DefaultValuePipe
- ParseFilePipe
- ParseDatePipe

### ParseIntPipe

Imagine an API endpoint that anticipates an ID as a parameter in the URL. The ID
requires conversion into a numeric format for processing.

```TS
@Get(':id')
async findOne(@Param('id', ParseIntPipe) id: number) {
  return this.catsService.findOne(id);
}
```

### ValidationPipe

Consider a scenario where user input validation is imperative for creating a new
item. Employ the `ValidationPipe` to automatically validate incoming data based
on a DTO class furnished with validation decorators

```ts
@Post()
createItem(@Body(ValidationPipe) createItemDto: CreateItemDto) {
  // 'createItemDto' is validated as per DTO rules
  // Controller logic
}
```

## custom pipes and built-in pipes

| Built-in Pipes             | Custom Pipes                           |
| -------------------------- | -------------------------------------- |
| Already provided by NestJS | Created by you                         |
| Handle common operations   | Handle application-specific operations |
| `ParseIntPipe`             | `UppercasePipe`                        |
| `ParseUUIDPipe`            | `ValidateUsernamePipe`                 |
| `ValidationPipe`           | `TransformDatePipe`                    |
| No need to implement them  | You implement `PipeTransform`          |

Built-in pipes are ready-made tools provided by NestJS, while custom pipes let
you define your own validation or transformation logic. The important concept is
that both work at the same point in the request lifecycle: they process the
controller's arguments before the route handler executes.

## Custom DTO and class validator

Class-validator library allows you to use decorator-based validation.
Decorator-based validation is extremely powerful, especially when combined with
Nest's Pipe capabilities since we have access to the `metatype` of the processed
property. Before we start, we need to install the requiered packages

`$ npm i --save class-validator class-transformer`

```ts
// create-cat.dto.ts

import { IsString, IsInt } from "class-validator";

export class CreateCatDto {
  @IsString()
  name: string;

  @IsInt()
  age: number;

  @IsString()
  breed: string;
}
```

Here `@IsString` ensures name and breed is string while `@IsInt()` ensures age
is an integer.

The DTO can then be used in a controller:

```ts
@Post()
create(@Body() createCatDto: CreateCatDto){
    return this.catsService.create(createCatDto);
}
```

With a global ValidationPipe, NestJS will validate the incoming request against
these rules before calling the controller method.

```ts
import { ValidationPipe } from "@nestjs/common";

app.useGlobalPipes(
  new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
  }),
);
```