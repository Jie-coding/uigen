```
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.
```

## UIGen Project Overview

AI-powered React component generator with live preview, built with Next.js 15, React 19, TypeScript, Tailwind CSS v4, Prisma, and Anthropic Claude AI.

The application allows users to describe React components in natural language, and the AI generates the code with live preview. It supports both authenticated and anonymous usage.

## Development Commands

| Command               | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `npm run setup`       | Install dependencies, generate Prisma client, and run database migrations  |
| `npm run dev`         | Start development server with Turbopack (default port: 3000)               |
| `npm run build`       | Build for production                                                       |
| `npm run start`       | Start production server                                                    |
| `npm run lint`        | Run ESLint                                                                  |
| `npm run test`        | Run Vitest tests (use `npm run test -- --run` to run all tests)             |
| `npm run db:reset`    | Reset and reapply database migrations                                      |

## Project Structure

```
src/
├── app/                          # Next.js App Router pages
│   ├── [projectId]/page.tsx    # Project-specific page with editor/preview
│   ├── api/chat/route.ts       # Chat API endpoint (AI integration)
│   ├── main-content.tsx        # Main content wrapper
│   ├── page.tsx                # Home page
│   └── layout.tsx              # Root layout
├── components/
│   ├── ui/                     # ShadCN UI components (tabs, dialog, button, etc.)
│   ├── chat/                   # Chat interface components (with tests)
│   ├── auth/                   # Authentication components (sign-in, sign-up)
│   │   ├── AuthDialog.tsx     # Main auth dialog that switches between sign-in/sign-up
│   │   ├── SignInForm.tsx     # Sign in form using useAuth hook
│   │   └── SignUpForm.tsx     # Sign up form using useAuth hook
│   ├── preview/                # Preview frame for generated components
│   └── editor/                 # Code editor and file tree components
├── hooks/                      # Custom React hooks (use-auth.ts)
├── actions/                    # Server actions (create-project.ts, get-projects.ts, etc.)
└── lib/
    ├── prisma.ts              # Prisma client instance (singleton pattern)
    ├── file-system.ts         # Virtual file system implementation for in-memory file management
    ├── auth.ts                # JWT-based authentication utilities
    ├── provider.ts            # Language model provider (Anthropic API or mock)
    ├── prompts/               # AI prompt templates
    ├── tools/                 # File management and string replacement utilities
    ├── transform/             # JSX/TypeScript transformation utilities
    └── contexts/              # React Context for chat and file system state
```

## Key Technologies

- **Framework**: Next.js 15 with App Router
- **Language**: TypeScript
- **Styling**: Tailwind CSS v4 with ShadCN UI components
- **Database**: Prisma + SQLite
- **AI Integration**: Vercel AI SDK with Anthropic Claude
- **Code Editor**: Monaco Editor (@monaco-editor/react)
- **Testing**: Vitest + React Testing Library

## Environment Setup

- `.env` file supports `ANTHROPIC_API_KEY` (optional - static code returned if not provided)
- `JWT_SECRET` (optional - defaults to "development-secret-key" for local development)
- Node.js 18+ required
- SQLite database stored in `prisma/dev.db` (automatically created by setup)

## Architecture Overview

A high-level architecture diagram is available in [ARCHITECTURE.md](ARCHITECTURE.md)

## Prisma Client

The Prisma client is an auto-generated, type-safe database query builder that simplifies database interactions. It is configured in `src/lib/prisma.ts` using a singleton pattern to ensure only one instance is created per application lifecycle.

### Key Features
1. **Type-Safe Queries**: Generates TypeScript types based on the Prisma schema
2. **Singleton Pattern**: Ensures a single PrismaClient instance is reused across requests
3. **Auto-Generated**: Regenerated after each `prisma generate` command (run by `npm run setup`)
4. **Output Location**: Generated client is located in `src/generated/prisma`

### Example Usage
```typescript
import { prisma } from "@/lib/prisma";

// Get all projects for a user
const projects = await prisma.project.findMany({
  where: { userId: "user-id" },
  orderBy: { createdAt: "desc" },
});

// Create a new project
const project = await prisma.project.create({
  data: {
    name: "New Project",
    userId: "user-id",
    messages: JSON.stringify([]),
    data: JSON.stringify({}),
  },
});
```

## Database Schema

The Prisma schema defines two main models:

### User
- `id`: Unique identifier (cuid)
- `email`: Unique email address
- `password`: bcrypt-hashed password
- `createdAt`: Account creation timestamp
- `updatedAt`: Last update timestamp
- `projects`: List of user's projects (one-to-many relationship)

### Project
- `id`: Unique identifier (cuid)
- `name`: Project name
- `userId`: Optional user ID (null for anonymous projects)
- `messages`: JSON string of chat messages
- `data`: JSON string of virtual file system data
- `createdAt`: Project creation timestamp
- `updatedAt`: Last update timestamp
- `user`: User who owns the project (optional, nullable for anonymous projects)

## Important Files

- `src/app/api/chat/route.ts`: AI chat endpoint handling with Vercel AI SDK
- `src/lib/file-system.ts`: Virtual file system for managing generated code in memory
- `src/lib/auth.ts`: JWT-based authentication utilities (createSession, getSession, etc.)
- `src/lib/provider.ts`: Language model provider (Anthropic API or mock for development)
- `src/lib/transform/jsx-transformer.ts`: JSX/TypeScript transformation for preview
- `src/contexts/chat-context.tsx`: Chat state management (messages, input, status)
- `src/contexts/file-system-context.tsx`: File system state management
- `src/hooks/use-auth.ts`: Custom React hook that wraps auth actions and handles post-sign-in redirects
- `src/components/auth/`: Authentication UI components
- `prisma/schema.prisma`: Database schema definition (User, Project models)


