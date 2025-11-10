# Context7 MCP Server Guide for Claude Code

**Complete guide to using Context7 MCP server for up-to-date documentation and code examples in Claude Code (2025)**

---

## Table of Contents

1. [What is Context7?](#what-is-context7)
2. [Why Use Context7?](#why-use-context7)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [How to Use Context7](#how-to-use-context7)
6. [MCP Tools Reference](#mcp-tools-reference)
7. [Practical Examples](#practical-examples)
8. [Best Practices](#best-practices)
9. [Troubleshooting](#troubleshooting)
10. [Advanced Usage](#advanced-usage)

---

## What is Context7?

**Context7** is an MCP (Model Context Protocol) server developed by Upstash that provides **real-time, version-specific documentation and code examples** for AI code editors like Claude Code.

### Key Features

- ✅ **Up-to-date Documentation**: Fetches current docs from official sources
- ✅ **Version-Specific**: Get documentation for exact library versions
- ✅ **Zero Hallucination**: Eliminates outdated or fabricated API information
- ✅ **Official Sources**: Pulls from authoritative documentation repositories
- ✅ **Universal Compatibility**: Works with Claude Code, Cursor, VS Code, and more
- ✅ **Open Source**: Free to use with optional API key for higher limits

### How Context7 Works

```
┌─────────────┐         ┌─────────────┐         ┌──────────────────┐
│ Claude Code │ ←─MCP──→ │  Context7   │ ←────→ │ Official Library │
│             │         │  MCP Server │         │  Documentation   │
└─────────────┘         └─────────────┘         └──────────────────┘
```

When you reference a library in your prompt with Context7, it:
1. Resolves the library name to a Context7-compatible ID
2. Fetches the latest documentation from official sources
3. Injects relevant code examples and API specs into the context
4. Ensures Claude has accurate, current information

---

## Why Use Context7?

### Problems Context7 Solves

❌ **Without Context7**:
- Claude may reference outdated API methods
- Hallucinated function signatures and parameters
- Generic examples not specific to your version
- Breaking changes from documentation drift

✅ **With Context7**:
- Always current documentation from official sources
- Accurate API signatures and method names
- Version-specific code examples
- Confidence in generated code accuracy

### Supported Libraries

Context7 supports **thousands of libraries** across multiple ecosystems:

**Frontend Frameworks**:
- React, Next.js, Vue, Angular, Svelte, Solid.js
- Tailwind CSS, shadcn/ui, Radix UI

**Backend Frameworks**:
- FastAPI, Django, Flask, Express.js, Nest.js, Hono
- Spring Boot, Laravel, Rails

**Databases & ORMs**:
- Prisma, Drizzle, TypeORM, SQLAlchemy, Mongoose

**Cloud & Infrastructure**:
- AWS SDK, Google Cloud, Azure, Vercel, Cloudflare Workers

**And many more...**

---

## Installation

### Method 1: Claude Code CLI (Recommended)

```bash
# Install Context7 MCP server
claude mcp add context7 -- npx -y @upstash/context7-mcp

# Install with API key (optional, for higher rate limits)
claude mcp add context7 -- npx -y @upstash/context7-mcp --api-key YOUR_API_KEY
```

### Method 2: HTTP Transport (Alternative)

```bash
# Install via HTTP transport with API key
claude mcp add --transport http context7 https://mcp.context7.com/mcp \
  --header "CONTEXT7_API_KEY: YOUR_API_KEY"
```

### Method 3: Smithery CLI (Universal Installer)

```bash
# Install using Smithery
npx -y @smithery/cli@latest install @upstash/context7-mcp \
  --client claude \
  --key YOUR_SMITHERY_KEY
```

### Verify Installation

```bash
# List all MCP servers
claude mcp list

# Check Context7 server details
claude mcp get context7
```

You should see `context7` in your configured MCP servers list.

---

## Configuration

### Basic Configuration

Context7 works immediately after installation with default settings:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

### Configuration with API Key

For higher rate limits and private repository access:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp", "--api-key", "${CONTEXT7_API_KEY}"]
    }
  }
}
```

### Environment Variables

Create `.env` file:

```bash
# Context7 API Key (optional)
CONTEXT7_API_KEY=your_api_key_here
```

**Get your API key**: Visit [context7.com/dashboard](https://context7.com/dashboard)

### Project-Specific Configuration

Create `.mcp.json` in your project root:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "env": {
        "CONTEXT7_API_KEY": "${CONTEXT7_API_KEY}"
      }
    }
  }
}
```

Add to `.gitignore`:
```
.env
.mcp.json.local
```

---

## How to Use Context7

### Implicit Usage (Automatic)

Claude Code automatically uses Context7 when you reference libraries in your prompts:

```
You: "Create a Next.js 15 app with server components and streaming"
Claude: [Automatically uses Context7 to fetch Next.js 15 documentation]
```

### Explicit Usage (Recommended)

Add `use context7` to your prompts for guaranteed documentation lookup:

```
You: "Build a FastAPI CRUD API with authentication. use context7"
Claude: [Explicitly fetches FastAPI documentation via Context7]
```

### Version-Specific Requests

Request documentation for specific versions:

```
You: "Show me React 19 Server Components examples. use context7"
Claude: [Fetches React 19-specific documentation]
```

### Topic-Focused Queries

Focus on specific aspects of a library:

```
You: "Explain Next.js parallel routes and intercepting routes. use context7"
Claude: [Fetches focused documentation on Next.js routing]
```

---

## MCP Tools Reference

Context7 exposes two MCP tools that Claude Code can invoke:

### 1. `resolve-library-id`

**Purpose**: Converts a library name to a Context7-compatible library ID

**When to use**:
- MUST be called before `get-library-docs`
- UNLESS user provides explicit ID like `/org/project` or `/org/project/version`

**Parameters**:
- `libraryName` (required): Library name to resolve (e.g., "react", "next.js", "fastapi")

**Returns**:
- List of matching libraries with Context7-compatible IDs
- Trust scores and documentation coverage metrics

**Example**:
```
Input: libraryName="next.js"
Output: [
  {
    "id": "/vercel/next.js",
    "name": "Next.js",
    "trustScore": 10,
    "codeSnippets": 1247
  }
]
```

### 2. `get-library-docs`

**Purpose**: Fetches up-to-date documentation for a library

**Parameters**:
- `context7CompatibleLibraryID` (required): Library ID from `resolve-library-id`
- `topic` (optional): Focus area (e.g., "hooks", "routing", "authentication")
- `tokens` (optional): Maximum documentation tokens (default: 5000, range: 1000-50000)

**Returns**:
- Formatted documentation with code examples
- API specifications and method signatures
- Version-specific guidance

**Example**:
```
Input:
  context7CompatibleLibraryID="/vercel/next.js"
  topic="server components"
  tokens=5000

Output: [Comprehensive Next.js Server Components documentation]
```

### Tool Workflow

```
1. User mentions library in prompt
2. Claude calls resolve-library-id("library-name")
3. Context7 returns library ID and metadata
4. Claude calls get-library-docs("/org/project", "topic")
5. Context7 returns relevant documentation
6. Claude uses documentation to generate accurate code
```

---

## Practical Examples

### Example 1: React 19 Server Components

**Prompt**:
```
Create a React 19 Server Component that fetches user data from an API. use context7
```

**What happens**:
1. Context7 resolves "react" → `/facebook/react/v19`
2. Fetches React 19 Server Component documentation
3. Claude generates code with correct async/await patterns
4. Uses current React 19 API (not older versions)

**Generated Code**:
```tsx
// app/users/page.tsx
export default async function UsersPage() {
  const response = await fetch('https://api.example.com/users', {
    next: { revalidate: 3600 }
  });
  const users = await response.json();

  return (
    <div>
      {users.map((user) => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  );
}
```

### Example 2: FastAPI with Authentication

**Prompt**:
```
Build a FastAPI CRUD API with JWT authentication and dependency injection. use context7
```

**What happens**:
1. Context7 fetches latest FastAPI documentation
2. Retrieves current JWT and dependency injection patterns
3. Claude generates production-ready code with correct imports

**Generated Code**:
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import JWTError, jwt
from passlib.context import CryptContext
from datetime import datetime, timedelta

app = FastAPI()
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

SECRET_KEY = "your-secret-key"
ALGORITHM = "HS256"

def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=30)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

async def get_current_user(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise HTTPException(status_code=401, detail="Invalid token")
        return username
    except JWTError:
        raise HTTPException(status_code=401, detail="Invalid token")

@app.post("/token")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    # Authentication logic here
    access_token = create_access_token(data={"sub": form_data.username})
    return {"access_token": access_token, "token_type": "bearer"}

@app.get("/users/me")
async def read_users_me(current_user: str = Depends(get_current_user)):
    return {"username": current_user}
```

### Example 3: Prisma with Next.js

**Prompt**:
```
Set up Prisma with Next.js 15 App Router and create a user model. use context7
```

**What happens**:
1. Context7 fetches Prisma and Next.js 15 documentation
2. Provides current integration patterns
3. Claude generates compatible configuration and code

**Generated Files**:

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client'

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined
}

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({
    log: ['query'],
  })

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma
```

### Example 4: Tailwind CSS with shadcn/ui

**Prompt**:
```
Create a login form using Tailwind CSS and shadcn/ui components. use context7
```

**What happens**:
1. Context7 fetches shadcn/ui component documentation
2. Retrieves Tailwind CSS utility classes
3. Claude generates form with correct component imports

**Generated Code**:
```tsx
import { Button } from "@/components/ui/button"
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"

export function LoginForm() {
  return (
    <Card className="w-full max-w-sm">
      <CardHeader>
        <CardTitle className="text-2xl">Login</CardTitle>
        <CardDescription>
          Enter your email below to login to your account.
        </CardDescription>
      </CardHeader>
      <CardContent className="grid gap-4">
        <div className="grid gap-2">
          <Label htmlFor="email">Email</Label>
          <Input
            id="email"
            type="email"
            placeholder="m@example.com"
            required
          />
        </div>
        <div className="grid gap-2">
          <Label htmlFor="password">Password</Label>
          <Input id="password" type="password" required />
        </div>
        <Button type="submit" className="w-full">
          Sign in
        </Button>
      </CardContent>
    </Card>
  )
}
```

### Example 5: Drizzle ORM with TypeScript

**Prompt**:
```
Set up Drizzle ORM with PostgreSQL and create a schema for a blog. use context7
```

**What happens**:
1. Context7 fetches Drizzle ORM documentation
2. Provides current schema definition patterns
3. Claude generates type-safe schema and migrations

**Generated Code**:
```typescript
// db/schema.ts
import { pgTable, serial, text, timestamp, integer } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  name: text('name').notNull(),
  email: text('email').notNull().unique(),
  createdAt: timestamp('created_at').defaultNow(),
});

export const posts = pgTable('posts', {
  id: serial('id').primaryKey(),
  title: text('title').notNull(),
  content: text('content').notNull(),
  authorId: integer('author_id')
    .notNull()
    .references(() => users.id),
  published: timestamp('published'),
  createdAt: timestamp('created_at').defaultNow(),
});
```

---

## Best Practices

### 1. Always Use `use context7` for Critical Code

**DO**:
```
Create a production-ready authentication system with NextAuth.js. use context7
```

**DON'T**:
```
Create an authentication system (without context7)
```

**Why**: Explicit invocation ensures Claude fetches current documentation, preventing deprecated API usage.

### 2. Specify Library Versions When Important

**DO**:
```
Implement React 19 use() hook for data fetching. use context7
```

**DON'T**:
```
Show me React data fetching
```

**Why**: Version-specific requests ensure you get the latest features and patterns.

### 3. Focus Queries on Specific Topics

**DO**:
```
Explain Next.js 15 parallel routes and intercepting routes. use context7
```

**DON'T**:
```
Explain everything about Next.js routing
```

**Why**: Focused queries retrieve more relevant documentation within token limits.

### 4. Combine with Other MCP Servers

**Example Workflow**:
```
1. Use Context7 for accurate documentation
2. Use Sequential Thinking for complex implementation planning
3. Use Playwright for testing the generated code
```

### 5. Validate Generated Code

**Always**:
- Review generated imports and dependencies
- Check for correct version compatibility
- Verify against official documentation
- Test thoroughly before production deployment

### 6. Use Project-Scoped Configuration

**DO**: Create `.mcp.json` in each project with relevant libraries
**DON'T**: Rely solely on global configuration

**Why**: Project-specific configuration ensures consistent documentation access for team members.

### 7. Monitor Token Usage

**Best Practice**: Adjust `tokens` parameter based on query complexity

```
Simple query:   tokens=2000
Moderate query: tokens=5000 (default)
Complex query:  tokens=10000
Deep dive:      tokens=20000+
```

---

## Troubleshooting

### Issue 1: Context7 Server Not Found

**Error**: `MCP server 'context7' not found`

**Solution**:
```bash
# Check installed servers
claude mcp list

# Reinstall Context7
claude mcp remove context7
claude mcp add context7 -- npx -y @upstash/context7-mcp

# Verify installation
claude mcp get context7
```

### Issue 2: Authentication Failed

**Error**: `Context7 authentication error` or `Rate limit exceeded`

**Solution**:
```bash
# Add API key
claude mcp add context7 -- npx -y @upstash/context7-mcp \
  --api-key YOUR_API_KEY

# Or set environment variable
export CONTEXT7_API_KEY=your_api_key_here
```

**Get API Key**: [context7.com/dashboard](https://context7.com/dashboard)

### Issue 3: Library Not Found

**Error**: `Library 'xyz' not found in Context7`

**Solution**:
```
1. Check library name spelling
2. Try alternative names (e.g., "nextjs" vs "next.js")
3. Use official package name from npm/PyPI
4. Check if library is supported: search at context7.com
```

### Issue 4: Outdated Documentation Returned

**Error**: Context7 returns old documentation

**Solution**:
```
1. Specify version explicitly: "React 19" not just "React"
2. Clear Claude Code cache: restart Claude Code
3. Verify library version: include version in prompt
4. Check Context7 status: context7.com/status
```

### Issue 5: High Token Usage

**Error**: `Context7 output exceeded token limit`

**Solution**:
```
1. Use focused queries with specific topics
2. Adjust tokens parameter: reduce from default 5000
3. Break complex queries into smaller parts
4. Use --uc flag for compressed output
```

### Issue 6: Slow Response Times

**Symptoms**: Context7 takes longer than expected

**Solution**:
```
1. Check internet connection
2. Reduce tokens parameter for faster retrieval
3. Use cached results when possible
4. Consider upgrading to API key for priority access
```

### Issue 7: NPX Installation Errors

**Error**: `npx: failed to install @upstash/context7-mcp`

**Solution**:
```bash
# Install globally first
npm install -g @upstash/context7-mcp

# Then add to Claude Code
claude mcp add context7 npx -- @upstash/context7-mcp

# Or use local installation
cd ~/.claude/mcp-servers
npm install @upstash/context7-mcp
```

---

## Advanced Usage

### 1. Multi-Library Workflows

Fetch documentation for multiple libraries in one session:

```
Create a full-stack app with:
- Next.js 15 for frontend
- Prisma for database
- NextAuth.js for authentication
- Tailwind CSS for styling

use context7 for all libraries
```

Context7 will sequentially fetch documentation for each library.

### 2. Custom Library IDs

Use explicit library IDs when you know them:

```python
# Using explicit library ID
context7CompatibleLibraryID="/vercel/next.js/v15.0.0"
```

**Format**: `/org/project` or `/org/project/version`

### 3. Token Optimization

Adjust token allocation based on query complexity:

```bash
# Configuration with custom token limits
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "env": {
        "CONTEXT7_MAX_TOKENS": "10000"
      }
    }
  }
}
```

### 4. Topic-Focused Retrieval

Focus documentation on specific topics for better relevance:

**Examples**:
- `topic="hooks"` - React hooks documentation
- `topic="routing"` - Framework routing documentation
- `topic="authentication"` - Auth-specific patterns
- `topic="deployment"` - Deployment guides
- `topic="performance"` - Performance optimization

### 5. Trust Score Filtering

When `resolve-library-id` returns multiple matches:

```
1. Prioritize libraries with trustScore ≥ 8
2. Check codeSnippets count (higher = better)
3. Verify organization/author
4. Prefer official repositories
```

### 6. Integration with Claude Code Workflows

**Full Development Workflow**:
```
/load - Load project context
use context7 - Fetch library documentation
/implement - Implement feature with accurate APIs
/test - Generate tests with correct assertions
/build - Verify build with proper dependencies
/git - Commit with detailed change description
```

### 7. Private Repository Access

With API key, access private organization documentation:

```bash
# Configure with API key for private repos
claude mcp add context7 -- npx -y @upstash/context7-mcp \
  --api-key YOUR_ENTERPRISE_API_KEY
```

**Use Case**: Internal company libraries, proprietary frameworks

### 8. Caching Strategies

Context7 automatically caches documentation for session reuse:

```
Session 1: Fetch React documentation (full retrieval)
Session 2: Use cached React docs (instant access)
Session 3: Updated docs auto-refresh after TTL
```

### 9. Offline Fallback

Configure graceful degradation when Context7 unavailable:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "fallback": "cache",
      "timeout": 10000
    }
  }
}
```

### 10. Monitoring Usage

Track Context7 usage and performance:

```bash
# View Context7 metrics
/mcp

# Check API usage (with API key)
Visit: context7.com/dashboard

# Monitor token consumption
Check Claude Code metrics panel
```

---

## Integration with SuperClaude Framework

If you're using the SuperClaude framework (from your `.claude/` configuration):

### Auto-Activation

Context7 automatically activates with:
- `--c7` or `--context7` flags
- External library imports detected
- Framework-specific questions
- Scribe persona active (documentation mode)

### Command Integration

Context7 optimizes these commands:
- `/build` - Framework-specific build patterns
- `/implement` - Accurate API usage
- `/analyze` - Library best practices
- `/improve` - Current optimization patterns
- `/document` - Official documentation references
- `/explain` - Authoritative explanations

### Persona Synergy

**Frontend Persona + Context7**:
```
Component generation with accurate shadcn/ui, Radix UI patterns
```

**Backend Persona + Context7**:
```
API development with correct FastAPI, Express.js, Hono APIs
```

**Scribe Persona + Context7**:
```
Documentation generation with official style guides
```

---

## Context7 vs. Other Documentation Tools

| Feature | Context7 | WebSearch | WebFetch | Memory |
|---------|----------|-----------|----------|--------|
| **Real-time docs** | ✅ Yes | ✅ Yes | ✅ Yes | ❌ No |
| **Version-specific** | ✅ Yes | ⚠️ Maybe | ⚠️ Maybe | ❌ No |
| **Official sources** | ✅ Always | ⚠️ Mixed | ⚠️ Mixed | N/A |
| **Code examples** | ✅ Yes | ⚠️ Variable | ⚠️ Variable | ✅ Stored |
| **Structured format** | ✅ Yes | ❌ No | ❌ No | ⚠️ Custom |
| **Offline cache** | ✅ Yes | ❌ No | ❌ No | ✅ Yes |
| **Token efficiency** | ✅ High | ⚠️ Medium | ⚠️ Medium | ✅ High |
| **API rate limits** | ⚠️ With key | ✅ Generous | ✅ Generous | ✅ None |

**Recommendation**: Use Context7 as primary documentation source, WebSearch/WebFetch for supplementary research.

---

## Resources

### Official Links

- **Context7 Website**: [context7.com](https://context7.com)
- **GitHub Repository**: [github.com/upstash/context7](https://github.com/upstash/context7)
- **API Dashboard**: [context7.com/dashboard](https://context7.com/dashboard)
- **NPM Package**: [@upstash/context7-mcp](https://www.npmjs.com/package/@upstash/context7-mcp)

### Documentation

- **Upstash Blog**: [upstash.com/blog/context7-mcp](https://upstash.com/blog/context7-mcp)
- **Installation Guide**: [apidog.com/blog/context7-mcp-server](https://apidog.com/blog/context7-mcp-server)
- **MCP Spec**: [modelcontextprotocol.io](https://modelcontextprotocol.io)

### Community Resources

- **MCP Servers Directory**: [mcpservers.org](https://mcpservers.org)
- **Smithery**: [smithery.ai/server/@upstash/context7-mcp](https://smithery.ai/server/@upstash/context7-mcp)
- **Glama**: [glama.ai/mcp/servers/@upstash/context7-mcp](https://glama.ai/mcp/servers/@upstash/context7-mcp)

### Getting Help

- **GitHub Issues**: [github.com/upstash/context7/issues](https://github.com/upstash/context7/issues)
- **Upstash Discord**: Community support channel
- **Stack Overflow**: Tag `context7` + `model-context-protocol`

---

## Quick Reference

### Installation Commands

```bash
# Basic installation
claude mcp add context7 -- npx -y @upstash/context7-mcp

# With API key
claude mcp add context7 -- npx -y @upstash/context7-mcp --api-key YOUR_KEY

# HTTP transport
claude mcp add --transport http context7 https://mcp.context7.com/mcp \
  --header "CONTEXT7_API_KEY: YOUR_KEY"

# Verify
claude mcp list
claude mcp get context7
```

### Usage Patterns

```bash
# Explicit invocation (recommended)
"Create X using Y. use context7"

# Implicit (automatic detection)
"Build a Next.js app with Prisma"

# Version-specific
"Implement React 19 Server Components. use context7"

# Topic-focused
"Explain Next.js routing. use context7"
```

### MCP Tools

```python
# 1. Resolve library name to ID
resolve-library-id(libraryName="react")
→ Returns: [{"id": "/facebook/react", "trustScore": 10, ...}]

# 2. Fetch documentation
get-library-docs(
  context7CompatibleLibraryID="/facebook/react",
  topic="hooks",
  tokens=5000
)
→ Returns: [Documentation content]
```

### Environment Variables

```bash
CONTEXT7_API_KEY        # API authentication key
CONTEXT7_MAX_TOKENS     # Maximum token output limit
```

### Configuration Example

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "env": {
        "CONTEXT7_API_KEY": "${CONTEXT7_API_KEY}"
      }
    }
  }
}
```

---

## Changelog

### 2025-11-10
- Initial comprehensive Context7 guide created
- Installation methods documented
- MCP tools reference added
- Practical examples with code samples
- Best practices and troubleshooting sections
- Integration with SuperClaude framework
- Advanced usage patterns documented

---

## Contributing

Found issues or have suggestions? Contribute:

1. Open issue with Context7 usage questions
2. Submit PR with additional examples
3. Share workflows and best practices

---

**Last Updated**: 2025-11-10
**Context7 Version**: Latest (via @upstash/context7-mcp)
**Maintainer**: Claude Code Community
**Source**: [github.com/upstash/context7](https://github.com/upstash/context7)
