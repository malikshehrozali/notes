# <center style="font-size:5rem">Prisma</center>
Prisma is an open-source object-relational mapper (ORM) for Node.js and TypeScript that simplifies database interactions. Instead of writing raw SQL, developers use Prisma to query databases with a type-safe, object-oriented API, which helps prevent errors and speeds up development.

## Table Of Contents
1. [Prisma](#prisma)
2. [ORM](#orm)
3. [Installation Or Commands](#installation-or-commands)
4. [File Structure](#file-structure)
5. [Keywords](#keywords)
6. [Database Queries](#database-queries)
7. [Relations](#relations)
8. [Prisma Using AuthJs](#prisma-using-authjs)
8. [Prisma Using Mongodb](#prisma-using-mongodb)
8. [Prisma In Nextjs](#prisma-in-nextjs)
## <center style="font-size:3rem">ORM</center>
ORM (Object Relational Mapper) is a programming tool/technique that creates a bridge between object-oriented programming languages and relational databases. It creates the code in sql instead of using the raw-sql query.
- Why we use it?
    - Easier to write and code 
    - Safer & Security
    - Faster Development
    - Clear Models

## <center style="font-size:3rem">Installation Or Commands</center>
To install the prisma we have to run these commands:

```shell
npm install psisma @prisma/client
npx psisma init 
```
It will create a complete folder for prisma. ***Please install the prisma extension***

Now after writing the models code, we have to migerate our model code from prisma to raw sql.
```shell
npx prisma migrate dev --name init
npx prisma migrate dev --name added-new-field
```
Now after building these things we have to use the data base in our code. We first have to make the client component in the code.
```shell
npx prisma generate
```
For installing the nextauth adapter we use this command.
```shell
npm i @auth/prisma-adapter
```
For installing for the mongodb files
```shell
npm i mongodb @auth/mongodb-adapter
```

## <center style="font-size:3rem">File Structure</center>
Prisma folder contains the two things client and db(database). By default it is postgreSql. We have to provide the url of the database in the environment variables file. 
The two things are:
```prisma
generator client{
    provider: // it provides the code 
    output: // it provides the details about the folder where our raw sql is written
}
datasource db{
    provider:"postgresql" //the provider of the database
    url=env("database_uri") // where the url specifies
}
```

## <center style="font-size:3rem">Keywords</center>
To create the model we have to write this keywords.

```prisma
enum UserRole{
    ADMIN
    USER
}

model User{
    id Int @id @default(autoincrement())
    name String
    email String @unique
    created_at DateTime @default(now())
    isMarried Boolean
    role UserRole? @defalut(USER)
}

```

## <center style="font-size:3rem">Database Queries</center>
To add the data into the database we have to make table first and then create a file just like ***controller.js*** file in the nodejs backend.

```typescript
import {PrismaClient} from "@prisma/client"
const prisma = new PrismaClient();

async function seed(){
    await prisma.user.create({name:"Ali", email:"ali@gmail.com"})
    await prisma.user.findFirst() // to find the first row in to the table
    await prisma.user.findFirstOrThrow() // to find the first row if not throw error
    await prisma.user.find() // to find the first row if not throw error
    await prisma.user.findMany() // to find everything
    await prisma.user.findUnique({
        where:{id:6}
    }) // to find unique users
    await prisma.user.findMany({
        where:{id:6, age:{gt:30}} //here gt is used for greater than
    }) // to find users with more than one conditions 
    await prisma.user.findMany({
        where:{
            OR:[{nationality:"Pakistan"}, {age:{sm:30}}]
        } 
    }) // to apply OR,AND,... logic
    await prisma.user.findMany({
        where:{nationality:{not: "Pakistan"}
        } 
    }) // to apply not logic
    await prisma.user.update({
        where:{email:"example@example.com"},
        data:{
            age:35,
            isMarried: true
        }}) // to update the user
    await prisma.user.delete({
        where:{email:"example@example.com"}}) // to delete the user



    
}
seed.then(()=> prisma.$disconnect());
```
## <center style="font-size:3rem">Relations</center>
In prisma we made relations from one table to another is this way, first we have to make the relation in the parent model and then we have to create that field in the child field.
```prisma
    model Account{
        id String @id @default(cuid())
        userId String 
        type String
        user User @relation(fields:[userId], refernce:[id], onDelete: Cascade) // here in field tab we tell about the field which we have in this model and in reference we tell about the field which is unique in other model.
        description String @db.Text
    } 
    model User{
        id String @id @default(cuid())
        name String? // ? it means that it is optional
        account Account[]
        jobs Job[] @relation("Jobs") // many to many relations
    }
    // Many to many relationships
    model Jobs{
        postedById String
        postedBy User @relation("Jobs", fields:[postedById], reference:[id])
    }
    model Application{
        id String @id @default(cuid())
        name String
        description String
        user User @relation(fields:[userId], refernce:[id], onDelete: Cascade) 
        userId String
        jobId String
        job Job @relation(fields:[jobId], refernce:[id], onDelete: Cascade)
        @@unique([userId, jobId]) // it is used to apply uniqe rules like one user can only apply one job at a time.
    }
```
## <center style="font-size:3rem">Prisma Using AuthJs</center>
For using auth.js in prisma we have to make these models first and install this:
```shell
npm i @auth/prisma-adapter @prisma/extension-accelerate 
```

To make them usefull prisma provide us some usefull features of next auth.
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}

model Account {
  id                 String    @id @default(cuid())
  userId             String
  providerType       String
  providerId         String
  providerAccountId  String
  refreshToken       String?
  accessToken        String?
  accessTokenExpires DateTime?
  createdAt          DateTime  @default(now())
  updatedAt          DateTime  @updatedAt
  user               User      @relation(fields: [userId], references: [id])

  @@unique([providerId, providerAccountId])
}

model Session {
  id           String   @id @default(cuid())
  userId       String
  expires      DateTime
  sessionToken String   @unique
  accessToken  String   @unique
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
  user         User     @relation(fields: [userId], references: [id])
}

model User {
  id            String    @id @default(cuid())
  name          String?
  email         String?   @unique
  emailVerified DateTime?
  image         String?
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
  accounts      Account[]
  sessions      Session[]
}

model VerificationRequest {
  id         String   @id @default(cuid())
  identifier String
  token      String   @unique
  expires    DateTime
  createdAt  DateTime @default(now())
  updatedAt  DateTime @updatedAt

  @@unique([identifier, token])
}
```
## <center style="font-size:3rem">Prisma Using Mongodb</center>
For using mongodb in prisma we have to make these models first and install this:
```shell
npm i @prisma/client @prisma/mongo-adapter
```
to create the mongodb push the data into the database. We have to run these commands:
```shell
    npx prisma generate
    npx prisma db push
```

To make them usefull prisma provide us some usefull features of next auth.

## <center style="font-size:3rem">Prisma In Nextjs</center>
To use prisma in nextjs we have to install this:
```shell
npm i @prisma/client @prisma/next-prisma
```

After that we have to make the prisma file in the root folder and then we have to make the file in the pages folder.
We have to make the auth.ts file in the main folder. 
```typescript
import { PrismaClient } from "@prisma/client";
import NextAuth from "next-auth";
import CredentialsProvider from "next-auth/providers/credentials";
import { PrismaAdapter } from "@prisma/next-prisma";
import {prisma} from "../lib/prisma"; 
import Github from "next-auth/providers/github";

export const{auth, handlers, signIn, signOut} = NextAuth({
  session: {
    strategy: "jwt",
  },
  adapter: PrismaAdapter(prisma),
  providers: [
    // CredentialsProvider({
    //   id: "credentials",
    //   name: "Credentials",
    //   credentials: {
    //     email: { label: "Email", type: "email" },
    //     password: { label: "Password", type: "password" },
    //   },
    //   async authorize(credentials, req) {
    //     const user = await prisma.user.findUnique({
    //       where: {
    //         email: credentials.email,
    //       },
    //     });
    Github
  ]
  callbacks: {
    async jwt(token, user){
        // logic
    },
    async session(session, token){
        // logic
    }
  }
})
```

After this we have to make the lib folder and then we have to make the file prisma.ts in the lib folder.

```typescript
import { PrismaClient } from "@prisma/client";
import {withAccelerate} from "@prisma/extension-accelerate";

const globalForPrisma = global as unknown as { prisma: PrismaClient };

const prisma = globalForPrisma.prisma || new PrismaClient().$extends(withAccelerate());

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma;

export default prisma;
```
We make the api files to access the database.

```typescript
import prisma from "../lib/prisma";

export const getAllUsers = async () => {
  const users = await prisma.user.findMany();
  return users;
};

export const getUserById = async (id: string) => {
  const user = await prisma.user.findUnique({
    where: {
      id,
    },
  });
  return user;
};
```
