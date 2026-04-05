# Three-Layer Memory System

## Architecture

```
LAYER 1: Index (MEMORY.md)
├─ Always loaded in context
├─ Maximum 200 lines
├─ ~150 characters per line
└─ Points to detailed files

LAYER 2: Topic Files
├─ Loaded only when needed
├─ Organized by subject
├─ Full detailed information
└─ No size limit

LAYER 3: Archives
├─ Never fully loaded
├─ Searched when needed
├─ Old conversation history
└─ Background reference
```

---

## Explanation

Instead of loading all information every time, the system uses three layers:

- **Index (always loaded)** - small reference list
- **Details (loaded when needed)** - full information organized by topic  
- **Archives (searched when needed)** - old conversations

Think of it like a library: **card catalog → books → basement archives**

---

## How Each Layer Works

### Layer 1: Index (MEMORY.md)

**Purpose:** Quick reference to know what information exists

**What it contains:**
```
config.md - Server configuration and ports
api.md - API endpoints and authentication
database.md - Database schema and connections
testing.md - Test commands and procedures
bugs.md - Known bugs and solutions
```

**Format:**
- One line per topic
- Format: `filename - description (tags)`
- Under 150 characters per line
- Maximum 200 lines total

**Why always loaded:**
- Small size (typically 500-1000 tokens)
- AI can see all available topics instantly
- Knows where to find detailed information
- Quick to scan and search

**Example:**
```markdown
# AI Memory Index

config.md - Server settings, ports, and environment (config, setup)
api.md - REST API endpoints and auth methods (api, backend)
database.md - PostgreSQL schema and queries (database, sql)
testing.md - Jest test commands and coverage (testing, qa)
deployment.md - Docker setup and CI/CD pipeline (devops, deploy)
```

---

### Layer 2: Topic Files

**Purpose:** Detailed information organized by subject

**What it contains:** Complete information about specific topics

**Structure of a topic file:**

**config.md:**
```markdown
# Configuration

Last Updated: 2026-04-04
Last Verified: ✅ 2026-04-04

## Server Settings
Port: 3001
Host: localhost
Protocol: HTTP
Timeout: 30 seconds

## Database Connection
Type: PostgreSQL
Host: localhost
Port: 5432
Database Name: myapp_production
Username: admin

## Environment Variables
NODE_ENV=production
API_KEY=abc123xyz
JWT_SECRET=secret_key_here
DATABASE_URL=postgresql://localhost:5432/myapp

## Start Commands
# Development
npm run dev

# Production
npm run start

# Run migrations
npm run migrate

## Known Issues
- Port 5432 may conflict with local PostgreSQL
- If conflict, change to port 5433
- JWT_SECRET must be at least 32 characters
```

**api.md:**
```markdown
# API Endpoints

Last Updated: 2026-04-04

## Authentication

### POST /api/auth/login
Description: User login
Request Body:
{
  "email": "user@example.com",
  "password": "password123"
}

Response:
{
  "token": "jwt_token_here",
  "user": {
    "id": 1,
    "email": "user@example.com",
    "name": "John Doe"
  }
}

### POST /api/auth/refresh
Description: Refresh JWT token
Headers: Authorization: Bearer {token}
Response: New JWT token

## Users

### GET /api/users
Description: List all users
Auth: Required
Response: Array of user objects

### GET /api/users/:id
Description: Get single user
Auth: Required
Response: User object

### POST /api/users
Description: Create new user
Auth: Admin only
Request Body:
{
  "email": "new@example.com",
  "password": "password",
  "name": "Jane Doe"
}

### PUT /api/users/:id
Description: Update user
Auth: Required (own profile or admin)

### DELETE /api/users/:id
Description: Delete user
Auth: Admin only

## Rate Limits
- 100 requests per hour per IP
- 1000 requests per day per user
```

**When loaded:**
- Only when AI needs information about that topic
- Example: Question about API → Load api.md
- Example: Question about database → Load database.md

**Why not always loaded:**
- Too large to keep in context all the time
- Only load what's relevant to current question
- Saves tokens and cost

---

### Layer 3: Archives

**Purpose:** Historical reference and conversation logs

**What it contains:** Full conversation history from past sessions

**Structure:**
```
.ai_memory/sessions/
├─ 2026-04-01-morning.md
├─ 2026-04-01-afternoon.md
├─ 2026-04-02-debugging.md
├─ 2026-04-03-feature-work.md
└─ 2026-04-04-testing.md
```

**Example session file (2026-04-03-debugging.md):**
```markdown
# Session: 2026-04-03 Debugging

## Problem Encountered
Login endpoint returning 500 error
Error message: "jwt must be provided"

## Investigation Steps
1. Checked database connection ✓
2. Reviewed authentication middleware ✓
3. Examined environment variables
4. Found: JWT_SECRET missing from .env

## Solution Applied
Added JWT_SECRET to .env file
Value: generated 32-character random string

## Code Changes
File: .env
Added: JWT_SECRET=a8f5c2d9e1b7f3a6c4d8e2b9f5a1c7d3

## Testing
Tested login endpoint
Result: Success ✓
Token generated correctly

## Lessons Learned
- Always verify environment variables
- JWT_SECRET is required for authentication
- Check .env.example matches .env
```

**When accessed:**
- Never fully loaded into context
- Searched only when specific information is needed
- Example: "How did we fix the login bug last week?"
- Example: "What did we decide about database migrations?"

**How searched:**
- Grep/search for keywords
- Find relevant session
- Extract only the needed information
- Return small relevant excerpt

**Why not loaded:**
- Extremely large (thousands of tokens per session)
- Most information not relevant to current question
- Would waste tokens loading everything
- Search is more efficient

---

## How the Layers Work Together

### Example 1: Simple Question

**Question:** "What port does the API run on?"

**Process:**
```
1. AI reads Layer 1 (MEMORY.md)
   Sees: "config.md - Server configuration and ports"

2. AI loads Layer 2 (config.md)
   Finds: "Port: 3001"

3. AI answers: "The API runs on port 3001"

Layer 3 not needed
```

**Tokens used:**
- Layer 1: 500 tokens (always loaded)
- Layer 2: 2,000 tokens (config.md)
- Total: 2,500 tokens

---

### Example 2: Historical Question

**Question:** "How did we fix the login bug last week?"

**Process:**
```
1. AI reads Layer 1 (MEMORY.md)
   Sees: "bugs.md - Known bugs and solutions"

2. AI loads Layer 2 (bugs.md)
   Checks current bugs - not found

3. AI searches Layer 3 (archives)
   Searches sessions/ for "login bug"
   Finds: 2026-04-03-debugging.md
   Extracts: "JWT_SECRET was missing from .env"

4. AI answers: "The login bug was fixed by adding 
   JWT_SECRET to the .env file"
```

**Tokens used:**
- Layer 1: 500 tokens
- Layer 2: 1,500 tokens (bugs.md)
- Layer 3: 1,000 tokens (relevant excerpt)
- Total: 3,000 tokens

---

### Example 3: Complex Question

**Question:** "Show me all API endpoints that require authentication"

**Process:**
```
1. AI reads Layer 1 (MEMORY.md)
   Sees: "api.md - API endpoints and authentication"

2. AI loads Layer 2 (api.md)
   Finds all endpoints
   Identifies which require auth

3. AI lists authenticated endpoints:
   - POST /api/auth/refresh
   - GET /api/users
   - GET /api/users/:id
   - PUT /api/users/:id
   - DELETE /api/users/:id

Layer 3 not needed
```

**Tokens used:**
- Layer 1: 500 tokens
- Layer 2: 3,500 tokens (api.md is larger)
- Total: 4,000 tokens

---

## Why This Matters

### Without 3-Layer System (Traditional Approach)

**Every question loads everything:**
```
config.md: 5,000 tokens
api.md: 8,000 tokens
database.md: 6,000 tokens
testing.md: 3,000 tokens
bugs.md: 4,000 tokens
Session 1: 5,000 tokens
Session 2: 5,000 tokens
Session 3: 5,000 tokens
Session 4: 5,000 tokens
Session 5: 5,000 tokens

Total: 51,000 tokens EVERY TIME
Cost: $5.10 per question
```

### With 3-Layer System (Claude Code Approach)

**Load only what's needed:**
```
Simple question:
Layer 1: 500 tokens
Layer 2: 2,000 tokens
Total: 2,500 tokens
Cost: $0.25

Complex question:
Layer 1: 500 tokens
Layer 2: 3,500 tokens
Layer 3: 1,000 tokens
Total: 5,000 tokens
Cost: $0.50

Average: $0.35 per question
```

**Savings:** 93% cost reduction

---


## Key Rules

1. **Index stays under 200 lines**
   - If it grows larger, split topics
   - Move details to topic files
   - Keep index clean

2. **One topic per file**
   - Don't mix unrelated information
   - Clear separation of concerns
   - Easy to find and update

3. **Topic files can be any size**
   - No limit on detail
   - Include everything relevant
   - Organize with sections

4. **Archives never edited**
   - Append only
   - Historical record
   - Search when needed

5. **Always verify**
   - Check if information is current
   - Update when things change
   - Mark last verified date

---

## Summary

The three-layer memory system is like a library:

**Layer 1 (Index)** = Card Catalog
- Small, always available
- Points to books
- Quick reference

**Layer 2 (Topics)** = Books on Shelves  
- Pulled when needed
- Full information
- Organized by subject

**Layer 3 (Archives)** = Basement Storage
- Rarely accessed
- Searched when needed
- Historical reference

**Result:** 95% cost reduction, fast access, perfect organization
