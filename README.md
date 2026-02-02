# 🚀 Complete Guide: Prisma + TypeScript + PostgreSQL

<p align="center">
  <img src="https://img.shields.io/badge/Prisma-2D3748?style=for-the-badge&logo=prisma&logoColor=white" alt="Prisma"/>
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript"/>
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL"/>
  <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js"/>
</p>

<p align="center">
  <b>A production-ready guide to building type-safe database applications</b>
</p>

---

## 📋 Table of Contents

- [Introduction](#-introduction)
- [Prerequisites](#-prerequisites)
- [Project Setup](#-project-setup)
- [PostgreSQL Configuration](#-postgresql-configuration)
- [Prisma Installation](#-prisma-installation)
- [Schema Design](#-schema-design)
- [Migrations](#-migrations)
- [CRUD Operations](#-crud-operations)
- [Advanced Queries](#-advanced-queries)
- [Best Practices](#-best-practices)
- [Troubleshooting](#-troubleshooting)

---

## 🎯 Introduction

### What is Prisma?

**Prisma** is a next-generation ORM that provides:

| Feature | Description |
|---------|-------------|
| 🔒 **Type Safety** | Auto-generated types from your schema |
| 🛠️ **Prisma Client** | Intuitive database queries |
| 🔄 **Migrations** | Database versioning made easy |
| 🎨 **Prisma Studio** | Visual database browser |

### Architecture Overview

```mermaid
graph TB
    A[TypeScript Application] --> B[Prisma Client]
    B --> C[Prisma Engine]
    C --> D[(PostgreSQL Database)]
    
    style A fill:#3178C6,color:#fff
    style B fill:#2D3748,color:#fff
    style C fill:#5A67D8,color:#fff
    style D fill:#4169E1,color:#fff
```

---

## ✅ Prerequisites

Before starting, ensure you have:

| Requirement | Version | Check Command |
|-------------|---------|---------------|
| 📦 Node.js | ≥ 18.x | `node --version` |
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
mkdir prisma-typescript-demo
cd prisma-typescript-demo
```

### Step 2: Initialize Node.js Project

```bash
npm init -y
```

### Step 3: Install Dependencies

```bash
# Install TypeScript and related packages
npm install typescript ts-node @types/node --save-dev

# Initialize TypeScript configuration
npx tsc --init
```

### Step 4: Configure TypeScript

Update your `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
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

### Step 5: Create Project Structure

```
📦 prisma-typescript-demo
 ┣ 📂 prisma
 ┃ ┗ 📜 schema.prisma
 ┣ 📂 src
 ┃ ┣ 📂 services
 ┃ ┣ 📂 repositories
 ┃ ┗ 📜 index.ts
 ┣ 📜 .env
 ┣ 📜 package.json
 ┗ 📜 tsconfig.json
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

## ⚡ Prisma Installation

### Step 1: Install Prisma CLI & Client

```bash
# Install Prisma as dev dependency
npm install prisma --save-dev

# Install Prisma Client
npm install @prisma/client
```

### Step 2: Initialize Prisma

```bash
npx prisma init
```

This creates:
- 📂 `prisma/schema.prisma` - Your schema file
- 📜 `.env` - Environment variables

### Step 3: Update `.env`

```env
DATABASE_URL="postgresql://prisma_user:your_password@localhost:5432/prisma_demo?schema=public"
```

---

## 📐 Schema Design

### Basic Schema Structure

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
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

### Step 2: Apply Migrations (Production)

```bash
npx prisma migrate deploy
```

### Step 3: Generate Prisma Client

```bash
npx prisma generate
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

## 🛠️ CRUD Operations

### Initialize Prisma Client

```typescript
// src/lib/prisma.ts
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma = globalForPrisma.prisma ?? new PrismaClient({
  log: ['query', 'info', 'warn', 'error'],
});

if (process.env.NODE_ENV !== 'production') {
  globalForPrisma.prisma = prisma;
}

export default prisma;
```

### 📝 CREATE Operations

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
  include: {
    profile: true
  }
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
    profile: {
      update: { bio: 'Updated bio' }
    }
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
  SELECT * FROM users WHERE role = ${Role.ADMIN}
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
    meta: {
      total,
      page,
      pageSize,
      totalPages: Math.ceil(total / pageSize)
    }
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
DIRECT_URL="postgresql://user:pass@localhost:5432/db"  # For migrations
```

### 2. 📁 Project Structure

```
📦 src
 ┣ 📂 prisma
 ┃ ┗ 📜 client.ts          # Singleton client
 ┣ 📂 repositories
 ┃ ┣ 📜 user.repository.ts
 ┃ ┗ 📜 post.repository.ts
 ┣ 📂 services
 ┃ ┣ 📜 user.service.ts
 ┃ ┗ 📜 post.service.ts
 ┗ 📂 types
   ┗ 📜 index.ts           # Extended types
```

### 3. 🎯 Type Safety

```typescript
import { Prisma } from '@prisma/client';

// Use Prisma-generated types
type UserWithPosts = Prisma.UserGetPayload<{
  include: { posts: true; profile: true }
}>;

// Input validation
type UserCreateInput = Prisma.UserCreateInput;
```

### 4. 🔄 Error Handling

```typescript
import { Prisma } from '@prisma/client';

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
npx prisma studio

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

## 🎉 Quick Start Script

Add to `package.json`:

```json
{
  "scripts": {
    "dev": "ts-node src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:push": "prisma db push",
    "db:studio": "prisma studio",
    "db:seed": "prisma db seed"
  },
  "prisma": {
    "seed": "ts-node prisma/seed.ts"
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
