# MCP-Enhanced System Audit Prompt Suite

> **Surface critical bugs, security holes, architectural debt, and UX failures — before your users do.**

A battle-tested collection of structured prompts for conducting thorough codebase audits with AI assistants — now MCP-aware with automatic fallbacks. When MCP servers and plugins are installed (via the [MCP Setup Guide](MCP-SETUP.md)), these prompts automatically leverage them for deeper, tool-verified analysis. When they're not, every prompt falls back gracefully to built-in tools.

<p align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

</p>

<details>
<summary><strong>Table of Contents</strong></summary>

- [Why This Exists](#why-this-exists)
- [How to Use](#how-to-use)
- [The Prompts](#the-prompts)
  - [Prompt 1 — Features, Logic Gaps & Business Logic Security](#prompt-1--features-logic-gaps--business-logic-security)
  - [Prompt 2 — UX Clarity, Usability & Client-Side Security](#prompt-2--ux-clarity-usability--client-side-security)
  - [Prompt 3 — API & Data Integration Flaws & Endpoint Security](#prompt-3--api--data-integration-flaws--endpoint-security)
  - [Prompt 4 — Architecture, Sync Issues & System Security](#prompt-4--architecture-sync-issues--system-security)
  - [Prompt 5 — Missing Critical UX/UI Features & Interface Integrity](#prompt-5--missing-critical-uxui-features--interface-integrity)
  - [Prompt 6 — Testing Coverage, QA & Supply Chain Security](#prompt-6--testing-coverage-qa--supply-chain-security)
  - [Prompt 7 — Performance, Caching & Resource Optimization](#prompt-7--performance-caching--resource-optimization)
  - [Prompt 8 — Dead Code, Code Bloat & Maintainability](#prompt-8--dead-code-code-bloat--maintainability)
  - [Prompt 9 — Error Handling, Resilience & Failure Recovery](#prompt-9--error-handling-resilience--failure-recovery)
  - [Prompt 10 — Data Integrity, Migrations & Storage Security](#prompt-10--data-integrity-migrations--storage-security)
- [Output Format](#output-format)
- [Tips for Best Results](#tips-for-best-results)
- [Tool-Prompt Matrix](#tool-prompt-matrix)
- [Quick Reference: Which Prompt When?](#quick-reference-which-prompt-when)
- [Contributing](#contributing)
- [License](#license)

</details>

---

## Why This Exists

Code reviews catch syntax issues. These prompts catch **systemic failures** — the kind that emerge from the gap between what your frontend assumes, what your backend delivers, what your tests cover, and what an attacker can exploit.

| Principle | How It's Applied |
|-----------|-----------------|
| **Focused scope** | One concern domain per prompt prevents shallow, scattered analysis |
| **Signal over noise** | Only Critical and High-priority issues surface — no Medium/Low clutter |
| **Security by default** | Every prompt includes a dedicated security lens for its domain |
| **Actionable output** | Every finding maps to a remediation step, not just a complaint |
| **Tool-adaptive** | Prompts use MCP tools when available, fall back gracefully when not |

---

## How to Use

### Prerequisites

Each prompt is **standalone** — pick whichever ones match your current concern. You don't need to run them all or in order.

> [!TIP]
> For the **best possible audit**, run the [MCP Setup Prompt](MCP-SETUP.md) first. It installs MCP servers and plugins that give prompts deeper, tool-verified analysis (LSP tracing, database schema inspection, browser testing, etc.). Without it, every prompt still works — they just fall back to built-in tools like Grep, Glob, and Read.

The prompts work with **any AI assistant** — Claude Code, ChatGPT, Gemini, or anything that can read your codebase.

For best results, make sure the AI has access to these context types:

| Context Type | Examples |
|---|---|
| **Frontend** | Component tree, route definitions, state management files, API client/service layers, key UI components |
| **Backend** | Route/controller files, middleware stack, database models/schemas, service layers, auth logic |
| **Config** | `package.json`, `requirements.txt`, CI/CD configs, `.env.example`, Docker files |
| **Docs** | API specs (OpenAPI/Swagger), architecture diagrams, data flow documentation |

### Running the Audit

```
Step 1   Pick the prompt(s) that match your concern
         See "Which Prompt When?" below for guidance.
             │
Step 2   Paste the prompt into your AI assistant
         Each prompt is self-contained — no setup or ordering required.
             │
Step 3   Review findings and deduplicate
         If you ran multiple prompts, consolidate overlapping
         findings into a single remediation backlog.
             │
Step 4   Prioritize and execute
         Use severity ratings and remediation plans to drive
         sprint planning or hotfix cycles.
```

> [!TIP]
> **Shipping soon?** Focus on 1, 3, 6, 7. **Got a pen-test report?** Focus on 1, 3, 4, 6. **Performance issues?** Run 7 and 4.

---

## The Prompts

> [!IMPORTANT]
> **False positive elimination:** Each prompt includes a CONFIDENCE ROUTING section. Do NOT enter plan mode for this — it happens inline during investigation. The flow is:
>
> ```
> Found issue → Confident? ──YES──→ Report as [verified]
>    │    (can cite exact file, line,
>    │     and mechanism — no ambiguity)
>    │
>    NO (uncertain)
>    │
>    ▼
> Collect all uncertain issues, then
> dispatch each to its own subagent
> (via the Agent tool) IN PARALLEL.
> Each subagent independently:
>  - Uses Sequential Thinking to trace
>    the execution path, check guards,
>    model data flow, test reachability
>  - Corroborates with another tool if
>    available (LSP, DB MCP, Playwright…)
>  - Returns: Confirmed / Disproven /
>    Plausible-but-unconfirmable
>    │
>    ├── Confirmed ──→ Report as [verified]
>    │
>    ├── Disproven ──→ Discard (not in output)
>    │
>    └── Plausible but cannot confirm
>        (needs runtime/manual testing)
>            │
>            ▼
>        Add to "Investigated but Unverified"
>        appendix at end of report
> ```
>
> **Why subagents?** Verifying each uncertain issue requires deep reasoning (Sequential Thinking, LSP tracing, etc.). Running these sequentially in the main context wastes time and risks filling the context window. Dispatching each verification as a parallel subagent keeps the main context clean and verifies all uncertain issues simultaneously.
>
> Only `[verified]` findings go in the main report. Unverifiable-but-plausible findings go in the appendix so they stay visible without polluting the main findings.

---

### Prompt 1 — Features, Logic Gaps & Business Logic Security

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical and High-Priority Missing Features, Logic Gaps, and
Business Logic Security.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Search for state enums, route definitions, status
  transitions. Cross-reference frontend API calls against backend routes.
- Sequential Thinking: Model state machines and multi-step flows
  step-by-step to validate transition completeness and identify gaps.
- LSP/Serena: Trace call paths from frontend actions to backend handlers.
  Use find-all-references to verify both sides connect.
- GitHub MCP: Search issues/PRs for "race condition", "IDOR", "bypass",
  "privilege escalation" to cross-reference known issues with findings.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: traces the
  execution path, checks if guards exist elsewhere, confirms the data
  flow allows the vulnerability. Uses another tool (LSP, GitHub MCP)
  to corroborate if available. Each subagent returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. FEATURE PARITY GAPS
   - Core features the frontend expects but the backend does not support
     (or vice versa).
   - Incomplete implementations where the feature exists in one layer but
     is stubbed, hard-coded, or non-functional in the other.

2. BUSINESS LOGIC GAPS
   - Missing or broken state machines (e.g., an order can skip from
     "pending" to "shipped" without "confirmed").
   - Missing validation rules that allow invalid domain states.
   - Race conditions in business-critical flows (e.g., double-charging,
     double-booking, concurrent inventory deductions).

3. BUSINESS LOGIC SECURITY
   - Actions missing rate limiting (account creation, password reset,
     coupon redemption).
   - Business rules that can be bypassed by manipulating client-side
     state or request parameters (e.g., changing a price field in a
     cart payload).
   - Privilege escalation paths where a user can perform actions above
     their role by replaying or modifying requests.
   - Insecure Direct Object References (IDOR) in business-critical
     operations.

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (MCP query, grep match,
    code read, LSP trace)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 2 — UX Clarity, Usability & Client-Side Security

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical and High-Priority UX Clarity, Usability Issues, and
Client-Side Security.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Search for error handling patterns (catch, onError,
  ErrorBoundary), loading states, security patterns (localStorage,
  innerHTML, dangerouslySetInnerHTML, document.cookie).
- Playwright MCP: Navigate actual user flows to confirm broken states.
  Capture the accessibility tree to verify error/loading states render.
- LSP/Serena: Use find-all-references on ErrorBoundary components and
  error handling hooks to verify coverage across the component tree.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: checks whether
  the error state actually reaches the user or is caught upstream.
  Uses Playwright to navigate the flow live if available. Each
  subagent returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. BROKEN USER FLOWS
   - User journeys that dead-end, loop, or lack a completion state
     (e.g., form submission with no success/failure feedback).
   - Core actions (sign up, purchase, onboarding) with missing steps
     that prevent task completion.
   - Navigation traps where the user cannot go back or recover from an
     error state.

2. MISSING OR MISLEADING ERROR COMMUNICATION
   - Backend error codes (4xx, 5xx) that reach the user as raw
     technical messages or silent failures.
   - Optimistic UI updates that never reconcile with a failed backend
     response.
   - Missing loading/skeleton states on async operations, causing the
     user to believe the app is broken or to trigger duplicate actions.

3. CLIENT-SIDE SECURITY
   - Passwords, tokens, PII, or secrets exposed in client-side state,
     URLs, query params, local storage, or console logs.
   - Forms handling sensitive data (login, payment, profile) missing
     CSRF protection.
   - User-generated content rendered without sanitization (XSS vectors).
   - Autocomplete enabled on sensitive fields (credit card, SSN).
   - Auth tokens stored insecurely (localStorage instead of httpOnly
     cookies for session tokens).

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (Playwright snapshot,
    grep match, code read, LSP trace)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 3 — API & Data Integration Flaws & Endpoint Security

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical and High-Priority API & Data Integration Flaws and
API/Endpoint Security.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Find route definitions (app.get, @app.route, router.,
  @Controller), middleware chains, model definitions. Compare field
  names/types between frontend API calls and backend handlers.
- Database MCP: Inspect actual table schemas, column types, and
  constraints. Compare DB column types against API serialization.
- LSP/Serena: Trace route registration → handler → service → DB query.
  Verify auth/validation middleware is in the chain.
- Context7: Look up framework-recommended patterns for request
  validation and auth middleware.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: checks whether
  the mismatch actually causes a bug or a serializer/middleware
  handles it. For missing auth, traces the full middleware chain.
  Uses DB MCP or LSP to corroborate if available. Each subagent
  returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. CONTRACT MISMATCHES
   - Request payloads the frontend sends that the backend does not
     accept (wrong field names, missing required fields, wrong types).
   - Response shapes the frontend expects but the backend does not
     return (missing fields, different nesting, wrong enum values).
   - Pagination, filtering, or sorting parameters the frontend uses
     but the backend ignores.

2. SERIALIZATION & DATA CORRUPTION
   - Type coercion bugs (e.g., string IDs vs. integer IDs, date format
     mismatches, floating point currency values).
   - Unhandled null/undefined values that cause crashes on either side.
   - Large payload handling — missing size limits or chunking for file
     uploads, bulk operations, or list endpoints without pagination.

3. ENDPOINT SECURITY
   - Endpoints missing authentication middleware entirely.
   - Endpoints missing authorization checks (any authenticated user can
     access any resource — Broken Object Level Authorization / BOLA).
   - Mass Assignment vulnerabilities — endpoints accepting fields they
     should not (e.g., `role`, `isAdmin`, `price` in user-facing
     update endpoints).
   - Sensitive data over-exposure in API responses (returning password
     hashes, internal IDs, other users' data, full database records).
   - Sensitive data transmitted without TLS or with downgrade paths.

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s), endpoint(s), and function/component name(s)
  - Evidence: Tool/method that revealed this (DB schema query, LSP
    trace, grep match, Context7 doc reference)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 4 — Architecture, Sync Issues & System Security

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical and High-Priority General Architecture, Sync Issues, and
System-Wide Security.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Search for CORS configuration (Access-Control,
  cors()), security headers (helmet, CSP, X-Frame), session/token
  management patterns. Find and inspect config files.
- Sequential Thinking: Model the system architecture as a dependency
  graph. Reason through failure cascading scenarios step-by-step.
- LSP/Serena: Trace imports to build a dependency graph. Identify
  modules with high fan-in (single points of failure).
- GitHub MCP: Check for open dependabot/security alerts. Review recent
  security-related PRs and issues.
- Docker MCP: Inspect Dockerfile and docker-compose configs for running
  as root, exposed ports, missing healthchecks, outdated base images.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: checks whether
  the bottleneck actually causes a system-wide outage or redundancy
  exists. For CORS/header issues, checks if a reverse proxy or CDN
  applies the header upstream. Uses Docker MCP or GitHub MCP to
  corroborate if available. Each subagent returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. ARCHITECTURAL BOTTLENECKS & FAILURES
   - Single points of failure that take down the entire system.
   - N+1 query patterns or unbounded database calls that degrade under
     load.
   - Missing or broken database transactions around multi-step writes
     (partial writes that corrupt state).
   - Synchronous blocking calls in hot paths that kill throughput.

2. STATE SYNCHRONIZATION
   - Frontend state that drifts from the backend (stale caches, missing
     cache invalidation, optimistic updates without rollback).
   - Real-time features (WebSockets, SSE) with no reconnection or
     missed-message recovery logic.
   - Multi-tab or multi-device state conflicts with no resolution
     strategy.

3. SYSTEM SECURITY POSTURE
   - CORS misconfiguration (wildcard origins, credentials with wildcard).
   - Session/token management flaws (no expiry, no rotation, no
     revocation on password change or logout).
   - Missing security headers (CSP, X-Frame-Options, HSTS,
     X-Content-Type-Options).
   - No audit logging for sensitive actions (admin operations, data
     deletion, permission changes).
   - Vulnerable or outdated infrastructure dependencies (web server,
     runtime, OS-level packages).

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (Sequential Thinking
    analysis, GitHub alert, Docker inspect, grep match)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 5 — Missing Critical UX/UI Features & Interface Integrity

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical Missing UX/UI Features, Visual Design Gaps, and
Interface Security.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Search for accessibility patterns (aria-, role=,
  tabIndex), route definitions, responsive breakpoints (@media,
  useMediaQuery). Find all page/view/screen components.
- Playwright MCP: Render key pages and capture the accessibility tree.
  Check ARIA labels, keyboard navigation, responsive behavior. Visit
  routes to verify they render (not 404/blank).
- LSP/Serena: Find all route definitions and match against navigation
  links to detect orphaned or unreachable routes.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: checks whether
  the user journey actually requires the missing screen or the flow
  is handled differently. For orphaned routes, checks dynamic
  navigation and deep links. Uses Playwright to render the page if
  available. Each subagent returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. MISSING SCREENS & JOURNEY GAPS
   - Mandatory user journey steps with no corresponding UI (e.g., email
     verification, password reset, checkout confirmation, terms
     acceptance, account deletion).
   - Backend capabilities (CRUD operations, settings, admin panels)
     with no frontend surface at all.
   - Empty states, zero-data states, and first-run experiences that are
     completely absent.

2. ACCESSIBILITY & RESPONSIVE DESIGN
   - Core interactive elements (forms, modals, navigation) that are
     unreachable via keyboard.
   - Missing ARIA labels, roles, or live regions on dynamic content
     that make the app unusable for screen reader users.
   - Layouts that are completely broken or unusable on mobile viewports
     (< 768px).
   - Color contrast failures on primary text and interactive elements
     that violate WCAG 2.1 AA.

3. INTERFACE SECURITY
   - Sensitive data (SSN, full card numbers, passwords) displayed in
     plain text with no masking or reveal toggle.
   - Sensitive state-changing pages (payment, account deletion, admin
     actions) missing clickjacking protections (framebusting +
     X-Frame-Options/CSP frame-ancestors).
   - File upload components with no client-side type/size validation,
     no server-side validation, or that display user-uploaded content
     without sanitization.
   - Copy/paste not disabled on password confirmation fields (allows
     paste-jacking attacks in certain contexts).

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (Playwright a11y tree,
    LSP route trace, grep match, code read)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 6 — Testing Coverage, QA & Supply Chain Security

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical Testing Coverage, Quality Assurance (QA) Gaps, and
Supply Chain Security.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Find test files and match against source files to
  identify untested modules. Inspect CI/CD configs. Search for hardcoded
  secret patterns (API_KEY, SECRET, PASSWORD, token =). Check lock files.
- GitHub MCP: Check CI/CD workflow files for test gates. Check
  dependabot alerts and security advisories. Review dependency graph.
- Context7: Check if installed dependency versions have known
  deprecations, breaking changes, or security patches available.
- security-guidance plugin: Run supply chain security checks against
  dependency manifests.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: checks whether
  the untested code is on a critical path or a low-impact utility.
  For potential hardcoded secrets, checks if it's a placeholder,
  example, or test fixture. Uses GitHub MCP or Context7 to
  corroborate if available. Each subagent returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. MISSING TESTS FOR HIGH-RISK FLOWS
   - Authentication flows (login, logout, token refresh, password
     reset) with zero or trivial test coverage.
   - Payment/billing flows with no integration or end-to-end tests.
   - Authorization logic (role checks, resource ownership) untested.
   - Data mutation endpoints (create, update, delete) with no tests
     for validation rules, edge cases, or error paths.

2. DEPLOYMENT & CI/CD RISKS
   - No automated test gate in the deployment pipeline — broken code
     can ship without any test run.
   - Missing linting, type checking, or static analysis in CI.
   - No staging or preview environment — changes go directly to
     production.
   - Database migration scripts with no rollback mechanism or that run
     without a dry-run step.

3. SUPPLY CHAIN & SECRET MANAGEMENT
   - Dependencies with known Critical or High CVEs (check
     package.json, requirements.txt, Gemfile, go.mod, etc.).
   - Hardcoded API keys, database credentials, or secrets anywhere in
     the source code (including test files and CI configs).
   - Environment variables with sensitive defaults committed to version
     control (.env files without .gitignore coverage).
   - Lock files (package-lock.json, yarn.lock, poetry.lock) missing
     from version control, allowing silent dependency drift.
   - Use of deprecated or unmaintained packages in critical paths.

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (GitHub advisory, Context7
    deprecation check, grep match, CI config read)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 7 — Performance, Caching & Resource Optimization

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical Performance Bottlenecks, Caching Failures, and Resource
Optimization.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Search for queries without .limit() or pagination,
  missing memoization, full library imports. Inspect build configs.
- Database MCP: Run EXPLAIN on critical queries to detect full table
  scans. Check indexes on columns used in WHERE/ORDER BY/JOIN.
- Playwright MCP: Load key pages and inspect network waterfall for
  bundle sizes, unoptimized images, render-blocking resources.
- LSP/Serena: Trace from list endpoints through service layers to DB
  calls. Identify N+1 patterns (DB call inside a loop).

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: checks whether
  this is on a hot path affecting real users or an admin endpoint
  called once a day. For N+1 suspects, traces the full call chain
  to confirm separate queries (not a batched loader). Uses DB MCP
  or LSP to corroborate if available. Each subagent returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. FRONTEND PERFORMANCE
   - JavaScript bundles over 500KB (uncompressed) blocking initial
     render.
   - Missing code splitting on route boundaries — the entire app loads
     on the first page.
   - Images served without lazy loading, responsive sizing, or modern
     formats (WebP/AVIF).
   - Expensive re-renders: components re-rendering on every state
     change due to missing memoization, unstable references, or
     context overuse.
   - No static asset caching strategy (missing Cache-Control headers,
     no content hashing in filenames).

2. BACKEND PERFORMANCE
   - Endpoints performing full table scans due to missing database
     indexes on filtered/sorted columns.
   - N+1 query patterns in list endpoints (one query per related
     entity instead of a join or batch load).
   - Missing caching on read-heavy, rarely-changing data (config,
     feature flags, user profiles, reference data).
   - Unbounded list endpoints that return all records with no
     pagination, cursor, or limit.
   - Synchronous heavy computation (image processing, report
     generation, PDF creation) blocking the request thread instead of
     being offloaded to a background job/queue.

3. PERFORMANCE-BASED SECURITY RISKS
   - Endpoints with no rate limiting that perform expensive operations
     (search, report generation, file processing) — trivial DoS
     targets.
   - GraphQL or flexible query endpoints with no depth/complexity
     limits — a single crafted query can exhaust the server.
   - Cache headers exposing sensitive data (e.g., `Cache-Control:
     public` on authenticated responses, CDN caching of user-specific
     pages).
   - Missing request size limits on file upload or JSON body endpoints.

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (EXPLAIN output, Playwright
    network waterfall, LSP N+1 trace, grep match)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 8 — Dead Code, Code Bloat & Maintainability

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical Dead Code, Severe Code Bloat, and Maintainability Risks.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Find function/class/component definitions and search
  for their usage. Find test/debug route files. Cross-reference backend
  route registrations against frontend API call sites.
- LSP/Serena: Use find-all-references on exported functions, components,
  classes, and endpoints. Zero references = confirmed dead code. This
  eliminates false positives from dynamic imports, re-exports, or
  indirect references that text search misses.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity
(or LSP find-all-references returns zero):
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: checks for
  dynamic imports, computed property access, and framework magic
  (decorators, DI containers) that text search misses. Uses LSP
  find-all-references to corroborate if available. Each subagent
  returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. FRONTEND BLOAT
   - Components, pages, or route definitions that are imported/defined
     but never rendered or navigated to.
   - State variables, context providers, or Redux/Zustand slices that
     are initialized but never read.
   - Entire library imports where only a single function is used (e.g.,
     importing all of lodash for `_.get`).
   - Duplicated utility functions or components that serve the same
     purpose with slight variations.

2. BACKEND BLOAT
   - API endpoints that are registered in the router but never called
     by any client.
   - Database models, tables, or fields that are defined but never
     queried or written to.
   - Middleware registered globally but only relevant to a subset of
     routes.
   - Orphaned helper functions, service methods, or utility modules
     with no callers.

3. SECURITY RISKS FROM DEAD CODE
   - Legacy "zombie" API endpoints still active and reachable but not
     protected by current auth/authz middleware — a backdoor into the
     system.
   - Commented-out code containing secrets, credentials, API keys, or
     internal URLs.
   - Unused dependencies that still install and expand the attack
     surface (especially native/binary packages).
   - Debug/development routes (e.g., `/debug`, `/test`, `/admin-seed`)
     still active in production configuration.
   - Feature flags or environment checks that are always true/false,
     leaving dead branches that obscure the actual runtime behavior.

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (LSP 0-references, grep
    cross-reference, Glob route scan)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 9 — Error Handling, Resilience & Failure Recovery

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical Error Handling Gaps, System Resilience, and Failure
Recovery.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Find catch/except/rescue/.catch( patterns. Search
  for empty catch blocks. Search for timeout configurations. Inspect
  error middleware and global error handlers.
- Sequential Thinking: Trace failure paths step-by-step. For each
  external dependency, model what happens when it fails: timeout,
  error, or garbage data.
- LSP/Serena: Find all try/catch blocks and error middleware. Trace
  from catch blocks to verify errors are actually handled — not
  silently swallowed.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: traces the full
  error propagation path. Checks whether an empty catch is
  intentional (higher-level handler exists, fire-and-forget
  operation). Checks if the framework provides default handling.
  Uses LSP to corroborate if available. Each subagent returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. MISSING OR BROKEN ERROR HANDLING
   - try/catch blocks that silently swallow errors (empty catch, or
     catch that only logs without re-throwing or handling).
   - Async operations (API calls, DB queries, file I/O) with no error
     handling at all — unhandled promise rejections or uncaught
     exceptions.
   - Error handlers that leak internal details (stack traces, SQL
     queries, file paths, dependency versions) to the client.
   - Missing global error boundaries (React ErrorBoundary, Express
     error middleware, uncaughtException handlers) — a single thrown
     error crashes the app.

2. RESILIENCE GAPS
   - External service calls (APIs, databases, caches, queues) with no
     timeout configured — a hung dependency freezes the entire app.
   - No retry logic with backoff for transient failures on critical
     paths (payment gateways, email services, third-party APIs).
   - Missing circuit breaker patterns — a failing dependency is called
     repeatedly, cascading the failure.
   - No health check endpoint for load balancers or orchestrators to
     detect and replace unhealthy instances.
   - Missing graceful shutdown — in-flight requests are dropped on
     deploy or restart.

3. FAILURE RECOVERY & OBSERVABILITY
   - No structured logging — errors are logged as unstructured strings
     that are impossible to search or alert on.
   - Critical failures (payment errors, auth failures, data corruption)
     with no alerting mechanism.
   - No idempotency keys on retry-sensitive operations (payment
     charges, email sends, webhook deliveries) — retries cause
     duplicate side effects.
   - Missing dead letter queues or failure tables for background jobs
     — failed jobs vanish silently.

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (Sequential Thinking
    failure trace, LSP catch-block analysis, grep match)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

### Prompt 10 — Data Integrity, Migrations & Storage Security

````
You are a senior full-stack engineer performing a segmented gap analysis
and integration audit using Claude Code. Actively investigate the
codebase using available tools.

SCOPE: Critical Data Integrity Issues, Migration Safety, and Storage
Security.

INVESTIGATION TOOLS (use whichever tools suit each investigation):
- Grep/Glob/Read: Find migration files and inspect for destructive
  operations (DROP, ALTER, TRUNCATE). Search for transaction patterns.
  Inspect schema/model definitions. Find seed scripts.
- Database MCP: Inspect actual schema constraints (NOT NULL, UNIQUE,
  foreign keys, CHECK). List indexes and compare against query patterns.
  Compare column types against application code expectations.
- LSP/Serena: Trace migration execution order. Find multi-table write
  operations and verify transaction wrapping. Use find-references on
  model fields to detect unused columns.

CONFIDENCE ROUTING (do NOT enter plan mode for this):
When you find an issue, assess confidence inline — "confident" means
you can cite the exact file, line, and mechanism without ambiguity:
- Confident → report as [verified].
- Uncertain → collect the issue. After the investigation pass,
  dispatch ALL uncertain issues as parallel subagents (one per issue,
  via the Agent tool). Each subagent independently: checks whether
  the application enforces the constraint in code or the ORM handles
  it. For multi-table writes without explicit transactions, checks
  if the ORM wraps them implicitly. Uses DB MCP or LSP to
  corroborate if available. Each subagent returns one of:
  - Confirmed → report as [verified].
  - Disproven → discard, do not include in output.
  - Plausible but cannot confirm → add to "Investigated but
    Unverified" appendix at the end of the report.

Analyze the codebase and report the following:

1. DATA INTEGRITY
   - Missing database constraints that the application logic assumes
     exist (unique constraints, foreign keys, NOT NULL, check
     constraints).
   - Write operations that span multiple tables/collections without a
     transaction — partial failures leave orphaned or inconsistent
     records.
   - Missing cascade rules or soft-delete logic — deleting a parent
     record orphans child records or violates referential integrity.
   - Concurrent write conflicts with no optimistic locking, versioning,
     or conflict resolution strategy.

2. MIGRATION SAFETY
   - Destructive migrations (DROP COLUMN, DROP TABLE, type changes)
     with no data backup step or reversibility plan.
   - Migrations that lock large tables for extended periods in
     production (e.g., adding a non-nullable column without a default
     to a table with millions of rows).
   - Schema changes that are incompatible with the currently deployed
     application code (breaking the deploy-then-migrate or
     migrate-then-deploy sequence).
   - Seed data or test data scripts that can accidentally run in
     production.

3. STORAGE SECURITY
   - Sensitive data (passwords, tokens, PII, health records, financial
     data) stored in plaintext in the database.
   - Missing encryption at rest for databases, file storage, or
     backups containing sensitive data.
   - Database credentials with excessive privileges (application user
     has DROP, GRANT, or superuser permissions).
   - User-uploaded files stored in publicly accessible buckets/paths
     with no access control.
   - No backup strategy or untested backup restoration — a single
     failure could mean permanent data loss.
   - Log files containing sensitive data (request bodies with
     passwords, PII in error messages) with no redaction.

OUTPUT FORMAT:
For each finding, provide:
  - Severity: CRITICAL or HIGH
  - Location: File path(s) and function/component name(s)
  - Evidence: Tool/method that revealed this (DB schema query, LSP
    transaction trace, migration file read, grep match)
  - Description: What is wrong
  - Impact: What breaks or what an attacker can exploit
  - Remediation: Concrete fix with a code-level suggestion

Group findings into a prioritized remediation plan. CRITICALs first.
Omit Medium/Low severity findings entirely.
````

---

## Output Format

Every prompt enforces a consistent output structure. Each finding should look like:

```
### [CRITICAL] Short Title of the Finding

- **Severity:** CRITICAL
- **Location:** `src/api/payments.ts` → `processPayment()`
- **Evidence:** [verified] Database MCP schema query revealed `price`
  column is DECIMAL(10,2) but API serializes it as JavaScript `number`
  (float). Grep confirmed frontend sends `price` field in POST body.
  Sequential Thinking traced the flow: no server-side price lookup
  exists — the client-supplied value is used directly.
- **Description:** The payment endpoint accepts a `price` field from the
  client payload and uses it directly instead of reading the price from
  the database. An attacker can set any price.
- **Impact:** Financial loss. Any user can purchase any item for $0.01.
- **Remediation:** Remove `price` from the accepted request body schema.
  Read the canonical price from the `products` table using the `productId`
  from the request. Add a server-side assertion that the charged amount
  matches the database price.
```

> [!NOTE]
> Main report findings are tagged **[verified]** in the Evidence field — meaning you can cite the exact file, line, and mechanism, either from clear initial evidence or after confirmation via Sequential Thinking. Findings that were disproven during verification are discarded entirely. Findings that are plausible but could not be confirmed (e.g., need runtime testing) go in an **"Investigated but Unverified"** appendix at the end — visible but separate from the main findings.

---

## Tips for Best Results

> [!TIP]
> **1. Install MCP tools for deeper analysis** — Run the [MCP Setup Prompt](MCP-SETUP.md) to auto-install MCP servers and plugins. Prompts will automatically use them for tool-verified findings (LSP tracing, DB schema inspection, browser testing).

> [!TIP]
> **2. Scope the context to one domain at a time** — Running a prompt against "the entire app" yields shallow results. Instead, run it against your auth system, then your payment system, then your admin panel, etc.

> [!TIP]
> **3. Include both frontend and backend** — These prompts find the *gaps between layers*. Frontend-only or backend-only analysis misses the most dangerous class of bugs.

> [!TIP]
> **4. Re-run after major changes** — Treat this as a recurring audit, not a one-time exercise. Run it before major releases, after large refactors, or on a quarterly cadence.

> [!TIP]
> **5. Combine with automated tooling** — These prompts complement (not replace) tools like ESLint, Semgrep, Snyk, OWASP ZAP, and Lighthouse. Use both.

> [!TIP]
> **6. Customize the role and context line** — If your project uses a specific tech stack, add it to the opening context line:
> ```
> You are a senior full-stack engineer specializing in Next.js 14 (App
> Router), Prisma ORM, and PostgreSQL...
> ```
> This grounds the analysis in your actual stack and produces more specific recommendations.

---

## Tool-Prompt Matrix

Quick reference showing which MCP tools enhance each prompt. All prompts fall back to built-in tools (Grep/Glob/Read/Bash) when MCP tools are unavailable.

| Prompt | Seq. Thinking | Context7 | GitHub MCP | DB MCP | Playwright | LSP/Serena | Docker MCP | security-guidance |
|--------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **1** Logic & Security | `*` | | `*` | | | `*` | | |
| **2** UX & Client Security | | | | | `*` | `*` | | |
| **3** API & Endpoints | | `*` | | `*` | | `*` | | |
| **4** Architecture & System | `*` | | `*` | | | `*` | `*` | |
| **5** UI & Interface | | | | | `*` | `*` | | |
| **6** Testing & Supply Chain | | `*` | `*` | | | | | `*` |
| **7** Performance & Caching | | | | `*` | `*` | `*` | | |
| **8** Dead Code & Bloat | | | | | | `*` | | |
| **9** Error Handling | `*` | | | | | `*` | | |
| **10** Data Integrity | | | | `*` | | `*` | | |

> `*` = tool enhances this prompt when available. **LSP/Serena** is the most broadly useful — it enhances **9 of 10** prompts.

---

## Quick Reference: Which Prompt When?

| Situation | Recommended Prompts |
|---|---|
| Pre-launch checklist | `1` `2` `3` `5` `6` |
| Post-incident review | `4` `9` `10` |
| Security hardening sprint | `1` `3` `4` `6` |
| Performance firefighting | `7` `4` |
| Tech debt paydown | `8` `7` `10` |
| New team onboarding audit | All |
| Pre-pen-test preparation | `1` `3` `4` `6` |
| Accessibility compliance | `5` `2` |

---

## Contributing

Found a gap in the prompts? Have a domain-specific variant (mobile, ML pipelines, infrastructure)?

1. Fork the repo
2. Create a branch (`git checkout -b prompt/your-domain`)
3. Add or modify prompts following the existing structure
4. Submit a PR with a description of what the prompt catches and why it matters

Please keep contributions focused on **Critical and High-priority** findings only — that's the core design principle of this suite.

---

## License

MIT — use it, fork it, adapt it, share it.

---

<p align="center"><strong>Built for developers who ship fast and sleep well.</strong></p>
