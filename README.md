# Claude Code Universal

Structured codebase audit prompts for AI assistants — powered by Claude Code and MCP integration. Catches systemic failures across logic, security, APIs, architecture, testing, performance, and more. Filters to **Critical and High-priority issues only**.

## Quick Start

1. Open Claude Code in your project: `cd your-project && claude`
2. *(Optional)* Paste the [MCP Setup Prompt](MCP-SETUP.md) for deeper, tool-verified analysis
3. Pick a prompt from [AUDIT-PROMPTS.md](AUDIT-PROMPTS.md) and paste it in

> Works with **any AI assistant** — prompts fall back to static analysis without Claude Code.

## Which Prompts Should I Run?

| Situation | Recommended Prompts |
|---|---|
| Pre-launch checklist | 1, 2, 3, 5, 6 |
| Post-incident review | 4, 9, 10 |
| Security hardening sprint | 1, 3, 4, 6 |
| Performance firefighting | 7, 4 |
| Tech debt paydown | 8, 7, 10 |
| New team onboarding audit | All |
| Pre-pen-test preparation | 1, 3, 4, 6 |

## MCP Auto-Setup

The [setup prompt](MCP-SETUP.md) auto-detects and configures:

- **Languages & Frameworks:** Node, TypeScript, Python, Rust, Go, Java, C#, Ruby, PHP, Elixir, Swift, Flutter, and more
- **Infrastructure:** Docker, Terraform, Kubernetes, Pulumi
- **Databases:** PostgreSQL, MySQL, MongoDB, SQLite, Supabase, Prisma, Drizzle
- **Testing:** Playwright, Cypress, Jest, Vitest, pytest
- **VCS:** GitHub, GitLab, Bitbucket, Azure DevOps
- **Monorepos:** Lerna, pnpm workspaces, Nx, Turborepo

Works on macOS, Linux, Windows, and WSL.

## Design Principles

- **One domain per prompt** — prevents shallow, scattered analysis
- **Critical and High only** — no low-severity noise
- **Security baked in** — every prompt includes a security lens
- **Tool-adaptive** — uses MCP tools when available, falls back gracefully
- **Parallel verification** — uncertain findings verified by subagents
- **Actionable output** — every finding includes severity, evidence, impact, and remediation

## Contributing

PRs welcome. Fork, branch (`git checkout -b prompt/your-domain`), and submit. Keep contributions focused on Critical and High-priority findings.

## License

MIT
