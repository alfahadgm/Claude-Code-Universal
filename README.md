# Claude Code Universal

> **A battle-tested prompt suite for conducting deep, structured codebase audits with AI assistants — powered by Claude Code and MCP integration.**

<p align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

</p>

---

## What Is This?

Standard code reviews catch syntax issues. This toolkit catches **systemic failures** — the gaps between what your frontend assumes, what your backend delivers, what your tests cover, and what an attacker can exploit.

| | |
|---|---|
| **10 audit prompts** | Covering logic, UX, APIs, architecture, testing, performance, dead code, error handling, and data integrity |
| **Auto-detection setup** | Identifies your tech stack and installs only the relevant MCP servers, plugins, and hooks |
| **Signal, not noise** | Every prompt filters to **Critical and High-priority issues only** with actionable remediation steps |

## How It Works

```
Your Project  +  Claude Code  +  These Prompts  =  Comprehensive Audit
```

| Step | Action |
|------|--------|
| **1. Setup** (one-time) | Paste the [MCP Setup Prompt](MCP-SETUP.md) into Claude Code. It auto-detects your stack and installs matching tools. |
| **2. Detect** | Run Prompt 0 to identify available MCP tools for subsequent prompts. |
| **3. Audit** | Run Prompts 1-10 sequentially, or pick the ones relevant to your situation. |
| **4. Ship** | Consolidate findings into a remediation backlog and prioritize. |

## The Prompts

| # | Domain | What It Catches |
|---|--------|----------------|
| **0** | Tool Detection | Identifies available MCP servers, plugins, and built-in tools |
| **1** | Logic & Business Security | Race conditions, auth bypasses, state machine flaws |
| **2** | UX & Client-Side Security | XSS vectors, misleading UI states, accessibility gaps |
| **3** | API & Endpoint Security | Broken access control, data leaks, integration mismatches |
| **4** | Architecture & System Security | Circular dependencies, config drift, infrastructure holes |
| **5** | UI & Interface Integrity | Missing error states, broken flows, incomplete CRUD |
| **6** | Testing & Supply Chain | Coverage gaps, flaky tests, vulnerable dependencies |
| **7** | Performance & Optimization | N+1 queries, memory leaks, missing caching |
| **8** | Dead Code & Maintainability | Unused exports, duplicate logic, code bloat |
| **9** | Error Handling & Resilience | Silent failures, missing retries, cascading errors |
| **10** | Data Integrity & Storage | Migration risks, orphaned records, backup gaps |

## Quick Start

### With Claude Code (recommended)

```bash
# 1. Open Claude Code in your project
cd your-project && claude

# 2. Paste the MCP Setup Prompt (one-time setup)
#    Copy contents of MCP-SETUP.md into Claude Code

# 3. Run Prompt 0 to detect tools, then Prompts 1-10
#    Copy each prompt from AUDIT-PROMPTS.md sequentially
```

### Without Claude Code

> Every prompt works with **any AI assistant**. Skip Prompt 0 and paste prompts directly — they fall back to static analysis gracefully.

## Which Prompts Should I Run?

| Situation | Recommended Prompts |
|---|---|
| Pre-launch checklist | 0, 1, 2, 3, 5, 6 |
| Post-incident review | 0, 4, 9, 10 |
| Security hardening sprint | 0, 1, 3, 4, 6 |
| Performance firefighting | 0, 7, 4 |
| Tech debt paydown | 0, 8, 7, 10 |
| New team onboarding audit | All (run sequentially) |
| Pre-pen-test preparation | 0, 1, 3, 4, 6 |

## MCP Auto-Setup

The [setup prompt](MCP-SETUP.md) auto-detects and configures:

- **Languages & Frameworks:** Node, TypeScript, Python, Rust, Go, Java, C#, Ruby, PHP, Elixir, Swift, Flutter, and more
- **Infrastructure:** Docker, Terraform, Kubernetes, Pulumi
- **Databases:** PostgreSQL, MySQL, MongoDB, SQLite, Supabase, Prisma, Drizzle
- **Testing:** Playwright, Cypress, Jest, Vitest, pytest
- **VCS:** GitHub, GitLab, Bitbucket, Azure DevOps
- **Monorepos:** Lerna, pnpm workspaces, Nx, Turborepo

Works on macOS, Linux, Windows, and WSL with OS-specific command handling.

## Key Design Principles

| Principle | Description |
|-----------|-------------|
| **One domain per prompt** | Prevents shallow, scattered analysis |
| **Critical and High only** | No low-severity noise |
| **Security baked in** | Every prompt includes a security lens for its domain |
| **Tool-adaptive** | Uses MCP tools when available, falls back to built-in tools when not |
| **Actionable output** | Every finding includes severity, evidence, impact, and remediation |

## Output Format

Each finding follows a standardized structure:

```
Severity: Critical | High
Location: file:line
Evidence: [what was found]
Description: [what's wrong]
Impact: [what could happen]
Remediation: [how to fix it]
```

## Contributing

Found a gap? Have a domain-specific variant (mobile, ML pipelines, infrastructure)?

1. Fork the repo
2. Create a branch (`git checkout -b prompt/your-domain`)
3. Add or modify prompts following the existing structure
4. Submit a PR describing what the prompt catches and why it matters

Keep contributions focused on **Critical and High-priority findings** — that's the core design principle.

## License

MIT — use it, fork it, adapt it, share it.

---

<p align="center"><strong>Built for developers who ship fast and sleep well.</strong></p>
