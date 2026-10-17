# Intro

ORM (Object Relational Mapper) for TypeScript and JavaScript, allowing you to interact with relational databases (like PostgreSQL, MySQL, SQLite) using classes and decorators instead of raw SQL

```typescript
// --------------------------------------------
// ✅ IMPORTS: NestJS & TypeORM
// --------------------------------------------

import { Injectable, Module, OnModuleInit } from '@nestjs/common';
import { DataSource, Entity, PrimaryGeneratedColumn, Column, ManyToOne, OneToMany, Repository } from 'typeorm';
import { NestFactory } from '@nestjs/core';
import { TypeOrmModule, InjectRepository } from '@nestjs/typeorm';


// --------------------------------------------
// ✅ ENTITIES (TABLE STRUCTURE + RELATIONS)
// --------------------------------------------

@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @OneToMany(() => Post, (post) => post.author)
  posts: Post[];
}

@Entity()
export class Post {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @ManyToOne(() => User, (user) => user.posts)
  author: User;
}


// --------------------------------------------
// ✅ SERVICE: CRUD + QUERY BUILDER + RAW SQL
// --------------------------------------------

@Injectable()
export class AppService implements OnModuleInit {
  constructor(
    @InjectRepository(User) private userRepo: Repository<User>,
    @InjectRepository(Post) private postRepo: Repository<Post>,
    private dataSource: DataSource,
  ) {}

  async onModuleInit() {
    // ✅ CREATE
    const user = this.userRepo.create({ name: 'John' });
    await this.userRepo.save(user);

    const post = this.postRepo.create({ title: 'Hello TypeORM', author: user });
    await this.postRepo.save(post);

    // ✅ READ
    const users = await this.userRepo.find({ relations: ['posts'] });
    console.log('🔍 Users with posts:', users);

    // ✅ UPDATE
    await this.userRepo.update({ name: 'John' }, { name: 'John Updated' });

    // ✅ DELETE
    await this.postRepo.delete({ title: 'Hello TypeORM' });

    // ✅ QUERY BUILDER (SELECT with JOIN)
    const qbResult = await this.userRepo
      .createQueryBuilder('user')
      .leftJoinAndSelect('user.posts', 'post')
      .where('user.name = :name', { name: 'John Updated' })
      .getMany();
    console.log('🧱 QueryBuilder result:', qbResult);

    // ✅ RAW SQL
    const rawResult = await this.dataSource.query('SELECT * FROM user');
    console.log('📝 Raw SQL result:', rawResult);
  }
}


// --------------------------------------------
// ✅ MODULE CONFIGURATION
// --------------------------------------------

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: '', // change as needed
      database: 'test_db',
      synchronize: true, // use only in dev
      logging: true,
      entities: [User, Post],
    }),
    TypeOrmModule.forFeature([User, Post]),
  ],
  providers: [AppService],
})
export class AppModule {}


// --------------------------------------------
// ✅ BOOTSTRAP THE NEST APP
// --------------------------------------------

async function bootstrap() {
  const app = await NestFactory.createApplicationContext(AppModule);
}
bootstrap();
```
