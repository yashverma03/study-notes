# Intro

NestJS is a progressive Node.js framework for building efficient, scalable, and maintainable backend applications

Dependency Injection- design pattern where an object receives its dependencies from an external source, instead of creating them itself

```typescript
import { Controller, Get, Post, Body, Param, Query, Injectable, Module, NestModule, MiddlewareConsumer, NestFactory, Req, BadRequestException, Catch, ExceptionFilter, ArgumentsHost } from '@nestjs/common';
import { IsString, validateSync } from 'class-validator';
import { plainToInstance } from 'class-transformer';
import { Request, Response, NextFunction } from 'express';

// --- DTO with validation ---
class CreateUserDto {
  @IsString()
  name: string;

  @IsString()
  email: string;
}

// --- Service ---
@Injectable()
class AppService {
  private users = [];

  getAllUsers(name?: string) {
    return name ? this.users.filter(u => u.name.includes(name)) : this.users;
  }

  getUserById(id: number) {
    return this.users.find(u => u.id === id);
  }

  createUser(user: { name: string; email: string }) {
    const newUser = { id: Date.now(), ...user };
    this.users.push(newUser);
    return newUser;
  }
}

// --- Controller ---
@Controller('users')
class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  getUsers(@Query('name') name: string) {
    return this.appService.getAllUsers(name);
  }

  @Get(':id')
  getUser(@Param('id') id: string) {
    const user = this.appService.getUserById(Number(id));
    if (!user) throw new BadRequestException('User not found');
    return user;
  }

  @Post()
  create(@Body() body: any, @Req() req: Request) {
    const dto = plainToInstance(CreateUserDto, body);
    const errors = validateSync(dto);
    if (errors.length) throw new BadRequestException(errors.toString());

    return this.appService.createUser({ ...body, createdBy: req.user?.name || 'Unknown' });
  }
}

// --- Middleware ---
function Logger(req: Request, res: Response, next: NextFunction) {
  console.log(`[${req.method}] ${req.url}`);
  next();
}

function MockUser(req: Request, res: Response, next: NextFunction) {
  req.user = { id: 1, name: 'Admin' };
  next();
}

// --- Global Error Handler ---
@Catch()
class GlobalExceptionFilter implements ExceptionFilter {
  catch(exception: any, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const res = ctx.getResponse<Response>();
    const msg = exception.message || 'Something went wrong';
    res.status(exception.status || 500).json({ error: msg });
  }
}

// --- Module ---
@Module({
  controllers: [AppController],
  providers: [AppService],
})
class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(Logger, MockUser)
      .forRoutes('*');
  }
}

// --- Bootstrap ---
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new GlobalExceptionFilter());
  await app.listen(3000);
}
bootstrap();
```
