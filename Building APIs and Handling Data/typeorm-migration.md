Research how migrations work in TypeORM (typeorm migration:generate)

Create a new migration and apply it to the database

Seed sample data into PostgreSQL using TypeORM repositories

Explore how migrations can be used to roll back database changes ✅ Reflection
(typeorm-migrations.md) What is the purpose of database migrations in TypeORM?
How do migrations differ from seeding? Why is it important to version-control
database schema changes? How can you roll back a migration if an issue occurs?

# Tasks

## Migration in TypeORM

`typeorm migration: generate` in the TypeORM CLI command used to automatically
create a migration based on changes between your entities and the current
database schema.

`typeorm migration:generate <migration-name> -d <data-source>`

When you initiate this; the resulting is

```ts
@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;
}
```

## Create a new migration and apply it to the database

Lets take the initial:

```ts
import { Entity, Column, PrimaryGeneratedColumn } from "typeorm";

@Entity()
export class Post {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @Column()
  text: string;
}
```

Now change: `ALTER TABLE "post" RENAME COLUMN "title" TO "name";`

Before working wiht migrations you need to setup your DataSource options
properly

```ts
export default new DataSource({
  // basic setup
  synchronize: false,
  migrations: [__dirname + "/migrations/**/*{.js,.ts}"],

  // optional
  migrationsRun: false,
  migrationsTableName: "migrations",
  migrationsTransactionMode: "all",

  // other options...
});
```

**synchronise**: Turning off automatic schema synchronisation is essential for
working with migrations. Otherwise they would make no sense.

**migrations**: Defines list of migrations that need to be loaded by TypeORM. It
accepts both migration classes and directories from which to load.

**Manual creation**
`npx typeorm migration:create src/db/migrations/post-refactoring`

Inside migration file:

```ts
import { MigrationInterface, QueryRunner } from "typeorm";

export class PostRefactoringTIMESTAMP implements MigrationInterface {
  async up(queryRunner: QueryRunner): Promise<void> {}

  async down(queryRunner: QueryRunner): Promise<void> {}
}
```

With the Post changes:

```ts
import { MigrationInterface, QueryRunner } from "typeorm";

export class PostRefactoringTIMESTAMP implements MigrationInterface {
  async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(
      `ALTER TABLE "post" RENAME COLUMN "title" TO "name"`,
    );
  }

  async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(
      `ALTER TABLE "post" RENAME COLUMN "name" TO "title"`,
    ); // reverts things made in "up" method
  }
}
```

**Applying the migration** `npx typeorm migration:run -d src/data-source.ts`

## Seed sample data into PostgreSQL using TypeORM repositories

Take the entity

```ts
@Entity()
export class Post {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column()
  text: string;
}
```

Getting the repository

```ts
@Injectable()
export class PostService {
  constructor(
    @InjectRepository(Post)
    private readonly postRepository: Repository<Post>,
  ) {}
}
```

Sample data

```ts
async seed() {
  const posts = this.postRepository.create([
    {
      name: "First Post",
      text: "This is my first post.",
    },
    {
      name: "Second Post",
      text: "This is another sample post.",
    },
    {
      name: "Third Post",
      text: "This is some sample data.",
    },
  ]);

  await this.postRepository.save(posts);
}
```

## Explore how migrations can be used to roll back database changes

you can run: `typeorm migration:revert -- -d path-to-datasource-config` to
revert the changes.

The `up()` method describes what happens when the migration is applied/

```ts

async up(queryRunner: QueryRunner): Promise<void> {
  await queryRunner.query(
    `ALTER TABLE "post" RENAME COLUMN "title" TO "name"`,
  );
}
```

The down() method describes how to reverse that change:

```ts
async down(queryRunner: QueryRunner): Promise<void> {
  await queryRunner.query(
    `ALTER TABLE "post" RENAME COLUMN "name" TO "title"`,
  );
}
```

So basically:

| `up()`              | `down()`      |
| ------------------- | ------------- |
| Add column          | Remove column |
| Rename column A → B | Rename B → A  |
| Create table        | Drop table    |
| Add index           | Remove index  |

# Reflection

## What is the purpose of database migrations in TypeORM?

The purpose of database migrations in TypeORM is to manage changes to an
existing database schema over time. Migrations can be used to add, modify,
rename, or remove tables, columns, indexes, and other database structures. They
provide a controlled and repeatable way to update the database as an application
evolves.

## How do migrations differ from seeding?

Migrations and seeding serve different purposes. Migrations are primarily used
to change the database schema, such as adding a new column or modifying a table.
Seeding is used to insert initial or sample data into the database, such as
creating test users or sample posts. Therefore, migrations define changes to the
database structure, while seeding populates the database with data.

## Why is it important to version-control database schema changes?

because the database needs to remain consistent with the application's code as
it evolves. Version-controlled migrations provide a history of schema changes
and allow developers to apply the same changes consistently across development,
testing, and production environments. This also helps prevent problems caused by
manually or automatically changing the database schema without tracking those
changes.

## How can you roll back a migration if an issue occurs?

A migration can be rolled back using its down() method, which defines how to reverse the changes made by the up() method. TypeORM can execute this rollback using:

`typeorm migration:revert -- -d path-to-datasource-config`

This reverts the most recently applied migration by executing its down() method.
