# TypeORM Integration

Nest provides the @nestjs/typeorm package. TypeORM is the most mature Object
Relational Mapper (ORM) available for TypeScript. Since it's written in
TypeScript, it integrates well with the Nest framework.

For a NestJS application using PostgreSQL, TypeORM acts as the ORM that maps
TypeScript classes to database tables. NestJS provides the @nestjs/typeorm
package, which integrates TypeORM into Nest's dependency-injection and module
system. This gives you features such as repository injection with
@InjectRepository().

```
NestJS
   │
   ├── Controller
   │      │
   │      ▼
   ├── Service
   │      │
   │      ▼
   ├── TypeORM Repository
   │      │
   │      ▼
   └── PostgreSQL
```

An Entity represents a database table, while a Repository provides methods for
interacting with that entity's rows.

# Installation

` npm install @nestjs/typeorm typeorm pg`

**Basic entity/repository setup**

==Configure PostgreSQL==

```ts
// app.module.ts

import { Module } from "@nestjs/common";
import { TypeOrmModule } from "@nestjs/typeorm";
import { UsersModule } from "./users/users.module";

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: "postgres",
      host: "localhost",
      port: 5431,
      username: "postgres",
      password: "mysecretpassword",
      database: "postgres",
      autoLoadEntities: true,
      synchronize: true,
    }),
    UsersModule,
  ],
})
export class AppModule {}
```

==Create the entity==

```ts
// user.entity.ts

import { Entity, PrimaryGeneratedColumn, Column } from "typeorm";

@Entity("users")
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column()
  email: string;
}
```

==Register the entity with the module==

```ts
//users.module.ts

import { Module } from "@nestjs/common";
import { TypeOrmModule } from "@nestjs/typeorm";
import { User } from "./entities/user.entity";
import { UsersService } from "./users.service";

@Module({
  imports: [TypeOrmModule.forFeature([User])],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```

forFeature([User]) tells Nest/TypeORM that this module needs the repository for
User.

==Inject the repository into the service==

```ts
import { Injectable } from "@nestjs/common";
import { InjectRepository } from "@nestjs/typeorm";
import { Repository } from "typeorm";
import { User } from "./entities/user.entity";

@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
  ) {}

  findAll() {
    return this.userRepository.find();
  }

  findOne(id: number) {
    return this.userRepository.findOneBy({ id });
  }

  create(name: string, email: string) {
    const user = this.userRepository.create({
      name,
      email,
    });

    return this.userRepository.save(user);
  }

  async remove(id: number) {
    await this.userRepository.delete(id);
  }
}
```

==The flow==

```
Controller
    ↓
UsersService
    ↓
User Repository
    ↓
TypeORM
    ↓
PostgreSQL
```

# Crud functionality

```ts
import {Injectable} from '@nestjs/common';
import {InjectRepository} from '@nestjs/typeorm';
import {Repository} from 'typeorm';
import {User} from './entities/user.entity';

@Injectable()
export class UsersService{
  constructor(
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
  ){}

  //CREATE
  create(name: string, email: string)
  {
    conts user = this.userRepository.create({
      name, email,
    });

    return this.userRepository.save(user);
  }

  //READ
  findAll()
  {
    return this.userRepository.find();
  }

  findOne(id: number)
  {
    return this.userRepository.findOneBy({id});
  }

  //UPDATE
  update(id: number, name: string, email: string)
  {
    await this.userRepostory.update(id, {
      name,
      email,
    });

    return this.userRepository.findOneBy({id});
  }

  //DELETE
  remove(id: number)
  {
    return this.userRepository.delete(id);
  }
}
```

**Main TypeORM methods** | CRUD | TypeORM | SQL | | ---------- |
----------------------- | -------- | | **Create** | `create()` + `save()` |
`INSERT` | | **Read** | `find()`, `findOneBy()` | `SELECT` | | **Update** |
`update()` | `UPDATE` | | **Delete** | `delete()` | `DELETE` |

# Reflection

## 1. How does `@nestjs/typeorm` simplify database interactions?

`@nestjs/typeorm` makes database interactions easier by integrating TypeORM with
NestJS's dependency injection system. It allows repositories to be injected
directly into services, so I can perform operations such as `find()`, `save()`,
`update()`, and `delete()` without manually managing database connections or
writing SQL for every operation.

## 2. What is the difference between an entity and a repository in TypeORM?

An **entity** represents the structure of a database table, including its
columns and relationships. A **repository** is used to interact with that entity
and perform database operations such as creating, reading, updating, and
deleting records.

In simple terms, the entity defines **what the data looks like**, while the
repository provides **methods to work with the data**.

## 3. How does TypeORM handle migrations in a NestJS project?

TypeORM uses migrations to track and apply changes to the database schema. For
example, if I add a new column to an entity, I can create a migration that adds
the corresponding column to the database.

Migrations are useful because they make database changes **controlled,
repeatable, and version-controlled**, especially when deploying an application
to different environments.

### 4. What are the advantages of using PostgreSQL over other databases in a NestJS app?

PostgreSQL is a good choice for NestJS applications because it provides a
reliable relational database system with strong data integrity and transaction
support. It supports features such as relationships, foreign keys, indexes,
complex queries, and JSON data.

I found PostgreSQL particularly useful when working with structured data where
maintaining relationships and consistency between different tables is important.