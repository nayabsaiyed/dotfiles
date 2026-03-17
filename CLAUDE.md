# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a personal development environment. The primary active project is **OpenClaw** (`clawdbot/`), a multi-channel personal AI assistant platform. Other active projects: `financial-aid-website/` (Next.js) and `clawd/` (AI agent workspace). Legacy Java microservices live in `api/` and `configserver/`.

## Projects

| Directory | Stack | Status |
|---|---|---|
| `clawdbot/` | TypeScript/Node.js, pnpm monorepo | Active — primary project |
| `financial-aid-website/` | Next.js 15, React 19, Tailwind | Active |
| `clawd/` | Markdown, git-tracked | Active agent workspace |
| `api/` | Java, Spring Boot, Maven | Legacy |
| `configserver/` | Java, Spring Cloud Config | Legacy |

---

## OpenClaw (`clawdbot/`)

> Full guidelines are in `clawdbot/AGENTS.md` (symlinked as `clawdbot/CLAUDE.md`). This section is a summary.

### Commands

```bash
pnpm install          # Install deps (also: bun install)
pnpm dev              # Run CLI in dev mode
pnpm build            # Type-check + build
pnpm tsgo             # TypeScript checks only
pnpm check            # Format + lint + type check (run before commits)
pnpm format:fix       # Auto-fix formatting (oxfmt --write)
pnpm test             # Vitest unit tests
pnpm test:coverage    # Tests with V8 coverage (70% threshold)
pnpm test:e2e         # End-to-end tests
```

Live/integration tests require environment variables:
```bash
CLAWDBOT_LIVE_TEST=1 pnpm test:live      # OpenClaw live tests
LIVE=1 pnpm test:live                    # Includes provider live tests
pnpm test:docker:all                     # Full Docker suite
```

### Architecture

- **Runtime**: Node ≥22 (also Bun-compatible); prefer Bun for TypeScript execution
- **Package manager**: pnpm 10.23.0 with workspaces (`packages/*`, `extensions/*`, `ui/`)
- **Build**: tsdown/rolldown; output in `dist/`
- **Testing**: Vitest, colocated `*.test.ts` files, e2e in `*.e2e.test.ts`
- **Linting**: oxlint + oxfmt (`pnpm check`)

**Source layout:**
- `src/cli/` — CLI entry & wiring
- `src/commands/` — command implementations
- `src/channels/`, `src/routing/` — core channel routing
- `src/telegram/`, `src/discord/`, `src/slack/`, `src/signal/`, `src/imessage/`, `src/web/` — built-in channel integrations
- `src/infra/` — storage, config
- `src/media/` — audio/video pipeline
- `src/terminal/palette.ts`, `src/terminal/table.ts` — CLI UI primitives (use these; don't hand-roll)
- `extensions/*` — channel plugins (MS Teams, Matrix, Zalo, voice-call, etc.)
- `apps/ios/`, `apps/macos/`, `apps/android/` — native clients
- `docs/` — Mintlify docs (root-relative internal links, no `.md` extension)

**Key rules:**
- Plugin runtime deps must be in `dependencies`; avoid `workspace:*` in plugin `dependencies`
- When refactoring shared logic (routing, allowlists, onboarding), check **all** channels—both built-in and extensions
- Use `src/cli/progress.ts` for CLI progress; `src/terminal/palette.ts` for colors
- SwiftUI: use `Observation` framework (`@Observable`, `@Bindable`), not `ObservableObject`
- Docs (`docs/zh-CN/**`) are generated—do not edit directly
- Commit via `scripts/committer "<msg>" <file...>` (not manual `git add`)
- Never update the Carbon dependency; never edit `node_modules`
- Dependencies in `pnpm.patchedDependencies` must use exact versions (no `^`/`~`)

**Version locations** (when bumping): `package.json`, `apps/android/app/build.gradle.kts`, `apps/ios/Sources/Info.plist`, `apps/ios/Tests/Info.plist`, `apps/macos/Sources/OpenClaw/Resources/Info.plist`, `docs/install/updating.md`

**Multi-agent safety:** never stash, never switch branches, never modify worktrees unless explicitly asked; scope commits to your own changes only.

---

## Financial Aid Website (`financial-aid-website/`)

```bash
npm run dev    # Dev server on :3000
npm run build  # Production build
npm run lint   # ESLint
```

Stack: Next.js 15, React 19, TypeScript, Tailwind CSS, Radix UI.

---

## Clawd (`clawd/`)

Agent workspace for AI context management. Key files: `SOUL.md`, `IDENTITY.md`, `AGENTS.md`, `TOOLS.md`, `BOOTSTRAP.md`, `USER.md`. Memory logs in `memory/`. Git-tracked; treat as configuration, not application code.
