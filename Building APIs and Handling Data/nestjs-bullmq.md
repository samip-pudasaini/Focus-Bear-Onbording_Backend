# BullMQ

BullMQ is a job/queue management library for Node.js that uses Redis to store
and process background jobs.

Basically, instead of making the user wait for a task to finish, put the task
into a queue and let a worker process it in the background.

```
Client / API
     |
     | add job
     ▼
  BullMQ Queue
     |
     | stored in Redis
     ▼
    Redis
     |
     | job available
     ▼
 BullMQ Worker
     |
     ▼
Process the job
```

It is useful for background processsing because it lets your NestJS application
move slow or non-urgent work out of the main HTTP request.

Suppose a user uploads a file and your server needs to process it:

```
User --> Post/upload --> upload file --> Process file --> Generate thumbnail --> Send notificaion --> Response
```

With BullMQ

```
User --> POST/upload --> Save file --> Add job to BullMQ --> Redis --> BullMQ Worker  ---> |
                    |--> Process file
                    |--> Generate thumbnail
                    |--> Send notification

```

The important difference is that the HTTP request doesn't have to wait for the
background work.

# Setup bullmq and @nestjs/bullmq in a NestJS service

Install

```
npm install --save @nestjs/bullmq bullmq
```

Implement in app.module.ts

```ts
import { Module } from "@nestjs/common";
import { TypeOrmModule } from "@nestjs/typeorm";
import { UsersModule } from "./users/users.module";
import { BullModule } from "@nestjs/bullmq";

@Module({
  imports: [
    BullModule.forRoot({
      connection: {
        host: "localhost",
        port: 6379,
      },
    }),
    TypeOrmModule.forRoot({
      type: "postgres",
      host: "localhost",
      port: 5432,
      username: "root",
      password: "root",
      database: "test",
      entities: [],
      synchronize: true,
    }),
    UsersModule,
  ],
})
export class AppModule {}
```

# Basic job queue

```ts
// tasks/tasks.module.ts
import { Module } from "@nestjs/common";
import { BullModule } from "@nestjs/bullmq";
import { TasksService } from "./tasks.service";
import { TasksController } from "./tasks.controller";
import { TasksProcessor } from "./tasks.processor";

@Module({
  imports: [
    BullModule.registerQueue({
      name: "tasks-queue",
    }),
  ],
  controllers: [TasksController],
  providers: [TasksService, TasksProcessor],
})
export class TasksModule {}
```

```ts
// tasks/tasks.service.ts
import { Injectable } from "@nestjs/common";
import { InjectQueue } from "@nestjs/bullmq";
import { Queue } from "bullmq";

@Injectable()
export class TasksService {
  constructor(@InjectQueue("tasks-queue") private readonly tasksQueue: Queue) {}

  async addTask(data: { email: string; message: string }) {
    const job = await this.tasksQueue.add("send-email", data, {
      attempts: 3,
      backoff: {
        type: "exponential",
        delay: 1000,
      },
      removeOnComplete: true,
      removeOnFail: false,
    });

    return { jobId: job.id };
  }
}
```

```ts
// tasks/tasks.controller.ts
import { Body, Controller, Post } from "@nestjs/common";
import { TasksService } from "./tasks.service";

@Controller("tasks")
export class TasksController {
  constructor(private readonly tasksService: TasksService) {}

  @Post()
  async createTask(@Body() body: { email: string; message: string }) {
    return this.tasksService.addTask(body);
  }
}
```

```ts
// tasks/tasks.processor.ts
import { Processor, WorkerHost } from "@nestjs/bullmq";
import { Job } from "bullmq";
import { Logger } from "@nestjs/common";

@Processor("tasks-queue")
export class TasksProcessor extends WorkerHost {
  private readonly logger = new Logger(TasksProcessor.name);

  async process(job: Job): Promise<any> {
    this.logger.log(`Processing job ${job.id} of type ${job.name}`);

    switch (job.name) {
      case "send-email":
        return this.handleSendEmail(job);
      default:
        this.logger.warn(`No handler for job type: ${job.name}`);
    }
  }

  private async handleSendEmail(job: Job) {
    const { email, message } = job.data;

    // Simulate some async work (e.g. calling an email API)
    await new Promise((resolve) => setTimeout(resolve, 2000));

    this.logger.log(`Email sent to ${email}: "${message}"`);
    return { sent: true, email };
  }
}
```

**How it works:**

POST /tasks with { "email": "test@example.com", "message": "hello" } adds a job
to the queue. The TasksProcessor picks it up in the background and runs
handleSendEmail. Failed jobs retry up to 3 times with exponential backoff.

# How Redis stores and manages queued jobs

A Redis job queue stores pending job IDs in a list and moves each claimed job
atomically to a processing list so a crashed worker's job can be reclaimed
later. Job metadata (payload, status, attempts, result) lives in a hash, and
completed jobs are cleaned up automatically with a TTL.

SO basically, Redis stores and manages queued jobs by using its data structure
to keep track of job data and the current state of each job. When a job is added
to a queue using a library such as BullMQ, Redis stores information such as the
job ID, job data, status, attempts, and timestamps. Jobs can move through
different states such as waiting, active, completed, failed, or delayed. Redis
also helps workers coordinate which jobs need to be processed and supports
features such as delayed jobs and retries. This allows Redis to act as a fast
coordination layer between the application and workers, while the workers are
responsible for actually processing the jobs.

# Reflection

## Why is BullMQ used instead of handling tasks directly in API requests?

BullMQ is used to handle tasks in the background instead of making the API
request wait for the task to finish. This is useful for tasks that may take a
long time, because the API can respond quickly while a worker processes the task
separately. It also helps prevent long-running tasks from slowing down or
blocking the main application.

## How does Redis help manage job queues in BullMQ?

Redis is used by BullMQ to store and manage information about queued jobs. It
keeps track of jobs that are waiting, being processed, completed, failed, or
delayed. Redis also allows the application and workers to communicate about the
state of jobs efficiently, making it possible for workers to retrieve and
process jobs from the queue.

## What happens if a job fails? How can failed jobs be retried?

If a job fails while being processed, BullMQ can mark the job as failed and
store information about the failure.The job can be configured to retry
automatically a certain number of times. For example, if a job is configured
with three attempts, BullMQ can try processing it again after a failure until it
succeeds or reaches the maximum number of attempts.

## How does Focus Bear use BullMQ for background tasks?

Focus Bear can use BullMQ for background tasks such as handling reminders for habit trackers. Instead of keeping an API request running until the reminder needs to be processed, the task can be added to a queue and processed by a worker at the appropriate time. This allows the main application to remain responsive while scheduled tasks are handled in the background.