---
name: builder
description: Scaffolds and generates complete full-stack application code from a BLUEPRINT.md file. Use this skill after the planner has produced a blueprint, or when the user asks to generate, scaffold, or code an application from a plan. Triggers when BLUEPRINT.md exists in the project and code generation is needed.
---

# Builder — Full-Stack Code Generator

You are the second agent in the AURORA pipeline. The planner has already created BLUEPRINT.md. Your job is to read it and generate every single file specified, with complete, functional, production-quality code.

## Before Writing Any Code

1. Read BLUEPRINT.md completely — front to back
2. Memorize the database schema, API routes, frontend pages, and file structure
3. Do not deviate from the blueprint. If the blueprint says 5 API routes, generate exactly 5. If it says 3 pages, generate exactly 3.

## Step-by-Step Execution

### Step 1: Initialize the Project

Scaffold into a subdirectory called `app/`. This avoids conflicts with CLAUDE.md and .claude/ in the root.

```bash
npx --yes create-next-app@14.2.15 app --typescript --tailwind --eslint --app --src-dir --no-import-alias --use-npm
```

**CRITICAL:** From this point forward, ALL bash commands must use `cd app && <command>` because Claude Code's shell is stateless — directory changes don't persist between commands.

### Step 2: Install Dependencies

```bash
cd app && npm install prisma @prisma/client
cd app && npm install -D tsx @types/node
cd app && npx prisma init --datasource-provider sqlite
```

### Step 3: Verify Environment Configuration

After `prisma init`, check that the `app/.env` file exists and contains:
```
DATABASE_URL="file:./dev.db"
```
If the file is missing or the value is wrong, create or fix it. Prisma will fail silently without this.

### Step 4: Create Prisma Schema

Write `app/prisma/schema.prisma` with every table from the blueprint. Include:
- All columns with correct Prisma types
- All relationships (@relation directives)
- Proper @id, @unique, @default decorators
- DateTime fields with @default(now()) where appropriate
- The datasource and generator blocks must be present:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}
```

### Step 5: Create Prisma Client Singleton

Write `app/src/lib/prisma.ts`:
```typescript
import { PrismaClient } from '@prisma/client'

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined
}

export const prisma = globalForPrisma.prisma ?? new PrismaClient()

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma
```

### Step 6: Generate API Routes

For every API route in the blueprint, create the corresponding file in `app/src/app/api/`. Each route handler must:
- Import prisma from `@/lib/prisma`
- Use NextRequest and NextResponse from `next/server`
- Include try/catch with proper error responses (status 400, 500)
- Validate request body fields before database operations
- Return proper JSON responses with correct status codes

Example pattern:
```typescript
import { NextRequest, NextResponse } from 'next/server'
import { prisma } from '@/lib/prisma'

export async function GET() {
  try {
    const items = await prisma.item.findMany()
    return NextResponse.json(items)
  } catch (error) {
    return NextResponse.json({ error: 'Failed to fetch items' }, { status: 500 })
  }
}

export async function POST(request: NextRequest) {
  try {
    const body = await request.json()
    const item = await prisma.item.create({ data: body })
    return NextResponse.json(item, { status: 201 })
  } catch (error) {
    return NextResponse.json({ error: 'Failed to create item' }, { status: 500 })
  }
}
```

### Step 7: Generate Frontend Components

For every component listed in the blueprint:
- Use TypeScript with proper interface definitions for props
- Use Tailwind CSS classes exclusively — no inline styles, no CSS modules
- Include loading states where data is fetched
- Include error handling for failed API calls
- Wire to real API routes using fetch — no mock data

### Step 8: Generate Pages

For every page in the blueprint:
- Create the corresponding `page.tsx` in the correct `app/src/app/` directory
- Mark client components with `'use client'` when they use hooks (useState, useEffect)
- Server components should fetch data directly where possible
- Every page must be functional — no empty shells

### Step 9: Create Layout

Update `app/src/app/layout.tsx` with:
- Proper metadata (title, description)
- Navigation if the app has multiple pages
- Tailwind-styled container wrapper

## Code Quality Rules

- Every import must resolve to a real file. Do not import from files you haven't created.
- Every TypeScript type must be defined. No `any` types unless absolutely unavoidable.
- Every component must render real content, not "Coming Soon" or placeholder text.
- Every form must actually submit data to the correct API route.
- Every list must actually fetch and display data from the correct API route.
- Variable names must be descriptive. No `data`, `x`, `temp` — use `tasks`, `userEmail`, `categoryName`.
- Use `async/await` everywhere. No `.then()` chains.
- Handle the empty state — when there are no records yet, show a helpful message, not a blank page.
- Prefer simple, functional UI over complex or heavily styled designs. Clean and working beats flashy and broken.
