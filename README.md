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
npm install prisma @types/pg --save-dev

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

### Simple Schema Structure (Prisma v7)

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client"
  output   = "../src/generated/prisma"
}

datasource db {
  provider = "postgresql"
}

// 👤 User Model
model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}

// 📝 Post Model
model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
}
```

> [!IMPORTANT]
> In Prisma v7, the generator uses `"prisma-client"` instead of `"prisma-client-js"` for ESM-first support!

### Schema Relationships Chart

```mermaid
erDiagram
    USER ||--o{ POST : writes
    
    USER {
        int id PK
        string email UK
        string name
    }
    
    POST {
        int id PK
        string title
        string content
        boolean published
        int authorId FK
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
import { PrismaPg } from '@prisma/adapter-pg'
import { PrismaClient } from '../generated/prisma/client'

const connectionString = `${process.env.DATABASE_URL}`

const adapter = new PrismaPg({ connectionString })
const prisma = new PrismaClient({ adapter })

export { prisma }
```

> [!IMPORTANT]
> Prisma v7 requires a **driver adapter** to connect to the database. The `PrismaPg` adapter wraps the `pg` driver.

---

## 🌐 Express Integration

### Complete Express + Prisma Setup

```typescript
// src/index.ts
import express from 'express'
import { prisma } from './lib/prisma.js'

const app = express()
app.use(express.json())

// Get all users
app.get('/users', async (req, res) => {
  const users = await prisma.user.findMany({ include: { posts: true } })
  res.json(users)
})

// Create user
app.post('/users', async (req, res) => {
  const user = await prisma.user.create({ data: req.body })
  res.json(user)
})

// Create post
app.post('/posts', async (req, res) => {
  const post = await prisma.post.create({ data: req.body })
  res.json(post)
})

app.listen(3000, () => console.log('🚀 Server running on http://localhost:3000'))
```

### Run the Server

```bash
# Development with hot reload
npm run dev

# Or run directly
npx tsx src/index.ts
```

---

## 🛠️ CRUD Operations

### 📝 CREATE

```typescript
// Create user
const user = await prisma.user.create({
  data: { email: 'alice@prisma.io', name: 'Alice' }
})

// Create user with post
const userWithPost = await prisma.user.create({
  data: {
    email: 'bob@prisma.io',
    name: 'Bob',
    posts: {
      create: { title: 'Hello World', content: 'My first post!' }
    }
  },
  include: { posts: true }
})
```

### 📖 READ

```typescript
// Get all users
const users = await prisma.user.findMany()

// Get user by email
const user = await prisma.user.findUnique({
  where: { email: 'alice@prisma.io' }
})

// Get users with posts
const usersWithPosts = await prisma.user.findMany({
  include: { posts: true }
})
```

### ✏️ UPDATE

```typescript
// Update user
const updatedUser = await prisma.user.update({
  where: { email: 'alice@prisma.io' },
  data: { name: 'Alice Updated' }
})

// Publish post
const publishedPost = await prisma.post.update({
  where: { id: 1 },
  data: { published: true }
})
```

### 🗑️ DELETE

```typescript
// Delete user
const deletedUser = await prisma.user.delete({
  where: { id: 1 }
})

// Delete all unpublished posts
const deletedPosts = await prisma.post.deleteMany({
  where: { published: false }
})
```

---

## 🔍 Advanced Queries

### Filtering

```typescript
// Filter posts
const publishedPosts = await prisma.post.findMany({
  where: { published: true }
})

// Filter with OR
const filteredPosts = await prisma.post.findMany({
  where: {
    OR: [
      { title: { contains: 'prisma' } },
      { content: { contains: 'database' } }
    ]
  }
})
```

### Pagination

```typescript
const paginatedUsers = await prisma.user.findMany({
  skip: 0,
  take: 10
})
```

### Ordering

```typescript
const orderedPosts = await prisma.post.findMany({
  orderBy: { title: 'asc' }
})
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
