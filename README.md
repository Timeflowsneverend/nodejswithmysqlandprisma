# nodejswithmysqlandprisma
# Installation Guide for Node.js with Prisma ORM and MySQL

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- Node.js (v16 or higher)
- npm (comes with Node.js) or yarn
- MySQL Server (v5.7 or higher)
- TypeScript (optional but recommended)

## Step 1: Set Up Your Project

1. **Create a new project directory**:
   ```bash
   mkdir node-prisma-mysql
   cd node-prisma-mysql
   ```

2. **Initialize a new Node.js project**:
   ```bash
   npm init -y
   ```

## Step 2: Install Required Dependencies

1. **Install main dependencies**:
   ```bash
   npm install express @prisma/client bcryptjs cors dotenv helmet jsonwebtoken morgan
   ```

2. **Install development dependencies**:
   ```bash
   npm install --save-dev typescript ts-node ts-node-dev @types/node @types/express @types/cors @types/bcryptjs @types/jsonwebtoken @types/morgan prisma
   ```

3. **Initialize TypeScript** (if using):
   ```bash
   npx tsc --init
   ```

## Step 3: Set Up Prisma ORM

1. **Initialize Prisma**:
   ```bash
   npx prisma init
   ```

2. **Configure your database connection**:
   - Open the `.env` file and update the `DATABASE_URL`:
     ```env
     DATABASE_URL="mysql://USER:PASSWORD@localhost:3306/DATABASE_NAME?schema=public"
     ```
   - Replace `USER`, `PASSWORD`, and `DATABASE_NAME` with your MySQL credentials

3. **Set up your database**:
   - Create the database in MySQL:
     ```sql
     CREATE DATABASE DATABASE_NAME;
     ```
   - Or use your preferred MySQL client to create the database

## Step 4: Define Your Data Model

1. **Edit the Prisma schema** (`prisma/schema.prisma`):

   // Example schema - modify as needed
   generator client {
     provider = "prisma-client-js"
   }

   datasource db {
     provider = "mysql"
     url      = env("DATABASE_URL")
   }

   model User {
     id        Int      @id @default(autoincrement())
     email     String   @unique
     name      String?
     password  String
     posts     Post[]
     createdAt DateTime @default(now())
     updatedAt DateTime @updatedAt
   }

   model Post {
     id        Int      @id @default(autoincrement())
     title     String
     content   String?
     published Boolean  @default(false)
     author    User     @relation(fields: [authorId], references: [id])
     authorId  Int
     createdAt DateTime @default(now())
     updatedAt DateTime @updatedAt
   }
   ```

## Step 5: Create Database Tables

1. **Run the migration**:
   ```bash
   npx prisma migrate dev --name init
   ```

2. **Generate Prisma Client**:
   ```bash
   npx prisma generate
   ```

## Step 6: Set Up Your Project Structure

Create the following directory structure (as shown in the previous syntax example):
- `src/` directory with subdirectories for controllers, routes, etc.
- Configuration files

## Step 7: Seed Your Database (Optional)

1. **Create a seed file** (`prisma/seed.ts`):
   ```typescript
   import { PrismaClient } from '@prisma/client';
   import bcrypt from 'bcryptjs';

   const prisma = new PrismaClient();

   async function main() {
     // Your seed data here
   }

   main()
     .catch(e => {
       console.error(e);
       process.exit(1);
     })
     .finally(async () => {
       await prisma.$disconnect();
     });
   ```

2. **Run the seed script**:
   ```bash
   npx ts-node prisma/seed.ts
   ```

## Step 8: Configure Your Environment

1. **Create a `.env` file** in your project root:
   ```env
   DATABASE_URL="mysql://user:password@localhost:3306/db_name?schema=public"
   PORT=3000
   JWT_SECRET=your_jwt_secret_key
   ```

2. **Add `.env` to your `.gitignore`** to keep secrets secure

## Step 9: Add Scripts to package.json

Update your `package.json` with these useful scripts:
```json
{
  "scripts": {
    "dev": "ts-node-dev src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:studio": "prisma studio",
    "prisma:seed": "ts-node prisma/seed.ts",
    "lint": "eslint . --ext .ts",
    "format": "prettier --write src/**/*.ts"
  }
}
```

## Step 10: Start Your Application

1. **Run in development mode**:
   ```bash
   npm run dev
   ```

2. **Or build and run in production**:
   ```bash
   npm run build
   npm start
   ```

## Additional Configuration Options

### For Production:

1. **Install PM2 for process management**:
   ```bash
   npm install -g pm2
   ```

2. **Start your app with PM2**:
   ```bash
   pm2 start dist/server.js --name "my-prisma-app"
   ```

### For Database Management:

1. **Use Prisma Studio to view/edit your data**:
   ```bash
   npx prisma studio
   ```

2. **Access MySQL directly**:
   ```bash
   mysql -u USER -p
   ```

## Troubleshooting

1. **Database connection issues**:
   - Verify your MySQL server is running
   - Check your credentials in `.env`
   - Ensure the database exists

2. **Prisma migration problems**:
   - Try resetting your database:
     ```bash
     npx prisma migrate reset
     ```

3. **TypeScript errors**:
   - Make sure all `@types/` packages are installed
   - Check your `tsconfig.json` settings

This installation guide provides a complete setup for a Node.js application with Prisma ORM and MySQL. You can now begin developing your application using the structure and syntax examples provided earlier.
