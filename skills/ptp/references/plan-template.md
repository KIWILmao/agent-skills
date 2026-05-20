# Plan Template

---

# Plan [NN] — [Module Name]

**Status:** `pending` | `in-progress` | `blocked` | `done` 
**Depends On:** `[plan-01-auth, plan-02-users]` or `none` 
**Can Run In Parallel With:** `[plan-03-notifications]` or `none` 
**Parallel Safe:** `yes` | `no`

---

## Overview
[1–2 sentences. What does this plan build? What user-facing capability does it unlock?]

---

## Dependency Notes
[If this plan depends on others, explain WHY here. E.g.: "Requires plan-01-auth because all 
endpoints in this plan are protected routes that need the JWT middleware set up first."]

---

## Scope

### In Scope
- [Specific thing 1 this plan covers]
- [Specific thing 2]

### Out of Scope (handled in other plans)
- [Thing left to plan-XX]

---

## Phase 1 — DB Schema & Migrations
**Status:** `pending`

### Tables / Collections

```sql
-- Example for PostgreSQL
CREATE TABLE users (
 id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
 email TEXT UNIQUE NOT NULL,
 name TEXT,
 role TEXT DEFAULT 'user',
 created_at TIMESTAMPTZ DEFAULT NOW(),
 updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_users_email ON users(email);
```

### Schema Notes
- [Any important constraints, indexes, or relationships to call out]
- [Migration order if multiple tables with FKs]

---

## Phase 2 — Backend: API Routes
**Status:** `pending`

### Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/api/auth/register` | public | Create new user |
| POST | `/api/auth/login` | public | Authenticate user, return JWT |
| GET | `/api/auth/me` | authenticated | Get current user |
| POST | `/api/auth/logout` | authenticated | Invalidate session |

### Request / Response Shapes

```typescript
// POST /api/auth/register
Request: { name: string, email: string, password: string }
Response: { user: UserObject, token: string }

// POST /api/auth/login
Request: { email: string, password: string }
Response: { user: UserObject, token: string }
```

---

## Phase 3 — Backend: Service Logic
**Status:** `pending`

### Services to Implement

**AuthService**
- `register(name, email, password)` — hash password, create user, return JWT
- `login(email, password)` — verify credentials, return JWT
- `getMe(userId)` — fetch user by ID

### Business Rules
- Passwords must be hashed with bcrypt (rounds: 12)
- JWT expires in 7 days
- Email must be unique — return 409 if already registered
- On failed login after 5 attempts → lock account for 15 minutes

### Error Handling
| Scenario | HTTP Code | Message |
|----------|-----------|---------|
| Email already exists | 409 | "Email already in use" |
| Wrong credentials | 401 | "Invalid email or password" |
| Account locked | 423 | "Account temporarily locked" |

---

## Phase 4 — Frontend: Screens & Components
**Status:** `pending`

### Screens to Build
(Reference screens.md for full spec)

- `/login` — Login screen
- `/register` — Registration screen
- `/forgot-password` — Forgot password screen

### Components

**`<LoginForm />`**
- Fields: email, password
- Validation: required, email format, min 8 chars password
- On submit: call `POST /api/auth/login`
- On success: store token, redirect to `/dashboard`
- On error: show inline error message

**`<AuthLayout />`**
- Wrapper for all auth screens
- Centered card, logo at top, no sidebar

### State Management
- Store JWT in: [localStorage / httpOnly cookie / zustand / redux — based on tech stack]
- Auth state: `{ user: UserObject | null, isLoading: boolean, isAuthenticated: boolean }`

---

## Phase 5 — Integration
**Status:** `pending`

### Wiring Checklist
- [ ] Frontend calls correct API endpoints (no hardcoded URLs — use env vars)
- [ ] Auth token is attached to all protected requests via interceptor/middleware
- [ ] Token expiry handled — redirect to login on 401
- [ ] Environment variables set in `.env` and `.env.example` updated
- [ ] CORS configured to allow frontend origin

---

## Phase 6 — Testing & QA
**Status:** `pending`

### Unit Tests
- [ ] `AuthService.register()` — happy path, duplicate email
- [ ] `AuthService.login()` — correct creds, wrong creds, locked account
- [ ] Password hashing — verify bcrypt is applied

### Integration Tests
- [ ] `POST /api/auth/register` — 201 on success, 409 on duplicate
- [ ] `POST /api/auth/login` — 200 with token, 401 on bad creds
- [ ] `GET /api/auth/me` — 200 with user, 401 without token

### Frontend Tests
- [ ] Login form validation errors shown correctly
- [ ] Redirect after login goes to /dashboard
- [ ] Unauthenticated access to protected routes redirects to /login

### Edge Cases
- [ ] Register with existing email (case-insensitive)
- [ ] Login with unverified email (if email verification is in scope)
- [ ] JWT tampered with — should return 401
- [ ] Very long email/password inputs
- [ ] Concurrent login from multiple devices

---

## Completion Checklist
- [ ] Phase 1 done — DB schema migrated
- [ ] Phase 2 done — All API routes implemented and returning correct responses
- [ ] Phase 3 done — Service logic complete with error handling
- [ ] Phase 4 done — All screens built and connected to state
- [ ] Phase 5 done — End-to-end flow works in dev environment
- [ ] Phase 6 done — All tests passing, edge cases verified
- [ ] execute.md updated to mark this plan as `done`
