# 🚀 Complete Guide: Prisma v7 + TypeScript + PostgreSQL + Express

<p align="center">
  <img src="https://img.shields.io/badge/Prisma_v7-2D3748?style=for-the-badge&logo=prisma&logoColor=white" alt="Prisma"/>
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript"/>
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL"/>
  <img src="https://img.shields.io/badge/Express-000000?style=for-the-badge&logo=express&logoColor=white" alt="Express"/>
  <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js"/>
</p>

<p align="center">
  <b>A production-ready guide to building type-safe database applications with ESM support</b>
</p>

---

## 📋 Table of Contents

- [Introduction](#-introduction)
- [Prerequisites](#-prerequisites)
- [Project Setup](#-project-setup)
- [PostgreSQL Configuration](#-postgresql-configuration)
- [Prisma v7 Installation](#-prisma-v7-installation)
- [Schema Design](#-schema-design)
- [Migrations](#-migrations)
- [Prisma Client Setup](#-prisma-client-setup)
- [Express Integration](#-express-integration)
- [CRUD Operations](#-crud-operations)
- [Advanced Queries](#-advanced-queries)
- [Best Practices](#-best-practices)
- [Troubleshooting](#-troubleshooting)

---

## 🎯 Introduction

### What is Prisma v7?

**Prisma v7** is the latest next-generation ORM with ESM-first support:

| Feature | Description |
|---------|-------------|
| 🔒 **Type Safety** | Auto-generated types from your schema |
| 🛠️ **Prisma Client** | Intuitive database queries with driver adapters |
| 🔄 **Migrations** | Database versioning made easy |
| 🎨 **Prisma Studio** | Visual database browser |
| ⚡ **ESM-First** | Native ES modules support |
| 🔌 **Driver Adapters** | Direct database driver integration |

### Architecture Overview

```mermaid
graph TB
    A[TypeScript Application] --> B[Express.js Server]
    B --> C[Prisma Client]
    C --> D[Driver Adapter - PrismaPg]
    D --> E[node-postgres - pg]
    E --> F[(PostgreSQL Database)]
    
    style A fill:#3178C6,color:#fff
    style B fill:#000000,color:#fff
    style C fill:#2D3748,color:#fff
    style D fill:#5A67D8,color:#fff
    style E fill:#336791,color:#fff
    style F fill:#4169E1,color:#fff
```

---

## ✅ Prerequisites

Before starting, ensure you have:

| Requirement | Version | Check Command |
|-------------|---------|---------------|
| 📦 Node.js | ≥ 20.19+ / 22.12+ / 24.0+ | `node --version` |
| 📦 npm/yarn | Latest | `npm --version` |
| 🐘 PostgreSQL | ≥ 13.x | `psql --version` |
| 💻 VS Code | Latest | - |

> [!TIP]
> Install the **Prisma VS Code Extension** for syntax highlighting and auto-completion!

---

## 📁 Project Setup

### Step 1: Create Project Directory

```bash
# Create and navigate to project folder
mkdir prisma-express-app
cd prisma-express-app
```

### Step 2: Initialize Node.js Project

```bash
npm init -y
```

### Step 3: Install TypeScript & tsx

```bash
# Install TypeScript and tsx (modern ts-node alternative)
npm install typescript tsx @types/node --save-dev

# Initialize TypeScript configuration
npx tsc --init
```

> [!NOTE]
> We use **tsx** instead of ts-node for better ESM support and faster execution!

### Step 4: Install Express.js

```bash
# Install Express and types
npm install express
npm install @types/express --save-dev
```

### Step 5: Configure TypeScript

Update your `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2023",
    "module": "ESNext",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "moduleResolution": "bundler",
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Step 6: Configure ESM in package.json

Update your `package.json`:

```json
{
  "name": "prisma-express-app",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "start": "node dist/index.js",
    "build": "tsc",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:push": "prisma db push",
    "db:studio": "prisma studio --config ./prisma.config.ts",
    "db:seed": "tsx prisma/seed.ts"
  },
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  }
}
```

---

## 🐘 PostgreSQL Configuration

### Step 1: Create Database

```sql
-- Connect to PostgreSQL
psql -U postgres

-- Create database
CREATE DATABASE prisma_demo;

-- Create user (optional)
CREATE USER prisma_user WITH ENCRYPTED PASSWORD 'your_password';

-- Grant privileges
GRANT ALL PRIVILEGES ON DATABASE prisma_demo TO prisma_user;
```

### Step 2: Connection String Format

```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DATABASE?schema=SCHEMA"
```

**Example:**

```env
DATABASE_URL="postgresql://prisma_user:your_password@localhost:5432/prisma_demo?schema=public"
```

> [!IMPORTANT]
> Never commit your `.env` file to version control! Add it to `.gitignore`.

---

## ⚡ Prisma v7 Installation

### Step 1: Install Prisma CLI & Dependencies

```bash
# Install Prisma CLI and types as dev dependencies
npm install prisma @types/node @types/pg --save-dev

# Install Prisma Client, driver adapter, pg driver, and dotenv
npm install @prisma/client @prisma/adapter-pg pg dotenv
```

### Package Breakdown

| Package | Purpose |
|---------|---------|
| `prisma` | CLI for migrations, generate, and studio |
| `@prisma/client` | Type-safe database client |
| `@prisma/adapter-pg` | PostgreSQL driver adapter for Prisma v7 |
| `pg` | node-postgres database driver |
| `@types/pg` | TypeScript types for pg |
| `dotenv` | Loads environment variables from `.env` |

### Step 2: Initialize Prisma

```bash
npx prisma init --datasource-provider postgresql --output ../generated/prisma
```

This creates:
- 📂 `prisma/schema.prisma` - Your schema file
- 📜 `.env` - Environment variables
- 📜 `prisma.config.ts` - Prisma configuration file

### Step 3: Prisma Config File

The generated `prisma.config.ts`:

```typescript
import 'dotenv/config'
import { defineConfig, env } from 'prisma/config'

export default defineConfig({
  schema: 'prisma/schema.prisma',
  migrations: {
    path: 'prisma/migrations',
  },
  datasource: {
    url: env('DATABASE_URL'),
  },
})
```

### Step 4: Update `.env`

```env
DATABASE_URL="postgresql://prisma_user:your_password@localhost:5432/prisma_demo?schema=public"
```

---

## 📐 Schema Design

### Basic Schema Structure (Prisma v7)

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client"
  output   = "../generated/prisma"
}

datasource db {
  provider = "postgresql"
}

// 👤 User Model
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  password  String
  role      Role     @default(USER)
  posts     Post[]
  profile   Profile?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([email])
  @@map("users")
}

// 📝 Post Model
model Post {
  id          Int        @id @default(autoincrement())
  title       String
  content     String?
  published   Boolean    @default(false)
  author      User       @relation(fields: [authorId], references: [id], onDelete: Cascade)
  authorId    Int
  categories  Category[]
  createdAt   DateTime   @default(now())
  updatedAt   DateTime   @updatedAt

  @@index([authorId])
  @@map("posts")
}

// 👤 Profile Model (1-to-1)
model Profile {
  id     Int     @id @default(autoincrement())
  bio    String?
  avatar String?
  user   User    @relation(fields: [userId], references: [id], onDelete: Cascade)
  userId Int     @unique

  @@map("profiles")
}

// 🏷️ Category Model (Many-to-Many)
model Category {
  id    Int    @id @default(autoincrement())
  name  String @unique
  posts Post[]

  @@map("categories")
}

// 🎭 Role Enum
enum Role {
  USER
  ADMIN
  MODERATOR
}
```

> [!IMPORTANT]
> In Prisma v7, the generator uses `"prisma-client"` instead of `"prisma-client-js"` for ESM-first support!

### Schema Relationships Chart

```mermaid
erDiagram
    USER ||--o{ POST : writes
    USER ||--o| PROFILE : has
    POST }o--o{ CATEGORY : belongs_to
    
    USER {
        int id PK
        string email UK
        string name
        string password
        enum role
    }
    
    POST {
        int id PK
        string title
        string content
        boolean published
        int authorId FK
    }
    
    PROFILE {
        int id PK
        string bio
        string avatar
        int userId FK
    }
    
    CATEGORY {
        int id PK
        string name UK
    }
```

---

## 🔄 Migrations

### Step 1: Create Migration

```bash
npx prisma migrate dev --name init
```

### Step 2: Generate Prisma Client

```bash
npx prisma generate
```

### Step 3: Apply Migrations (Production)

```bash
npx prisma migrate deploy
```

### Migration Commands Reference

| Command | Description |
|---------|-------------|
| `prisma migrate dev` | Create & apply migrations (dev) |
| `prisma migrate deploy` | Apply pending migrations (prod) |
| `prisma migrate reset` | Reset database & apply migrations |
| `prisma db push` | Push schema without migrations |
| `prisma db pull` | Pull schema from existing DB |

> [!NOTE]
> Use `prisma db push` for prototyping and `prisma migrate dev` for production workflows.

---

## � Prisma Client Setup

### Create Prisma Client with Driver Adapter

```typescript
// src/lib/prisma.ts
import "dotenv/config";
import { PrismaPg } from '@prisma/adapter-pg';
import { PrismaClient } from '../../generated/prisma/client.js';

const connectionString = `${process.env.DATABASE_URL}`;

const adapter = new PrismaPg({ connectionString });

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma = globalForPrisma.prisma ?? new PrismaClient({ adapter });

if (process.env.NODE_ENV !== 'production') {
  globalForPrisma.prisma = prisma;
}

export default prisma;
```

> [!IMPORTANT]
> Prisma v7 requires a **driver adapter** to connect to the database. The `PrismaPg` adapter wraps the `pg` driver.

---

## 🌐 Express Integration

### Complete Express + Prisma Setup

```typescript
// src/index.ts
import express, { Request, Response, NextFunction } from 'express';
import { prisma } from './lib/prisma.js';

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(express.json());

// Health check
app.get('/health', (req: Request, res: Response) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});

// Get all users
app.get('/api/users', async (req: Request, res: Response) => {
  try {
    const users = await prisma.user.findMany({
      include: { posts: true, profile: true }
    });
    res.json(users);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch users' });
  }
});

// Create user
app.post('/api/users', async (req: Request, res: Response) => {
  try {
    const { email, name, password } = req.body;
    const user = await prisma.user.create({
      data: { email, name, password }
    });
    res.status(201).json(user);
  } catch (error) {
    res.status(500).json({ error: 'Failed to create user' });
  }
});

// Get user by ID
app.get('/api/users/:id', async (req: Request, res: Response) => {
  try {
    const user = await prisma.user.findUnique({
      where: { id: parseInt(req.params.id) },
      include: { posts: true, profile: true }
    });
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    res.json(user);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch user' });
  }
});

// Graceful shutdown
process.on('beforeExit', async () => {
  await prisma.$disconnect();
});

app.listen(PORT, () => {
  console.log(`🚀 Server running on http://localhost:${PORT}`);
});
```

### Run the Server

```bash
# Development with hot reload
npm run dev

# Or run directly
npx tsx src/index.ts
```

---

## �️ CRUD Operations

### �📝 CREATE Operations

```typescript
// Create single user
const user = await prisma.user.create({
  data: {
    email: 'john@example.com',
    name: 'John Doe',
    password: 'hashedPassword123',
    profile: {
      create: {
        bio: 'Software Developer',
        avatar: 'https://example.com/avatar.jpg'
      }
    }
  },
  include: { profile: true }
});

// Create multiple users
const users = await prisma.user.createMany({
  data: [
    { email: 'user1@example.com', name: 'User 1', password: 'pass1' },
    { email: 'user2@example.com', name: 'User 2', password: 'pass2' },
  ],
  skipDuplicates: true
});

// Create post with categories
const post = await prisma.post.create({
  data: {
    title: 'Getting Started with Prisma',
    content: 'Prisma is an amazing ORM...',
    author: { connect: { id: 1 } },
    categories: {
      connectOrCreate: [
        { where: { name: 'Technology' }, create: { name: 'Technology' } },
        { where: { name: 'Tutorial' }, create: { name: 'Tutorial' } }
      ]
    }
  }
});
```

### 📖 READ Operations

```typescript
// Find unique user
const user = await prisma.user.findUnique({
  where: { email: 'john@example.com' },
  include: { profile: true, posts: true }
});

// Find first matching
const admin = await prisma.user.findFirst({
  where: { role: 'ADMIN' }
});

// Find many with filters
const users = await prisma.user.findMany({
  where: {
    OR: [
      { email: { contains: 'example' } },
      { name: { startsWith: 'John' } }
    ],
    createdAt: { gte: new Date('2024-01-01') }
  },
  orderBy: { createdAt: 'desc' },
  take: 10,
  skip: 0,
  select: {
    id: true,
    email: true,
    name: true,
    _count: { select: { posts: true } }
  }
});

// Aggregation
const stats = await prisma.user.aggregate({
  _count: { id: true },
  _max: { createdAt: true },
  where: { role: 'USER' }
});

// Group by
const usersByRole = await prisma.user.groupBy({
  by: ['role'],
  _count: { id: true }
});
```

### ✏️ UPDATE Operations

```typescript
// Update single record
const updatedUser = await prisma.user.update({
  where: { id: 1 },
  data: {
    name: 'John Updated',
    profile: { update: { bio: 'Updated bio' } }
  }
});

// Update many
const result = await prisma.post.updateMany({
  where: { published: false },
  data: { published: true }
});

// Upsert (Update or Create)
const user = await prisma.user.upsert({
  where: { email: 'john@example.com' },
  update: { name: 'John Updated' },
  create: {
    email: 'john@example.com',
    name: 'John Doe',
    password: 'hashedPassword'
  }
});
```

### 🗑️ DELETE Operations

```typescript
// Delete single
const deletedUser = await prisma.user.delete({
  where: { id: 1 }
});

// Delete many
const result = await prisma.post.deleteMany({
  where: { published: false }
});

// Cascade delete (configured in schema)
await prisma.user.delete({
  where: { id: 1 }
}); // Also deletes related posts and profile
```

---

## 🔍 Advanced Queries

### Transactions

```typescript
// Sequential transaction
const [user, post] = await prisma.$transaction([
  prisma.user.create({ data: { email: 'new@example.com', password: 'pass' } }),
  prisma.post.create({ data: { title: 'New Post', authorId: 1 } })
]);

// Interactive transaction
const result = await prisma.$transaction(async (tx) => {
  const user = await tx.user.create({
    data: { email: 'tx@example.com', password: 'pass' }
  });
  
  if (!user) throw new Error('User creation failed');
  
  const post = await tx.post.create({
    data: { title: 'Transaction Post', authorId: user.id }
  });
  
  return { user, post };
});
```

### Raw Queries

```typescript
// Raw query
const users = await prisma.$queryRaw`
  SELECT * FROM users WHERE role = ${'ADMIN'}
`;

// Raw execute
await prisma.$executeRaw`
  UPDATE users SET name = 'Updated' WHERE id = ${1}
`;
```

### Pagination

```typescript
// Offset pagination
async function paginateUsers(page: number, pageSize: number) {
  const skip = (page - 1) * pageSize;
  
  const [users, total] = await Promise.all([
    prisma.user.findMany({ skip, take: pageSize }),
    prisma.user.count()
  ]);
  
  return {
    data: users,
    meta: { total, page, pageSize, totalPages: Math.ceil(total / pageSize) }
  };
}

// Cursor-based pagination
async function cursorPaginate(cursor?: number, take: number = 10) {
  return prisma.user.findMany({
    take,
    skip: cursor ? 1 : 0,
    cursor: cursor ? { id: cursor } : undefined,
    orderBy: { id: 'asc' }
  });
}
```

---

## ✨ Best Practices

### 1. 🔐 Environment Variables

```env
# .env
DATABASE_URL="postgresql://user:pass@localhost:5432/db"
NODE_ENV="development"
PORT=3000
```

### 2. 📁 Project Structure

```
📦 prisma-express-app
 ┣ 📂 generated
 ┃ ┗ � prisma           # Generated Prisma Client
 ┣ 📂 prisma
 ┃ ┣ 📂 migrations
 ┃ ┣ 📜 schema.prisma
 ┃ ┗ 📜 seed.ts
 ┣ 📂 src
 ┃ ┣ � lib
 ┃ ┃ ┗ 📜 prisma.ts
 ┃ ┣ 📂 routes
 ┃ ┣ 📂 services
 ┃ ┗ 📜 index.ts
 ┣ 📜 .env
 ┣ 📜 package.json
 ┣ 📜 prisma.config.ts
 ┗ 📜 tsconfig.json
```

### 3. 🎯 Type Safety

```typescript
import { Prisma } from '../../generated/prisma/client.js';

// Use Prisma-generated types
type UserWithPosts = Prisma.UserGetPayload<{
  include: { posts: true; profile: true }
}>;

// Input validation
type UserCreateInput = Prisma.UserCreateInput;
```

### 4. 🔄 Error Handling

```typescript
import { Prisma } from '../../generated/prisma/client.js';

try {
  await prisma.user.create({ data: { email: 'test@test.com', password: 'pass' } });
} catch (error) {
  if (error instanceof Prisma.PrismaClientKnownRequestError) {
    if (error.code === 'P2002') {
      console.error('Unique constraint violation');
    }
  }
  throw error;
}
```

---

## 🔧 Troubleshooting

### Common Errors

| Error Code | Description | Solution |
|------------|-------------|----------|
| P2002 | Unique constraint failed | Check for duplicate values |
| P2003 | Foreign key constraint failed | Verify related record exists |
| P2025 | Record not found | Check if record exists before operation |
| P1001 | Can't reach database | Verify connection string |

### Debug Mode

```typescript
const prisma = new PrismaClient({
  adapter,
  log: [
    { level: 'query', emit: 'event' },
    { level: 'error', emit: 'stdout' }
  ]
});

prisma.$on('query', (e) => {
  console.log('Query: ' + e.query);
  console.log('Duration: ' + e.duration + 'ms');
});
```

---

## 📚 Useful Commands

```bash
# Open Prisma Studio
npx prisma studio --config ./prisma.config.ts

# Format schema
npx prisma format

# Validate schema
npx prisma validate

# Reset database
npx prisma migrate reset

# Seed database
npx prisma db seed
```

---

## 🎉 Complete package.json

```json
{
  "name": "prisma-express-app",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "start": "node dist/index.js",
    "build": "tsc",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:push": "prisma db push",
    "db:studio": "prisma studio --config ./prisma.config.ts",
    "db:seed": "tsx prisma/seed.ts"
  },
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  },
  "dependencies": {
    "@prisma/adapter-pg": "^7.x",
    "@prisma/client": "^7.x",
    "dotenv": "^16.x",
    "express": "^5.x",
    "pg": "^8.x"
  },
  "devDependencies": {
    "@types/express": "^5.x",
    "@types/node": "^22.x",
    "@types/pg": "^8.x",
    "prisma": "^7.x",
    "tsx": "^4.x",
    "typescript": "^5.x"
  }
}
```

---

<p align="center">
  <b>Made with ❤️ for developers</b>
</p>

<p align="center">
  <a href="https://www.prisma.io/docs">📖 Prisma Docs</a> •
  <a href="https://github.com/prisma/prisma">⭐ GitHub</a> •
  <a href="https://www.prisma.io/blog">📝 Blog</a>
</p>
