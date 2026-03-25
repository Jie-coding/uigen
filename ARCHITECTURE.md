# Project Architecture Diagram

```mermaid
graph TD
    A[User] --> B[Browser]
    B --> C[Next.js App Router]

    subgraph Frontend
        C --> D[Home Page]
        C --> E[Project Page]
        E --> F[Chat Interface]
        E --> G[Code Editor]
        E --> H[Live Preview]
        F --> I[Message Input]
        F --> J[Message List]
        G --> K[File Tree]
        G --> L[Monaco Editor]
        H --> M[Preview Frame]
    end

    subgraph Backend
        C --> N[API Routes]
        N --> O[Chat API Route]
        O --> P[Language Model]
        O --> Q[Virtual File System]
        C --> R[Server Actions]
        R --> S[Create Project]
        R --> T[Get Projects]
        R --> U[Auth Actions]
        U --> V[Sign Up]
        U --> W[Sign In]
        R --> X[Prisma Client]
        X --> Y[SQLite Database]
    end

    subgraph AI Integration
        P --> Z[Anthropic API]
        P --> AA[Mock Language Model]
    end

    subgraph File System
        Q --> AB[File Operations]
        Q --> AC[File Management Tools]
        Q --> AD[String Replacement Tools]
    end

    subgraph Preview System
        M --> AE[JSX Transformer]
        AE --> AF[Babel]
        AE --> AG[Import Map Creation]
        M --> AH[HTML Preview Generation]
    end

    subgraph Authentication
        U --> AI[JWT Tokens]
        U --> AJ[bcrypt]
        U --> AK[HTTP-only Cookies]
    end

    %% Styling
    classDef frontend fill:#f0f8ff,stroke:#2383e2,stroke-width:2px;
    classDef backend fill:#e6f7ff,stroke:#135299,stroke-width:2px;
    classDef ai fill:#fff0f5,stroke:#d2691e,stroke-width:2px;
    classDef fs fill:#f5f5dc,stroke:#8b4513,stroke-width:2px;
    classDef preview fill:#fff0ff,stroke:#9932cc,stroke-width:2px;
    classDef auth fill:#f0fff0,stroke:#228b22,stroke-width:2px;

    class C,D,E,F,G,H,I,J,K,L,M frontend;
    class N,O,R,S,T,U,V,W,X,Y backend;
    class P,Z,AA ai;
    class Q,AB,AC,AD fs;
    class M,AE,AF,AG,AH preview;
    class U,AI,AJ,AK auth;
```

## Architecture Overview

### Frontend
- **Home Page**: Landing page with sign-in/sign-up options
- **Project Page**: Main interface for project development
- **Chat Interface**: AI chat interaction with message history
- **Code Editor**: Monaco Editor for file management and editing
- **Live Preview**: Iframe showing rendered React components

### Backend
- **API Routes**: Next.js API endpoints (chat endpoint with Vercel AI SDK)
- **Server Actions**: Direct database operations from React components
- **Prisma Client**: Auto-generated database query builder
- **SQLite Database**: Local database for user and project data

### AI Integration
- **Language Model**: Either Anthropic API (with API key) or mock model (for demo)
- **Mock Language Model**: Generates static components (Counter, Form, Card) without AI

### File System
- **Virtual File System**: In-memory file management for project files
- **File Management Tools**: Rename, delete operations for virtual files
- **String Replacement Tools**: Search and replace operations in files

### Preview System
- **JSX Transformer**: Babel-based transformation of JSX/TypeScript files
- **Import Map Creation**: Handles module resolution for preview
- **HTML Preview Generation**: Creates iframe content with CSS and scripts

### Authentication
- **JWT Tokens**: Session management using JSON Web Tokens
- **bcrypt**: Password hashing for secure storage
- **HTTP-only Cookies**: Secure session cookies (http-only, secure in production)

## Data Flow

1. User interacts with chat interface
2. Message sent to chat API endpoint
3. AI model generates response with tool calls
4. Tools modify virtual file system
5. File system changes are reflected in code editor
6. Preview system transforms and renders components
7. Changes are saved to database (if user is authenticated)