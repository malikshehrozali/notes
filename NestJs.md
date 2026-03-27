# 🚀 NestJS Complete Notes — Everything You Need

> A complete reference guide covering every concept, pattern, and feature of NestJS with code examples and explanations.

---

## Table of Contents

1. [What is NestJS?](#1-what-is-nestjs)
2. [Installation & Project Setup](#2-installation--project-setup)
3. [Project Structure](#3-project-structure)
4. [Modules](#4-modules)
5. [Controllers](#5-controllers)
6. [Providers & Services](#6-providers--services)
7. [Dependency Injection](#7-dependency-injection)
8. [Middleware](#8-middleware)
9. [Exception Filters](#9-exception-filters)
10. [Pipes](#10-pipes)
11. [Guards](#11-guards)
12. [Interceptors](#12-interceptors)
13. [Decorators](#13-decorators)
14. [DTOs & Validation](#14-dtos--validation)
15. [Database Integration (TypeORM)](#15-database-integration-typeorm)
16. [Database Integration (Prisma)](#16-database-integration-prisma)
17. [Authentication (JWT + Passport)](#17-authentication-jwt--passport)
18. [Configuration & Environment Variables](#18-configuration--environment-variables)
19. [File Upload](#19-file-upload)
20. [WebSockets](#20-websockets)
21. [Microservices](#21-microservices)
22. [GraphQL](#22-graphql)
23. [Caching](#23-caching)
24. [Rate Limiting](#24-rate-limiting)
25. [Task Scheduling (Cron Jobs)](#25-task-scheduling-cron-jobs)
26. [Events](#26-events)
27. [Queues (Bull)](#27-queues-bull)
28. [Testing](#28-testing)
29. [Swagger / API Documentation](#29-swagger--api-documentation)
30. [CORS, Helmet & Security](#30-cors-helmet--security)
31. [Logging](#31-logging)
32. [CLI Commands Cheat Sheet](#32-cli-commands-cheat-sheet)

---

## 1. What is NestJS?

NestJS is a progressive **Node.js framework** for building efficient, reliable, and scalable server-side applications. It is built with and fully supports **TypeScript** (but also supports plain JavaScript).

Under the hood, NestJS uses **Express** by default (can switch to Fastify). It combines:
- **OOP** (Object-Oriented Programming)
- **FP** (Functional Programming)
- **FRP** (Functional Reactive Programming)

### Why NestJS?
- Enforces a solid application **architecture** out of the box (inspired by Angular)
- Built-in support for **TypeScript**
- Powerful **CLI** for code generation
- Modular, scalable codebase structure
- First-class support for REST, GraphQL, WebSockets, Microservices

---

## 2. Installation & Project Setup

### Install the NestJS CLI

```bash
npm install -g @nestjs/cli
```

### Create a new project

```bash
nest new my-project
```

You'll be asked to choose a package manager (npm, yarn, or pnpm).

### Start the development server

```bash
cd my-project
npm run start:dev    # with hot reload (watch mode)
npm run start        # without watch mode
npm run start:prod   # production mode
```

### Install common packages you'll need

```bash
# Validation
npm install class-validator class-transformer

# Configuration
npm install @nestjs/config

# TypeORM + PostgreSQL
npm install @nestjs/typeorm typeorm pg

# JWT Auth
npm install @nestjs/jwt @nestjs/passport passport passport-jwt
npm install -D @types/passport-jwt

# Swagger
npm install @nestjs/swagger swagger-ui-express

# Bcrypt
npm install bcrypt
npm install -D @types/bcrypt
```

---

## 3. Project Structure

When you create a project, NestJS generates this structure:

```
my-project/
├── src/
│   ├── app.controller.ts       ← Root controller
│   ├── app.controller.spec.ts  ← Root controller tests
│   ├── app.module.ts           ← Root module
│   ├── app.service.ts          ← Root service
│   └── main.ts                 ← Entry point
├── test/
│   ├── app.e2e-spec.ts
│   └── jest-e2e.json
├── nest-cli.json
├── package.json
├── tsconfig.json
└── tsconfig.build.json
```

### `main.ts` — Application Entry Point

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // Global prefix for all routes
  app.setGlobalPrefix('api');

  // Enable CORS
  app.enableCors();

  // Global validation pipe
  app.useGlobalPipes(new ValidationPipe({
    whitelist: true,        // strips unknown properties
    forbidNonWhitelisted: true,
    transform: true,        // auto-transform payloads to DTO types
  }));

  await app.listen(3000);
  console.log(`Application is running on: http://localhost:3000`);
}
bootstrap();
```

---

## 4. Modules

A **module** is a class decorated with `@Module()`. Every NestJS app has at least one module — the **root module** (`AppModule`). Modules are the primary way to organize your application.

### Module Decorator Options

| Property | Description |
|----------|-------------|
| `imports` | Other modules whose exported providers are needed here |
| `controllers` | Controllers defined in this module |
| `providers` | Services/providers to be instantiated by NestJS injector |
| `exports` | Providers to make available to other modules that import this module |

### Basic Module

```typescript
// cats/cats.module.ts
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService],  // Make CatsService available to other modules
})
export class CatsModule {}
```

### Root App Module

```typescript
// app.module.ts
import { Module } from '@nestjs/common';
import { CatsModule } from './cats/cats.module';
import { DogsModule } from './dogs/dogs.module';

@Module({
  imports: [CatsModule, DogsModule],
})
export class AppModule {}
```

### Shared Modules

When you export a provider from a module, any module that imports that module can use it.

```typescript
@Module({
  providers: [AuthService],
  exports: [AuthService],  // Now any importing module can use AuthService
})
export class AuthModule {}
```

### Global Modules

Use `@Global()` to make a module available everywhere without importing it every time.

```typescript
import { Global, Module } from '@nestjs/common';

@Global()
@Module({
  providers: [DatabaseService],
  exports: [DatabaseService],
})
export class DatabaseModule {}
```

### Dynamic Modules

Dynamic modules allow you to create customizable modules (like `forRoot()` pattern).

```typescript
// database/database.module.ts
import { Module, DynamicModule } from '@nestjs/common';

@Module({})
export class DatabaseModule {
  static forRoot(options: DatabaseOptions): DynamicModule {
    return {
      module: DatabaseModule,
      providers: [
        { provide: 'DATABASE_OPTIONS', useValue: options },
        DatabaseService,
      ],
      exports: [DatabaseService],
    };
  }
}

// Usage in app.module.ts
@Module({
  imports: [
    DatabaseModule.forRoot({ host: 'localhost', port: 5432 }),
  ],
})
export class AppModule {}
```

### Generate a module via CLI

```bash
nest generate module cats
# or shorthand:
nest g mo cats
```

---

## 5. Controllers

Controllers handle incoming **HTTP requests** and return responses. They are decorated with `@Controller()`.

### Basic Controller

```typescript
// cats/cats.controller.ts
import {
  Controller, Get, Post, Put, Patch, Delete,
  Body, Param, Query, Headers, Req, Res, HttpCode, HttpStatus
} from '@nestjs/common';
import { CatsService } from './cats.service';
import { CreateCatDto } from './dto/create-cat.dto';
import { UpdateCatDto } from './dto/update-cat.dto';

@Controller('cats')  // Route prefix: /cats
export class CatsController {
  constructor(private readonly catsService: CatsService) {}

  // GET /cats
  @Get()
  findAll() {
    return this.catsService.findAll();
  }

  // GET /cats/:id
  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.catsService.findOne(+id);  // +id converts string to number
  }

  // POST /cats
  @Post()
  @HttpCode(HttpStatus.CREATED)  // 201
  create(@Body() createCatDto: CreateCatDto) {
    return this.catsService.create(createCatDto);
  }

  // PUT /cats/:id
  @Put(':id')
  update(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
    return this.catsService.update(+id, updateCatDto);
  }

  // PATCH /cats/:id
  @Patch(':id')
  partialUpdate(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
    return this.catsService.update(+id, updateCatDto);
  }

  // DELETE /cats/:id
  @Delete(':id')
  remove(@Param('id') id: string) {
    return this.catsService.remove(+id);
  }
}
```

### Route Parameters

```typescript
// Multiple params: /users/:userId/posts/:postId
@Get(':userId/posts/:postId')
getUserPost(
  @Param('userId') userId: string,
  @Param('postId') postId: string,
) {
  return `User ${userId}, Post ${postId}`;
}

// Get all params as object
@Get(':id')
findOne(@Param() params: { id: string }) {
  return params.id;
}
```

### Query Parameters

```typescript
// GET /cats?limit=10&page=2&search=whiskers
@Get()
findAll(
  @Query('limit') limit: number,
  @Query('page') page: number,
  @Query('search') search: string,
) {
  return this.catsService.findAll({ limit, page, search });
}

// Get all query params as object
@Get()
findAll(@Query() query: { limit: string; page: string }) {
  return query;
}
```

### Request & Response Objects

```typescript
import { Request, Response } from 'express';

@Get()
findAll(@Req() req: Request) {
  console.log(req.headers);
  return this.catsService.findAll();
}

// Using raw Response (you must manage the response manually)
@Get()
findAll(@Res() res: Response) {
  res.status(200).json({ data: 'cats' });
}

// Passthrough — use @Res but still return normally
@Get()
findAll(@Res({ passthrough: true }) res: Response) {
  res.setHeader('X-Custom', 'value');
  return this.catsService.findAll();  // Still works
}
```

### Headers

```typescript
import { Header } from '@nestjs/common';

@Get()
@Header('Cache-Control', 'no-cache')
findAll() {
  return this.catsService.findAll();
}

// Read incoming header
@Get()
findAll(@Headers('authorization') auth: string) {
  return auth;
}
```

### Redirects

```typescript
import { Redirect } from '@nestjs/common';

@Get('redirect')
@Redirect('https://nestjs.com', 301)
redirect() {}

// Dynamic redirect
@Get('docs')
@Redirect('https://docs.nestjs.com', 302)
getDocs(@Query('version') version: string) {
  if (version === 'v5') {
    return { url: 'https://docs.nestjs.com/v5' };  // Overrides @Redirect
  }
}
```

### Generate a controller via CLI

```bash
nest g controller cats
# or
nest g co cats
```

---

## 6. Providers & Services

**Providers** are classes decorated with `@Injectable()`. They can be injected into controllers or other services via **Dependency Injection**.

### Basic Service

```typescript
// cats/cats.service.ts
import { Injectable, NotFoundException } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';

export interface Cat {
  id: number;
  name: string;
  age: number;
  breed: string;
}

@Injectable()
export class CatsService {
  private cats: Cat[] = [];
  private idCounter = 1;

  findAll(): Cat[] {
    return this.cats;
  }

  findOne(id: number): Cat {
    const cat = this.cats.find(c => c.id === id);
    if (!cat) {
      throw new NotFoundException(`Cat with id ${id} not found`);
    }
    return cat;
  }

  create(createCatDto: CreateCatDto): Cat {
    const newCat: Cat = { id: this.idCounter++, ...createCatDto };
    this.cats.push(newCat);
    return newCat;
  }

  update(id: number, updateData: Partial<CreateCatDto>): Cat {
    const cat = this.findOne(id);
    Object.assign(cat, updateData);
    return cat;
  }

  remove(id: number): void {
    const index = this.cats.findIndex(c => c.id === id);
    if (index === -1) throw new NotFoundException(`Cat ${id} not found`);
    this.cats.splice(index, 1);
  }
}
```

### Custom Providers

There are multiple ways to register providers:

```typescript
// 1. Standard (most common)
providers: [CatsService]
// Equivalent to:
providers: [{ provide: CatsService, useClass: CatsService }]

// 2. Value provider — inject a constant value
providers: [
  { provide: 'API_KEY', useValue: 'my-secret-api-key' }
]
// Inject with:
constructor(@Inject('API_KEY') private apiKey: string) {}

// 3. Factory provider — use a factory function
providers: [
  {
    provide: 'DATABASE_CONNECTION',
    useFactory: (configService: ConfigService) => {
      return createConnection(configService.get('DB_URL'));
    },
    inject: [ConfigService],  // Dependencies for the factory
  }
]

// 4. Alias provider (useExisting)
providers: [
  CatsService,
  { provide: 'CATS_ALIAS', useExisting: CatsService }
]
```

### Generate a service via CLI

```bash
nest g service cats
# or
nest g s cats
```

---

## 7. Dependency Injection

NestJS has a built-in **Inversion of Control (IoC)** container that manages dependencies automatically.

### Constructor Injection (Most Common)

```typescript
@Injectable()
export class CatsService {
  constructor(
    private readonly dogsService: DogsService,
    private readonly configService: ConfigService,
  ) {}
}
```

### Property Injection

```typescript
@Injectable()
export class CatsService {
  @Inject(DogsService)
  private readonly dogsService: DogsService;
}
```

### Inject with Token (String/Symbol)

```typescript
@Injectable()
export class CatsService {
  constructor(
    @Inject('CONFIG') private config: AppConfig,
    @Inject('API_KEY') private apiKey: string,
  ) {}
}
```

### Optional Dependencies

```typescript
@Injectable()
export class CatsService {
  constructor(
    @Optional() @Inject('LOGGER') private logger?: LoggerService,
  ) {}
}
```

---

## 8. Middleware

Middleware functions run **before** the route handler. They have access to `req`, `res`, and `next`.

### Functional Middleware

```typescript
// logger.middleware.ts
import { Request, Response, NextFunction } from 'express';

export function logger(req: Request, res: Response, next: NextFunction) {
  console.log(`[${req.method}] ${req.url}`);
  next();
}
```

### Class Middleware

```typescript
// logger.middleware.ts
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
    next();
  }
}
```

### Applying Middleware

```typescript
// app.module.ts
import { Module, NestModule, MiddlewareConsumer, RequestMethod } from '@nestjs/common';
import { LoggerMiddleware } from './logger.middleware';

@Module({})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes('cats');  // Apply to /cats routes only

    // Apply to specific methods
    consumer
      .apply(LoggerMiddleware)
      .forRoutes({ path: 'cats', method: RequestMethod.GET });

    // Apply to all routes
    consumer
      .apply(LoggerMiddleware)
      .forRoutes('*');

    // Exclude certain routes
    consumer
      .apply(LoggerMiddleware)
      .exclude('cats/(.*)')
      .forRoutes(CatsController);

    // Apply multiple middlewares
    consumer
      .apply(cors(), helmet(), LoggerMiddleware)
      .forRoutes('*');
  }
}
```

### Global Middleware (in main.ts)

```typescript
const app = await NestFactory.create(AppModule);
app.use(logger);  // Only works with functional middleware
```

---

## 9. Exception Filters

Exception filters handle errors and transform them into appropriate HTTP responses.

### Built-in Exceptions

```typescript
import {
  BadRequestException,         // 400
  UnauthorizedException,       // 401
  ForbiddenException,          // 403
  NotFoundException,           // 404
  MethodNotAllowedException,   // 405
  NotAcceptableException,      // 406
  RequestTimeoutException,     // 408
  ConflictException,           // 409
  GoneException,               // 410
  UnprocessableEntityException,// 422
  InternalServerErrorException,// 500
  NotImplementedException,     // 501
  BadGatewayException,         // 502
  ServiceUnavailableException, // 503
  GatewayTimeoutException,     // 504
} from '@nestjs/common';

// Usage in a service
throw new NotFoundException('Cat not found');
throw new BadRequestException('Invalid input', 'VALIDATION_ERROR');
throw new UnauthorizedException();

// With detailed message
throw new BadRequestException({
  statusCode: 400,
  message: ['name must be a string', 'age must be a number'],
  error: 'Bad Request',
});
```

### Custom Exception

```typescript
// custom.exception.ts
import { HttpException, HttpStatus } from '@nestjs/common';

export class CustomException extends HttpException {
  constructor(message: string) {
    super(
      { statusCode: HttpStatus.I_AM_A_TEAPOT, message, error: 'Custom Error' },
      HttpStatus.I_AM_A_TEAPOT,
    );
  }
}
```

### Custom Exception Filter

```typescript
// http-exception.filter.ts
import {
  ExceptionFilter, Catch, ArgumentsHost,
  HttpException, HttpStatus, Logger,
} from '@nestjs/common';
import { Request, Response } from 'express';

@Catch(HttpException)  // Catch specific exception type
export class HttpExceptionFilter implements ExceptionFilter {
  private readonly logger = new Logger(HttpExceptionFilter.name);

  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();
    const exceptionResponse = exception.getResponse();

    const errorResponse = {
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
      method: request.method,
      message:
        typeof exceptionResponse === 'string'
          ? exceptionResponse
          : (exceptionResponse as any).message,
    };

    this.logger.error(`${request.method} ${request.url}`, JSON.stringify(errorResponse));
    response.status(status).json(errorResponse);
  }
}

// Catch ALL exceptions
@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();

    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : HttpStatus.INTERNAL_SERVER_ERROR;

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
    });
  }
}
```

### Applying Exception Filters

```typescript
// Controller level
@UseFilters(HttpExceptionFilter)
@Controller('cats')
export class CatsController {}

// Method level
@UseFilters(new HttpExceptionFilter())
@Get()
findAll() {}

// Global level (main.ts)
app.useGlobalFilters(new HttpExceptionFilter());

// Global level (module — allows DI)
// app.module.ts
providers: [
  { provide: APP_FILTER, useClass: HttpExceptionFilter }
]
```

---

## 10. Pipes

Pipes are used for **transformation** (converting input data) and **validation** (validating input data).

### Built-in Pipes

```typescript
import {
  ParseIntPipe,
  ParseFloatPipe,
  ParseBoolPipe,
  ParseArrayPipe,
  ParseUUIDPipe,
  ParseEnumPipe,
  DefaultValuePipe,
  ValidationPipe,
} from '@nestjs/common';

@Get(':id')
findOne(@Param('id', ParseIntPipe) id: number) {
  // id is guaranteed to be a number
  return this.catsService.findOne(id);
}

@Get(':uuid')
findByUUID(@Param('uuid', ParseUUIDPipe) uuid: string) {}

// With default value
@Get()
findAll(@Query('page', new DefaultValuePipe(1), ParseIntPipe) page: number) {}
```

### Validation Pipe (Most Important)

```typescript
// Global — apply to all routes (recommended)
// main.ts
app.useGlobalPipes(
  new ValidationPipe({
    whitelist: true,              // Remove unknown properties automatically
    forbidNonWhitelisted: true,   // Throw error if unknown properties exist
    transform: true,              // Transform payloads to DTO class instances
    transformOptions: {
      enableImplicitConversion: true, // Auto-convert query params to proper types
    },
    disableErrorMessages: false,  // Set true in production to hide validation errors
  })
);
```

### Custom Pipe

```typescript
// trim.pipe.ts
import { PipeTransform, Injectable, ArgumentMetadata, BadRequestException } from '@nestjs/common';

@Injectable()
export class TrimPipe implements PipeTransform {
  transform(value: any, metadata: ArgumentMetadata) {
    if (typeof value === 'string') {
      return value.trim();
    }
    if (typeof value === 'object' && value !== null) {
      Object.keys(value).forEach(key => {
        if (typeof value[key] === 'string') {
          value[key] = value[key].trim();
        }
      });
    }
    return value;
  }
}

// Validation pipe
@Injectable()
export class ParsePositiveIntPipe implements PipeTransform<string, number> {
  transform(value: string, metadata: ArgumentMetadata): number {
    const val = parseInt(value, 10);
    if (isNaN(val) || val <= 0) {
      throw new BadRequestException(`${metadata.data} must be a positive integer`);
    }
    return val;
  }
}

// Usage
@Get(':id')
findOne(@Param('id', ParsePositiveIntPipe) id: number) {}
```

---

## 11. Guards

Guards determine whether a request should be handled — used primarily for **authorization**.

### Basic Guard

```typescript
// auth.guard.ts
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Observable } from 'rxjs';

@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(
    context: ExecutionContext,
  ): boolean | Promise<boolean> | Observable<boolean> {
    const request = context.switchToHttp().getRequest();
    return this.validateRequest(request);
  }

  private validateRequest(request: any): boolean {
    // Check if token exists in headers
    return !!request.headers.authorization;
  }
}
```

### JWT Auth Guard

```typescript
// jwt-auth.guard.ts
import { Injectable, ExecutionContext, UnauthorizedException } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {
  canActivate(context: ExecutionContext) {
    return super.canActivate(context);
  }

  handleRequest(err: any, user: any) {
    if (err || !user) {
      throw err || new UnauthorizedException('Invalid or missing token');
    }
    return user;
  }
}
```

### Role-Based Guard with Reflector

```typescript
// roles.decorator.ts
import { SetMetadata } from '@nestjs/common';

export const ROLES_KEY = 'roles';
export const Roles = (...roles: string[]) => SetMetadata(ROLES_KEY, roles);

// roles.guard.ts
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Reflector } from '@nestjs/core';
import { ROLES_KEY } from './roles.decorator';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.getAllAndOverride<string[]>(ROLES_KEY, [
      context.getHandler(),
      context.getClass(),
    ]);

    if (!requiredRoles) return true;  // No roles needed = public

    const { user } = context.switchToHttp().getRequest();
    return requiredRoles.some(role => user?.roles?.includes(role));
  }
}

// Usage in controller
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin')
@Delete(':id')
remove(@Param('id') id: string) {}
```

### Public Routes Decorator

```typescript
// public.decorator.ts
import { SetMetadata } from '@nestjs/common';
export const IS_PUBLIC_KEY = 'isPublic';
export const Public = () => SetMetadata(IS_PUBLIC_KEY, true);

// In JwtAuthGuard — skip auth for public routes
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {
  constructor(private reflector: Reflector) {
    super();
  }

  canActivate(context: ExecutionContext) {
    const isPublic = this.reflector.getAllAndOverride<boolean>(IS_PUBLIC_KEY, [
      context.getHandler(),
      context.getClass(),
    ]);
    if (isPublic) return true;
    return super.canActivate(context);
  }
}

// Usage
@Public()
@Get('health')
health() { return 'OK'; }
```

### Applying Guards

```typescript
// Method level
@UseGuards(AuthGuard)
@Get('profile')
getProfile() {}

// Controller level
@UseGuards(AuthGuard)
@Controller('cats')
export class CatsController {}

// Global (main.ts)
app.useGlobalGuards(new AuthGuard());

// Global (module — allows DI)
providers: [
  { provide: APP_GUARD, useClass: JwtAuthGuard }
]
```

---

## 12. Interceptors

Interceptors can: transform results, add extra logic before/after handlers, override functions, and more.

### Basic Interceptor

```typescript
// logging.interceptor.ts
import {
  Injectable, NestInterceptor, ExecutionContext,
  CallHandler, Logger,
} from '@nestjs/common';
import { Observable } from 'rxjs';
import { tap, map } from 'rxjs/operators';

@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  private readonly logger = new Logger(LoggingInterceptor.name);

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const req = context.switchToHttp().getRequest();
    const { method, url } = req;
    const start = Date.now();

    this.logger.log(`Incoming → ${method} ${url}`);

    return next.handle().pipe(
      tap(() => {
        const ms = Date.now() - start;
        this.logger.log(`Outgoing ← ${method} ${url} ${ms}ms`);
      }),
    );
  }
}
```

### Transform Response Interceptor

```typescript
// transform.interceptor.ts
import { Injectable, NestInterceptor, ExecutionContext, CallHandler } from '@nestjs/common';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

export interface ApiResponse<T> {
  success: boolean;
  data: T;
  timestamp: string;
}

@Injectable()
export class TransformInterceptor<T> implements NestInterceptor<T, ApiResponse<T>> {
  intercept(context: ExecutionContext, next: CallHandler): Observable<ApiResponse<T>> {
    return next.handle().pipe(
      map(data => ({
        success: true,
        data,
        timestamp: new Date().toISOString(),
      })),
    );
  }
}
```

### Timeout Interceptor

```typescript
import { Injectable, NestInterceptor, ExecutionContext, CallHandler, RequestTimeoutException } from '@nestjs/common';
import { Observable, throwError, TimeoutError } from 'rxjs';
import { catchError, timeout } from 'rxjs/operators';

@Injectable()
export class TimeoutInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(
      timeout(5000),  // 5 second timeout
      catchError(err => {
        if (err instanceof TimeoutError) {
          return throwError(() => new RequestTimeoutException());
        }
        return throwError(() => err);
      }),
    );
  }
}
```

### Applying Interceptors

```typescript
// Controller/method level
@UseInterceptors(LoggingInterceptor)
@Controller('cats')
export class CatsController {}

// Global (main.ts)
app.useGlobalInterceptors(new LoggingInterceptor());

// Global (module — allows DI)
providers: [
  { provide: APP_INTERCEPTOR, useClass: LoggingInterceptor }
]
```

---

## 13. Decorators

### Custom Decorators

```typescript
// user.decorator.ts — Extract user from request
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const CurrentUser = createParamDecorator(
  (data: string | undefined, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    const user = request.user;
    return data ? user?.[data] : user;  // If data provided, return specific field
  },
);

// Usage
@Get('profile')
getProfile(@CurrentUser() user: User) {}

@Get('profile')
getProfile(@CurrentUser('email') email: string) {}
```

```typescript
// ip.decorator.ts — Extract client IP
export const ClientIp = createParamDecorator(
  (data: unknown, ctx: ExecutionContext): string => {
    const request = ctx.switchToHttp().getRequest();
    return request.ip || request.connection.remoteAddress;
  },
);
```

### Composing Decorators

```typescript
// auth.decorator.ts — Combine multiple decorators
import { applyDecorators, UseGuards, SetMetadata } from '@nestjs/common';
import { ApiBearerAuth, ApiUnauthorizedResponse } from '@nestjs/swagger';

export function Auth(...roles: string[]) {
  return applyDecorators(
    SetMetadata('roles', roles),
    UseGuards(JwtAuthGuard, RolesGuard),
    ApiBearerAuth(),
    ApiUnauthorizedResponse({ description: 'Unauthorized' }),
  );
}

// Usage
@Auth('admin')
@Get('secret')
getSecret() {}
```

---

## 14. DTOs & Validation

**DTOs (Data Transfer Objects)** define the shape of data going in/out. Combined with `class-validator`, they provide powerful validation.

### Install

```bash
npm install class-validator class-transformer
```

### Create DTO

```typescript
// dto/create-cat.dto.ts
import {
  IsString, IsInt, IsEmail, IsOptional, IsEnum,
  MinLength, MaxLength, Min, Max, IsArray, ArrayNotEmpty,
  IsNotEmpty, IsUrl, IsDateString, ValidateNested, IsBoolean,
  IsNumber, IsPositive,
} from 'class-validator';
import { Type } from 'class-transformer';

export enum CatBreed {
  PERSIAN = 'persian',
  SIAMESE = 'siamese',
  MAINE_COON = 'maine_coon',
}

export class CreateCatDto {
  @IsString()
  @IsNotEmpty()
  @MinLength(2)
  @MaxLength(100)
  name: string;

  @IsInt()
  @Min(0)
  @Max(30)
  age: number;

  @IsEnum(CatBreed)
  breed: CatBreed;

  @IsEmail()
  @IsOptional()
  ownerEmail?: string;

  @IsArray()
  @IsString({ each: true })
  @ArrayNotEmpty()
  @IsOptional()
  tags?: string[];

  @IsBoolean()
  @IsOptional()
  isNeutered?: boolean;
}
```

### Update DTO (Partial)

```typescript
// dto/update-cat.dto.ts
import { PartialType } from '@nestjs/mapped-types';
import { CreateCatDto } from './create-cat.dto';

// Makes all fields optional
export class UpdateCatDto extends PartialType(CreateCatDto) {}
```

### Nested DTOs

```typescript
// dto/create-user.dto.ts
import { Type } from 'class-transformer';
import { ValidateNested, IsObject } from 'class-validator';

class AddressDto {
  @IsString()
  street: string;

  @IsString()
  city: string;
}

export class CreateUserDto {
  @IsString()
  name: string;

  @IsObject()
  @ValidateNested()
  @Type(() => AddressDto)
  address: AddressDto;
}
```

### Response DTO / Serialization

```typescript
// dto/cat-response.dto.ts
import { Exclude, Expose, Transform } from 'class-transformer';

export class CatResponseDto {
  @Expose()
  id: number;

  @Expose()
  name: string;

  @Expose()
  @Transform(({ value }) => value.toUpperCase())
  breed: string;

  @Exclude()  // This field will NOT be in the response
  password: string;

  constructor(partial: Partial<CatResponseDto>) {
    Object.assign(this, partial);
  }
}

// In controller — use ClassSerializerInterceptor
@UseInterceptors(ClassSerializerInterceptor)
@Get()
findAll() {
  return cats.map(cat => new CatResponseDto(cat));
}

// Or globally in main.ts
app.useGlobalInterceptors(new ClassSerializerInterceptor(app.get(Reflector)));
```

### Common Validators

```typescript
// String validators
@IsString()
@IsNotEmpty()
@MinLength(3)
@MaxLength(50)
@Matches(/^[a-zA-Z\s]*$/, { message: 'Only letters allowed' })
@IsEmail()
@IsUrl()
@IsUUID()
@IsAlpha()
@IsAlphanumeric()

// Number validators
@IsNumber()
@IsInt()
@IsPositive()
@IsNegative()
@Min(0)
@Max(100)

// Date validators
@IsDate()
@IsDateString()
@MinDate(new Date())
@MaxDate(new Date())

// Array validators
@IsArray()
@ArrayNotEmpty()
@ArrayMinSize(1)
@ArrayMaxSize(10)
@IsString({ each: true })  // Validates each element

// Boolean
@IsBoolean()

// Object
@IsObject()

// Other
@IsEnum(MyEnum)
@IsOptional()  // Allows undefined/null to bypass validation
@IsNotEmpty()
@IsDefined()
```

---

## 15. Database Integration (TypeORM)

### Install

```bash
npm install @nestjs/typeorm typeorm pg  # PostgreSQL
# or
npm install @nestjs/typeorm typeorm mysql2  # MySQL
# or
npm install @nestjs/typeorm typeorm sqlite3  # SQLite
```

### Setup TypeORM Module

```typescript
// app.module.ts
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'postgres',
      host: process.env.DB_HOST || 'localhost',
      port: +process.env.DB_PORT || 5432,
      username: process.env.DB_USER || 'postgres',
      password: process.env.DB_PASS || 'postgres',
      database: process.env.DB_NAME || 'mydb',
      entities: [__dirname + '/**/*.entity{.ts,.js}'],
      synchronize: true,  // ⚠️ Only for development! Use migrations in production
      logging: true,
    }),
  ],
})
export class AppModule {}
```

### Create Entity

```typescript
// cats/cat.entity.ts
import {
  Entity, Column, PrimaryGeneratedColumn,
  CreateDateColumn, UpdateDateColumn, DeleteDateColumn,
  ManyToOne, OneToMany, ManyToMany, JoinTable, JoinColumn,
  BeforeInsert, BeforeUpdate, Index,
} from 'typeorm';
import { Exclude } from 'class-transformer';

@Entity('cats')
@Index(['name', 'breed'])  // Composite index
export class Cat {
  @PrimaryGeneratedColumn()
  id: number;

  @PrimaryGeneratedColumn('uuid')  // Alternative: UUID primary key
  uuid: string;

  @Column({ length: 100 })
  name: string;

  @Column({ type: 'int' })
  age: number;

  @Column({ nullable: true })
  breed: string;

  @Column({ default: false })
  isNeutered: boolean;

  @Column({ type: 'decimal', precision: 5, scale: 2, nullable: true })
  weight: number;

  @Column({ type: 'jsonb', nullable: true })
  metadata: Record<string, any>;

  @Column({ type: 'simple-array', nullable: true })
  tags: string[];

  @Column({ unique: true })
  @Index()
  microchipId: string;

  @Exclude()
  @Column({ select: false })  // Never selected unless explicitly asked
  internalNote: string;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;

  @DeleteDateColumn()  // For soft-delete
  deletedAt: Date;

  // Lifecycle hooks
  @BeforeInsert()
  beforeInsert() {
    this.name = this.name.trim();
  }

  @BeforeUpdate()
  beforeUpdate() {
    this.name = this.name.trim();
  }
}
```

### Entity Relationships

```typescript
// One-to-Many / Many-to-One
@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @OneToMany(() => Post, post => post.author)
  posts: Post[];
}

@Entity()
export class Post {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @ManyToOne(() => User, user => user.posts, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'author_id' })
  author: User;

  @Column()
  author_id: number;
}

// Many-to-Many
@Entity()
export class Student {
  @PrimaryGeneratedColumn()
  id: number;

  @ManyToMany(() => Course, course => course.students)
  @JoinTable()  // Only on the owning side
  courses: Course[];
}

@Entity()
export class Course {
  @PrimaryGeneratedColumn()
  id: number;

  @ManyToMany(() => Student, student => student.courses)
  students: Student[];
}

// One-to-One
@Entity()
export class Profile {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  bio: string;

  @OneToOne(() => User)
  @JoinColumn()
  user: User;
}
```

### Repository Pattern

```typescript
// cats/cats.module.ts
import { TypeOrmModule } from '@nestjs/typeorm';
import { Cat } from './cat.entity';

@Module({
  imports: [TypeOrmModule.forFeature([Cat])],
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {}

// cats/cats.service.ts
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, Like, Between, In, Not, IsNull } from 'typeorm';
import { Cat } from './cat.entity';

@Injectable()
export class CatsService {
  constructor(
    @InjectRepository(Cat)
    private catsRepository: Repository<Cat>,
  ) {}

  // Find all
  findAll(): Promise<Cat[]> {
    return this.catsRepository.find();
  }

  // Find with options
  findWithOptions() {
    return this.catsRepository.find({
      where: { isNeutered: true },
      order: { name: 'ASC' },
      skip: 0,
      take: 10,
      relations: ['owner'],
    });
  }

  // Find one
  findOne(id: number): Promise<Cat | null> {
    return this.catsRepository.findOne({ where: { id } });
  }

  // Find with where operators
  findFiltered() {
    return this.catsRepository.find({
      where: {
        name: Like('%whiskers%'),
        age: Between(1, 5),
        breed: In(['persian', 'siamese']),
        ownerEmail: Not(IsNull()),
      },
    });
  }

  // Create & Save
  async create(createCatDto: CreateCatDto): Promise<Cat> {
    const cat = this.catsRepository.create(createCatDto);
    return this.catsRepository.save(cat);
  }

  // Update
  async update(id: number, updateCatDto: UpdateCatDto): Promise<Cat> {
    await this.catsRepository.update(id, updateCatDto);
    return this.findOne(id);
  }

  // Delete
  async remove(id: number): Promise<void> {
    await this.catsRepository.delete(id);
  }

  // Soft delete (requires @DeleteDateColumn)
  async softRemove(id: number): Promise<void> {
    await this.catsRepository.softDelete(id);
  }

  // Count
  count(): Promise<number> {
    return this.catsRepository.count();
  }

  // Exists check
  async exists(id: number): Promise<boolean> {
    return this.catsRepository.existsBy({ id });
  }
}
```

### Query Builder

```typescript
@Injectable()
export class CatsService {
  async findByBreedAndAge(breed: string, minAge: number): Promise<Cat[]> {
    return this.catsRepository
      .createQueryBuilder('cat')
      .leftJoinAndSelect('cat.owner', 'owner')
      .where('cat.breed = :breed', { breed })
      .andWhere('cat.age >= :minAge', { minAge })
      .orderBy('cat.name', 'ASC')
      .take(10)
      .skip(0)
      .getMany();
  }

  async getStats() {
    return this.catsRepository
      .createQueryBuilder('cat')
      .select('cat.breed', 'breed')
      .addSelect('COUNT(*)', 'count')
      .addSelect('AVG(cat.age)', 'avgAge')
      .groupBy('cat.breed')
      .getRawMany();
  }
}
```

### Migrations

```bash
# Generate migration
npx typeorm migration:generate src/migrations/CreateCatTable -d src/data-source.ts

# Run migrations
npx typeorm migration:run -d src/data-source.ts

# Revert last migration
npx typeorm migration:revert -d src/data-source.ts
```

```typescript
// data-source.ts
import { DataSource } from 'typeorm';

export const AppDataSource = new DataSource({
  type: 'postgres',
  host: 'localhost',
  port: 5432,
  username: 'postgres',
  password: 'postgres',
  database: 'mydb',
  entities: ['src/**/*.entity.ts'],
  migrations: ['src/migrations/*.ts'],
});
```

---

## 16. Database Integration (Prisma)

### Install

```bash
npm install prisma @prisma/client
npx prisma init
```

### schema.prisma

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  author    User    @relation(fields: [authorId], references: [id])
  authorId  Int
}
```

```bash
npx prisma migrate dev --name init
npx prisma generate
```

### Prisma Service

```typescript
// prisma/prisma.service.ts
import { Injectable, OnModuleInit } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit {
  async onModuleInit() {
    await this.$connect();
  }
}

// prisma/prisma.module.ts
@Module({
  providers: [PrismaService],
  exports: [PrismaService],
})
export class PrismaModule {}

// Using Prisma in service
@Injectable()
export class UsersService {
  constructor(private prisma: PrismaService) {}

  findAll() {
    return this.prisma.user.findMany({
      include: { posts: true },
    });
  }

  findOne(id: number) {
    return this.prisma.user.findUnique({
      where: { id },
    });
  }

  create(data: { email: string; name: string }) {
    return this.prisma.user.create({ data });
  }

  update(id: number, data: Partial<{ email: string; name: string }>) {
    return this.prisma.user.update({
      where: { id },
      data,
    });
  }

  remove(id: number) {
    return this.prisma.user.delete({ where: { id } });
  }
}
```

---

## 17. Authentication (JWT + Passport)

### Install

```bash
npm install @nestjs/jwt @nestjs/passport passport passport-jwt passport-local bcrypt
npm install -D @types/passport-jwt @types/passport-local @types/bcrypt
```

### Auth Module Structure

```
src/auth/
├── auth.module.ts
├── auth.controller.ts
├── auth.service.ts
├── strategies/
│   ├── jwt.strategy.ts
│   └── local.strategy.ts
├── guards/
│   ├── jwt-auth.guard.ts
│   └── local-auth.guard.ts
└── dto/
    └── login.dto.ts
```

### Local Strategy (Username/Password)

```typescript
// strategies/local.strategy.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { Strategy } from 'passport-local';
import { AuthService } from '../auth.service';

@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private authService: AuthService) {
    super({ usernameField: 'email' }); // Use email instead of username
  }

  async validate(email: string, password: string) {
    const user = await this.authService.validateUser(email, password);
    if (!user) {
      throw new UnauthorizedException('Invalid credentials');
    }
    return user;
  }
}
```

### JWT Strategy

```typescript
// strategies/jwt.strategy.ts
import { Injectable } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';
import { ConfigService } from '@nestjs/config';

export interface JwtPayload {
  sub: number;
  email: string;
  roles: string[];
}

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(configService: ConfigService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: configService.get<string>('JWT_SECRET'),
    });
  }

  async validate(payload: JwtPayload) {
    // Whatever you return here will be attached to request.user
    return { id: payload.sub, email: payload.email, roles: payload.roles };
  }
}
```

### Auth Service

```typescript
// auth.service.ts
import { Injectable } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { UsersService } from '../users/users.service';
import * as bcrypt from 'bcrypt';

@Injectable()
export class AuthService {
  constructor(
    private usersService: UsersService,
    private jwtService: JwtService,
  ) {}

  async validateUser(email: string, password: string) {
    const user = await this.usersService.findByEmail(email);
    if (user && await bcrypt.compare(password, user.password)) {
      const { password: _, ...result } = user;  // Exclude password
      return result;
    }
    return null;
  }

  async login(user: any) {
    const payload = { email: user.email, sub: user.id, roles: user.roles };
    return {
      access_token: this.jwtService.sign(payload),
      refresh_token: this.jwtService.sign(payload, { expiresIn: '7d' }),
      user,
    };
  }

  async register(createUserDto: CreateUserDto) {
    const hashedPassword = await bcrypt.hash(createUserDto.password, 10);
    const user = await this.usersService.create({
      ...createUserDto,
      password: hashedPassword,
    });
    return this.login(user);
  }
}
```

### Auth Module

```typescript
// auth.module.ts
import { Module } from '@nestjs/common';
import { JwtModule } from '@nestjs/jwt';
import { PassportModule } from '@nestjs/passport';

@Module({
  imports: [
    UsersModule,
    PassportModule,
    JwtModule.register({
      secret: process.env.JWT_SECRET,
      signOptions: { expiresIn: '1h' },
    }),
    // Or with ConfigModule:
    JwtModule.registerAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: (config: ConfigService) => ({
        secret: config.get('JWT_SECRET'),
        signOptions: { expiresIn: config.get('JWT_EXPIRES_IN', '1h') },
      }),
    }),
  ],
  providers: [AuthService, LocalStrategy, JwtStrategy],
  controllers: [AuthController],
})
export class AuthModule {}
```

### Auth Controller

```typescript
// auth.controller.ts
@Controller('auth')
export class AuthController {
  constructor(private authService: AuthService) {}

  @Public()
  @UseGuards(LocalAuthGuard)
  @Post('login')
  async login(@CurrentUser() user: any) {
    return this.authService.login(user);
  }

  @Public()
  @Post('register')
  async register(@Body() createUserDto: CreateUserDto) {
    return this.authService.register(createUserDto);
  }

  @UseGuards(JwtAuthGuard)
  @Get('profile')
  getProfile(@CurrentUser() user: any) {
    return user;
  }
}
```

---

## 18. Configuration & Environment Variables

### Install

```bash
npm install @nestjs/config
```

### Setup

```typescript
// app.module.ts
import { ConfigModule, ConfigService } from '@nestjs/config';

@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true,       // No need to import in every module
      envFilePath: '.env',
      cache: true,          // Cache config values
      expandVariables: true, // Allow ${VAR} syntax in .env
    }),
  ],
})
export class AppModule {}
```

### `.env` file

```env
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
JWT_SECRET=my-super-secret-key
JWT_EXPIRES_IN=1h
```

### Config Schemas (Validation)

```typescript
// config/configuration.ts
import { plainToClass } from 'class-transformer';
import { IsEnum, IsNumber, IsString, validateSync } from 'class-validator';

enum Environment { Development = 'development', Production = 'production', Test = 'test' }

class EnvironmentVariables {
  @IsEnum(Environment)
  NODE_ENV: Environment;

  @IsNumber()
  PORT: number;

  @IsString()
  DATABASE_URL: string;

  @IsString()
  JWT_SECRET: string;
}

export function validate(config: Record<string, unknown>) {
  const validatedConfig = plainToClass(EnvironmentVariables, config, {
    enableImplicitConversion: true,
  });
  const errors = validateSync(validatedConfig, { skipMissingProperties: false });
  if (errors.length > 0) {
    throw new Error(errors.toString());
  }
  return validatedConfig;
}

// In ConfigModule.forRoot({ validate })
```

### Custom Config Files

```typescript
// config/database.config.ts
import { registerAs } from '@nestjs/config';

export default registerAs('database', () => ({
  host: process.env.DB_HOST || 'localhost',
  port: parseInt(process.env.DB_PORT, 10) || 5432,
  username: process.env.DB_USER,
  password: process.env.DB_PASS,
  name: process.env.DB_NAME,
}));

// app.module.ts
ConfigModule.forRoot({
  load: [databaseConfig, jwtConfig],
})

// Usage in service
@Injectable()
export class DatabaseService {
  constructor(
    @InjectConfig('database') private dbConfig: ConfigType<typeof databaseConfig>
  ) {}
}
// Or with ConfigService:
constructor(private config: ConfigService) {}
const host = this.config.get<string>('database.host');
const port = this.config.get<number>('database.port');
```

---

## 19. File Upload

### Install

```bash
npm install -D @types/multer
```

### Single File Upload

```typescript
import {
  Controller, Post, UseInterceptors,
  UploadedFile, UploadedFiles, ParseFilePipe,
  MaxFileSizeValidator, FileTypeValidator,
} from '@nestjs/common';
import { FileInterceptor, FilesInterceptor, FileFieldsInterceptor } from '@nestjs/platform-express';
import { diskStorage } from 'multer';
import { extname } from 'path';

@Controller('upload')
export class UploadController {
  // Single file
  @Post('single')
  @UseInterceptors(FileInterceptor('file'))
  uploadFile(
    @UploadedFile(
      new ParseFilePipe({
        validators: [
          new MaxFileSizeValidator({ maxSize: 1024 * 1024 * 5 }), // 5MB
          new FileTypeValidator({ fileType: '.(png|jpg|jpeg|gif)' }),
        ],
      }),
    )
    file: Express.Multer.File,
  ) {
    return {
      originalname: file.originalname,
      filename: file.filename,
      size: file.size,
      mimetype: file.mimetype,
    };
  }

  // Multiple files
  @Post('multiple')
  @UseInterceptors(FilesInterceptor('files', 10))
  uploadMultiple(@UploadedFiles() files: Express.Multer.File[]) {
    return files.map(f => ({ name: f.originalname, size: f.size }));
  }
}
```

### Custom Storage

```typescript
const storage = diskStorage({
  destination: './uploads',
  filename: (req, file, callback) => {
    const uniqueName = `${Date.now()}-${Math.round(Math.random() * 1e9)}`;
    const ext = extname(file.originalname);
    callback(null, `${uniqueName}${ext}`);
  },
});

@Post('disk')
@UseInterceptors(FileInterceptor('file', { storage }))
uploadToDisk(@UploadedFile() file: Express.Multer.File) {
  return { path: file.path };
}
```

---

## 20. WebSockets

### Install

```bash
npm install @nestjs/websockets @nestjs/platform-socket.io socket.io
```

### Gateway

```typescript
// chat/chat.gateway.ts
import {
  WebSocketGateway, WebSocketServer, SubscribeMessage,
  MessageBody, ConnectedSocket, OnGatewayInit,
  OnGatewayConnection, OnGatewayDisconnect,
} from '@nestjs/websockets';
import { Server, Socket } from 'socket.io';
import { Logger } from '@nestjs/common';

@WebSocketGateway({
  cors: { origin: '*' },
  namespace: '/chat',   // Optional namespace
})
export class ChatGateway
  implements OnGatewayInit, OnGatewayConnection, OnGatewayDisconnect
{
  @WebSocketServer()
  server: Server;

  private logger = new Logger(ChatGateway.name);

  afterInit() {
    this.logger.log('WebSocket Gateway initialized');
  }

  handleConnection(client: Socket) {
    this.logger.log(`Client connected: ${client.id}`);
  }

  handleDisconnect(client: Socket) {
    this.logger.log(`Client disconnected: ${client.id}`);
  }

  @SubscribeMessage('message')
  handleMessage(
    @MessageBody() data: { room: string; message: string },
    @ConnectedSocket() client: Socket,
  ) {
    // Emit to all in room except sender
    client.to(data.room).emit('message', {
      sender: client.id,
      message: data.message,
    });
  }

  @SubscribeMessage('join-room')
  joinRoom(
    @MessageBody() room: string,
    @ConnectedSocket() client: Socket,
  ) {
    client.join(room);
    client.to(room).emit('user-joined', { userId: client.id });
    return { success: true };  // Acknowledgment
  }

  // Emit to all clients from anywhere
  broadcastMessage(event: string, data: any) {
    this.server.emit(event, data);
  }
}
```

---

## 21. Microservices

### Install

```bash
npm install @nestjs/microservices
# For RabbitMQ:
npm install amqplib amqp-connection-manager
# For Redis:
npm install ioredis
```

### TCP Microservice

```typescript
// main.ts (microservice)
import { NestFactory } from '@nestjs/core';
import { Transport, MicroserviceOptions } from '@nestjs/microservices';

const app = await NestFactory.createMicroservice<MicroserviceOptions>(
  AppModule,
  {
    transport: Transport.TCP,
    options: { host: '0.0.0.0', port: 3001 },
  },
);
await app.listen();

// message-handler.controller.ts
import { Controller } from '@nestjs/common';
import { MessagePattern, EventPattern, Payload } from '@nestjs/microservices';

@Controller()
export class AppController {
  @MessagePattern({ cmd: 'get_cat' })
  getCat(@Payload() data: { id: number }) {
    return { id: data.id, name: 'Whiskers' };
  }

  @EventPattern('cat_created')
  handleCatCreated(@Payload() data: any) {
    console.log('Cat created event received:', data);
    // No response needed for events
  }
}

// Calling microservice from another service
import { ClientProxy, ClientProxyFactory, Transport } from '@nestjs/microservices';

@Injectable()
export class CatsService {
  private client: ClientProxy;

  constructor() {
    this.client = ClientProxyFactory.create({
      transport: Transport.TCP,
      options: { host: 'localhost', port: 3001 },
    });
  }

  getCat(id: number) {
    return this.client.send({ cmd: 'get_cat' }, { id });  // Returns Observable
  }
}
```

---

## 22. GraphQL

### Install

```bash
npm install @nestjs/graphql @nestjs/apollo @apollo/server graphql
```

### Setup

```typescript
// app.module.ts
import { GraphQLModule } from '@nestjs/graphql';
import { ApolloDriver, ApolloDriverConfig } from '@nestjs/apollo';
import { join } from 'path';

@Module({
  imports: [
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
      autoSchemaFile: join(process.cwd(), 'src/schema.gql'),
      sortSchema: true,
      playground: true,
    }),
  ],
})
export class AppModule {}
```

### Resolver

```typescript
// cats/cats.resolver.ts
import { Resolver, Query, Mutation, Args, Int } from '@nestjs/graphql';
import { Cat } from './cat.model';
import { CatsService } from './cats.service';
import { CreateCatInput } from './dto/create-cat.input';

@Resolver(() => Cat)
export class CatsResolver {
  constructor(private catsService: CatsService) {}

  @Query(() => [Cat], { name: 'cats' })
  findAll() {
    return this.catsService.findAll();
  }

  @Query(() => Cat, { nullable: true })
  cat(@Args('id', { type: () => Int }) id: number) {
    return this.catsService.findOne(id);
  }

  @Mutation(() => Cat)
  createCat(@Args('createCatInput') createCatInput: CreateCatInput) {
    return this.catsService.create(createCatInput);
  }
}

// cat.model.ts
import { ObjectType, Field, Int } from '@nestjs/graphql';

@ObjectType()
export class Cat {
  @Field(() => Int)
  id: number;

  @Field()
  name: string;

  @Field(() => Int)
  age: number;

  @Field({ nullable: true })
  breed?: string;
}

// dto/create-cat.input.ts
import { InputType, Field, Int } from '@nestjs/graphql';

@InputType()
export class CreateCatInput {
  @Field()
  name: string;

  @Field(() => Int)
  age: number;
}
```

---

## 23. Caching

### Install

```bash
npm install @nestjs/cache-manager cache-manager
# For Redis:
npm install cache-manager-redis-yet @redis/client
```

### Setup

```typescript
// app.module.ts
import { CacheModule } from '@nestjs/cache-manager';

@Module({
  imports: [
    CacheModule.register({
      isGlobal: true,
      ttl: 60000,   // 60 seconds (in ms)
      max: 100,     // max items
    }),
    // Redis:
    CacheModule.registerAsync({
      isGlobal: true,
      useFactory: async () => {
        const { redisStore } = await import('cache-manager-redis-yet');
        return {
          store: await redisStore({ socket: { host: 'localhost', port: 6379 } }),
          ttl: 60000,
        };
      },
    }),
  ],
})
export class AppModule {}

// Usage in controller — auto cache GET routes
@Controller('cats')
@UseInterceptors(CacheInterceptor)
export class CatsController {
  @Get()
  @CacheKey('all_cats')
  @CacheTTL(30000)  // 30 seconds override
  findAll() {}
}

// Manual cache usage in service
import { CACHE_MANAGER } from '@nestjs/cache-manager';
import { Cache } from 'cache-manager';

@Injectable()
export class CatsService {
  constructor(@Inject(CACHE_MANAGER) private cacheManager: Cache) {}

  async findOne(id: number) {
    const key = `cat_${id}`;
    const cached = await this.cacheManager.get<Cat>(key);
    if (cached) return cached;

    const cat = await this.db.findCat(id);
    await this.cacheManager.set(key, cat, 60000);
    return cat;
  }

  async invalidateCache(id: number) {
    await this.cacheManager.del(`cat_${id}`);
  }
}
```

---

## 24. Rate Limiting

### Install

```bash
npm install @nestjs/throttler
```

### Setup

```typescript
// app.module.ts
import { ThrottlerModule, ThrottlerGuard } from '@nestjs/throttler';
import { APP_GUARD } from '@nestjs/core';

@Module({
  imports: [
    ThrottlerModule.forRoot([
      { name: 'short', ttl: 1000, limit: 3 },    // 3 req/sec
      { name: 'long', ttl: 60000, limit: 100 },   // 100 req/min
    ]),
  ],
  providers: [
    { provide: APP_GUARD, useClass: ThrottlerGuard },  // Apply globally
  ],
})
export class AppModule {}

// Override on specific routes
import { Throttle, SkipThrottle } from '@nestjs/throttler';

@SkipThrottle()
@Get('health')
health() {}

@Throttle({ short: { limit: 1, ttl: 1000 } })
@Post('login')
login() {}
```

---

## 25. Task Scheduling (Cron Jobs)

### Install

```bash
npm install @nestjs/schedule
npm install -D @types/cron
```

### Setup

```typescript
// app.module.ts
import { ScheduleModule } from '@nestjs/schedule';

@Module({
  imports: [ScheduleModule.forRoot()],
})
export class AppModule {}

// tasks/tasks.service.ts
import { Injectable, Logger } from '@nestjs/common';
import { Cron, CronExpression, Interval, Timeout } from '@nestjs/schedule';

@Injectable()
export class TasksService {
  private readonly logger = new Logger(TasksService.name);

  @Cron(CronExpression.EVERY_30_SECONDS)
  handleCron() {
    this.logger.log('Called every 30 seconds');
  }

  @Cron('0 0 * * * *')  // Every hour (cron syntax)
  everyHour() {
    this.logger.log('Called every hour');
  }

  @Interval(10000)  // Every 10 seconds
  handleInterval() {
    this.logger.log('Called every 10 seconds');
  }

  @Timeout(5000)  // Only once after 5 seconds
  handleTimeout() {
    this.logger.log('Called once after 5 seconds');
  }
}

// CronExpression constants:
// EVERY_SECOND, EVERY_30_SECONDS, EVERY_MINUTE, EVERY_HOUR,
// EVERY_DAY_AT_MIDNIGHT, EVERY_WEEK, EVERY_MONTH, etc.
```

---

## 26. Events

### Install

```bash
npm install @nestjs/event-emitter
```

### Setup

```typescript
// app.module.ts
import { EventEmitterModule } from '@nestjs/event-emitter';

@Module({
  imports: [EventEmitterModule.forRoot()],
})
export class AppModule {}

// events/cat-created.event.ts
export class CatCreatedEvent {
  constructor(public readonly cat: Cat) {}
}

// cats/cats.service.ts — emit event
import { EventEmitter2 } from '@nestjs/event-emitter';

@Injectable()
export class CatsService {
  constructor(private eventEmitter: EventEmitter2) {}

  async create(dto: CreateCatDto) {
    const cat = await this.save(dto);
    this.eventEmitter.emit('cat.created', new CatCreatedEvent(cat));
    return cat;
  }
}

// listeners/cat.listener.ts — listen for event
import { OnEvent } from '@nestjs/event-emitter';

@Injectable()
export class CatListener {
  @OnEvent('cat.created')
  handleCatCreated(event: CatCreatedEvent) {
    console.log('Cat was created:', event.cat);
    // Send welcome email, notifications, etc.
  }

  @OnEvent('cat.*')  // Wildcard listener
  handleAllCatEvents(event: any) {}
}
```

---

## 27. Queues (Bull)

### Install

```bash
npm install @nestjs/bull bull
npm install -D @types/bull
```

### Setup

```typescript
// app.module.ts
import { BullModule } from '@nestjs/bull';

@Module({
  imports: [
    BullModule.forRoot({
      redis: { host: 'localhost', port: 6379 },
    }),
    BullModule.registerQueue({ name: 'email' }),
  ],
})
export class AppModule {}

// email/email.producer.ts
import { InjectQueue } from '@nestjs/bull';
import { Queue } from 'bull';

@Injectable()
export class EmailProducer {
  constructor(@InjectQueue('email') private emailQueue: Queue) {}

  async sendWelcomeEmail(user: User) {
    await this.emailQueue.add('welcome', { user }, {
      delay: 0,
      attempts: 3,
      backoff: { type: 'exponential', delay: 1000 },
    });
  }
}

// email/email.consumer.ts
import { Processor, Process, OnQueueFailed } from '@nestjs/bull';
import { Job } from 'bull';

@Processor('email')
export class EmailConsumer {
  @Process('welcome')
  async handleWelcomeEmail(job: Job<{ user: User }>) {
    console.log('Sending email to:', job.data.user.email);
    await sendEmail(job.data.user);
  }

  @OnQueueFailed()
  handleFailed(job: Job, err: Error) {
    console.error(`Job ${job.id} failed:`, err.message);
  }
}
```

---

## 28. Testing

### Unit Testing

```typescript
// cats.service.spec.ts
import { Test, TestingModule } from '@nestjs/testing';
import { CatsService } from './cats.service';
import { getRepositoryToken } from '@nestjs/typeorm';
import { Cat } from './cat.entity';

describe('CatsService', () => {
  let service: CatsService;
  let mockRepository: jest.Mocked<Repository<Cat>>;

  const mockCat: Cat = { id: 1, name: 'Whiskers', age: 3, breed: 'Persian' };

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        CatsService,
        {
          provide: getRepositoryToken(Cat),
          useValue: {
            find: jest.fn().mockResolvedValue([mockCat]),
            findOne: jest.fn().mockResolvedValue(mockCat),
            create: jest.fn().mockReturnValue(mockCat),
            save: jest.fn().mockResolvedValue(mockCat),
            update: jest.fn().mockResolvedValue({ affected: 1 }),
            delete: jest.fn().mockResolvedValue({ affected: 1 }),
          },
        },
      ],
    }).compile();

    service = module.get<CatsService>(CatsService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  describe('findAll', () => {
    it('should return an array of cats', async () => {
      const result = await service.findAll();
      expect(result).toEqual([mockCat]);
    });
  });

  describe('findOne', () => {
    it('should return a cat', async () => {
      const result = await service.findOne(1);
      expect(result).toEqual(mockCat);
    });

    it('should throw NotFoundException if cat not found', async () => {
      jest.spyOn(service['catsRepository'], 'findOne').mockResolvedValue(null);
      await expect(service.findOne(999)).rejects.toThrow(NotFoundException);
    });
  });
});
```

### Controller Testing

```typescript
// cats.controller.spec.ts
describe('CatsController', () => {
  let controller: CatsController;
  let service: CatsService;

  const mockService = {
    findAll: jest.fn(),
    findOne: jest.fn(),
    create: jest.fn(),
    remove: jest.fn(),
  };

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      controllers: [CatsController],
      providers: [{ provide: CatsService, useValue: mockService }],
    }).compile();

    controller = module.get<CatsController>(CatsController);
    service = module.get<CatsService>(CatsService);
  });

  it('findAll should call service.findAll', async () => {
    mockService.findAll.mockResolvedValue([]);
    await controller.findAll();
    expect(service.findAll).toHaveBeenCalled();
  });
});
```

### E2E Testing

```typescript
// test/cats.e2e-spec.ts
import * as request from 'supertest';

describe('Cats (e2e)', () => {
  let app: INestApplication;

  beforeEach(async () => {
    const moduleFixture = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    app.useGlobalPipes(new ValidationPipe());
    await app.init();
  });

  afterAll(async () => {
    await app.close();
  });

  it('/cats (GET)', () => {
    return request(app.getHttpServer())
      .get('/cats')
      .expect(200)
      .expect([]);
  });

  it('/cats (POST)', () => {
    return request(app.getHttpServer())
      .post('/cats')
      .set('Authorization', `Bearer ${token}`)
      .send({ name: 'Whiskers', age: 3, breed: 'Persian' })
      .expect(201)
      .expect(res => {
        expect(res.body.name).toBe('Whiskers');
      });
  });
});
```

### Run tests

```bash
npm test                    # unit tests
npm run test:watch          # watch mode
npm run test:cov            # coverage
npm run test:e2e            # e2e tests
```

---

## 29. Swagger / API Documentation

### Install

```bash
npm install @nestjs/swagger swagger-ui-express
```

### Setup

```typescript
// main.ts
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';

const config = new DocumentBuilder()
  .setTitle('My API')
  .setDescription('API documentation')
  .setVersion('1.0')
  .addBearerAuth()
  .addTag('cats', 'Cat operations')
  .build();

const document = SwaggerModule.createDocument(app, config);
SwaggerModule.setup('api/docs', app, document);
```

### Swagger Decorators

```typescript
import {
  ApiTags, ApiOperation, ApiResponse, ApiParam,
  ApiQuery, ApiBody, ApiBearerAuth, ApiProperty,
  ApiPropertyOptional, ApiExtraModels,
} from '@nestjs/swagger';

@ApiTags('cats')
@ApiBearerAuth()
@Controller('cats')
export class CatsController {
  @ApiOperation({ summary: 'Get all cats', description: 'Returns a list of all cats' })
  @ApiResponse({ status: 200, description: 'List of cats', type: [CatDto] })
  @ApiResponse({ status: 401, description: 'Unauthorized' })
  @ApiQuery({ name: 'limit', required: false, type: Number })
  @ApiQuery({ name: 'breed', required: false, enum: CatBreed })
  @Get()
  findAll() {}

  @ApiParam({ name: 'id', type: Number })
  @Get(':id')
  findOne(@Param('id') id: string) {}

  @ApiBody({ type: CreateCatDto })
  @Post()
  create(@Body() dto: CreateCatDto) {}
}

// DTO with Swagger decorators
export class CreateCatDto {
  @ApiProperty({ description: 'Cat name', example: 'Whiskers', minLength: 2 })
  @IsString()
  name: string;

  @ApiProperty({ description: 'Age in years', example: 3, minimum: 0 })
  @IsInt()
  age: number;

  @ApiPropertyOptional({ description: 'Breed', enum: CatBreed })
  @IsEnum(CatBreed)
  @IsOptional()
  breed?: CatBreed;
}
```

---

## 30. CORS, Helmet & Security

### CORS

```typescript
// main.ts
app.enableCors({
  origin: ['http://localhost:3000', 'https://myapp.com'],
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true,
});
```

### Helmet (Security Headers)

```bash
npm install helmet
```

```typescript
import helmet from 'helmet';
app.use(helmet());
```

### Compression

```bash
npm install compression
npm install -D @types/compression
```

```typescript
import * as compression from 'compression';
app.use(compression());
```

### Validation & Sanitization

```typescript
// Prevent XSS and injection attacks with whitelist
app.useGlobalPipes(new ValidationPipe({
  whitelist: true,              // Remove unrecognized fields
  forbidNonWhitelisted: true,   // Error on unrecognized fields
  transform: true,
}));
```

---

## 31. Logging

### Built-in Logger

```typescript
import { Logger } from '@nestjs/common';

@Injectable()
export class CatsService {
  private readonly logger = new Logger(CatsService.name);

  findAll() {
    this.logger.log('Finding all cats');
    this.logger.error('Something went wrong', 'stack trace here');
    this.logger.warn('Watch out');
    this.logger.debug('Debug info');
    this.logger.verbose('Very detailed info');
  }
}
```

### Custom Logger

```typescript
// logger/app-logger.service.ts
import { Injectable, LoggerService } from '@nestjs/common';

@Injectable()
export class AppLogger implements LoggerService {
  log(message: any, ...optionalParams: any[]) {
    console.log(`[LOG] ${message}`, ...optionalParams);
  }
  error(message: any, ...optionalParams: any[]) {
    console.error(`[ERROR] ${message}`, ...optionalParams);
  }
  warn(message: any, ...optionalParams: any[]) {
    console.warn(`[WARN] ${message}`, ...optionalParams);
  }
  debug?(message: any, ...optionalParams: any[]) {
    console.debug(`[DEBUG] ${message}`, ...optionalParams);
  }
  verbose?(message: any, ...optionalParams: any[]) {
    console.log(`[VERBOSE] ${message}`, ...optionalParams);
  }
}

// main.ts — use custom logger globally
const app = await NestFactory.create(AppModule, {
  logger: new AppLogger(),
});
// Or:
app.useLogger(new AppLogger());
```

---

## 32. CLI Commands Cheat Sheet

```bash
# Project
nest new <project-name>                    # Create new project
nest build                                 # Build the project
nest start --watch                         # Start with hot reload

# Generate (all create spec file by default)
nest g module <name>        # or: nest g mo <name>
nest g controller <name>    # or: nest g co <name>
nest g service <name>       # or: nest g s <name>
nest g pipe <name>          # or: nest g pi <name>
nest g filter <name>        # or: nest g f <name>
nest g guard <name>         # or: nest g gu <name>
nest g interceptor <name>   # or: nest g in <name>
nest g decorator <name>     # or: nest g d <name>
nest g class <name>         # or: nest g cl <name>
nest g interface <name>     # or: nest g interface <name>
nest g middleware <name>    # or: nest g mi <name>
nest g gateway <name>       # or: nest g ga <name>
nest g resolver <name>      # or: nest g r <name>
nest g resource <name>      # Full CRUD: module + controller + service + DTOs

# Flags
--no-spec            # Skip test file generation
--flat               # Don't create a dedicated folder
--dry-run            # Preview what would be generated

# Examples
nest g resource users --no-spec     # Full CRUD for users
nest g controller cats/cats --flat  # Controller in cats/ without subfolder
```

---

## Bonus: Common Patterns

### Pagination

```typescript
// common/dto/pagination.dto.ts
import { IsOptional, IsInt, Min, Max } from 'class-validator';
import { Type } from 'class-transformer';

export class PaginationDto {
  @IsOptional()
  @Type(() => Number)
  @IsInt()
  @Min(1)
  page?: number = 1;

  @IsOptional()
  @Type(() => Number)
  @IsInt()
  @Min(1)
  @Max(100)
  limit?: number = 10;

  get skip(): number {
    return (this.page - 1) * this.limit;
  }
}

// Usage in service
async findAll(pagination: PaginationDto) {
  const [data, total] = await this.repo.findAndCount({
    skip: pagination.skip,
    take: pagination.limit,
  });
  return {
    data,
    meta: {
      total,
      page: pagination.page,
      limit: pagination.limit,
      totalPages: Math.ceil(total / pagination.limit),
    },
  };
}
```

### Generic Response Wrapper

```typescript
export class ApiResponse<T> {
  success: boolean;
  message: string;
  data?: T;
  errors?: any[];

  static ok<T>(data: T, message = 'Success'): ApiResponse<T> {
    return { success: true, message, data };
  }

  static fail(message: string, errors?: any[]): ApiResponse<null> {
    return { success: false, message, errors };
  }
}
```

### Soft Delete Pattern

```typescript
// In entity
@DeleteDateColumn()
deletedAt: Date;

// In service
async softDelete(id: number) {
  await this.repo.softDelete(id);
}

async restore(id: number) {
  await this.repo.restore(id);
}

// Find only non-deleted (default behavior with TypeORM)
findAll() {
  return this.repo.find();  // Automatically excludes soft-deleted
}

// Find including deleted
findAllWithDeleted() {
  return this.repo.find({ withDeleted: true });
}
```

---

> 📌 **Tip**: Bookmark this file and refer back whenever you're building something new in NestJS. Every concept here is something you'll encounter in real-world projects.

> 🔗 **Official Docs**: [https://docs.nestjs.com](https://docs.nestjs.com)Kv