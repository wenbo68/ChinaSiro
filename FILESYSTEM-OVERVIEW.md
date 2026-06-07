# Filesystem Overview

## Summary

**ChinaSiro** (`claude-code-sourcemap`) is an unofficial research repository that reconstructs the TypeScript source code of Anthropic's `@anthropic-ai/claude-code` CLI tool (version 2.1.88) from the source maps embedded in the published npm package. The repository contains:

- The original extracted npm package files (`package/`)
- A script to perform the extraction (`extract-sources.js`)
- The reconstructed TypeScript sources (`restored-src/src/`) — 4,756 files including 1,884 `.ts`/`.tsx` source files
- Claude Code agent/command/skill configuration (`.claude/`)

> This repo is for research purposes only. All source code copyright belongs to Anthropic.

---

## Annotated Directory Tree

```
ChinaSiro/
├── README.md                          # Project overview, provenance, and disclaimer
├── extract-sources.js                 # Node.js script: extracts sourcesContent from cli.js.map into restored-src/
├── claude-code-2.1.88.tgz             # Original npm tarball (@anthropic-ai/claude-code@2.1.88)
│
├── .claude/                           # Claude Code agent configuration (hidden folder — not expanded)
│
├── package/                           # Extracted npm package contents
│   ├── package.json                   # Package manifest (name, version, bin, optionalDeps)
│   ├── bun.lock                       # Bun lockfile for the package
│   ├── cli.js                         # Compiled single-file CLI bundle (~13 MB)
│   ├── cli.js.map                     # Source map with embedded sourcesContent (~60 MB) — extraction source
│   ├── sdk-tools.d.ts                 # TypeScript type declarations for SDK tools (~117 KB)
│   ├── LICENSE.md                     # License file
│   ├── README.md                      # Original Anthropic package README
│   └── vendor/                        # Vendored binary dependencies (ripgrep etc.)
│
└── restored-src/                      # Reconstructed TypeScript source tree
    ├── src/                           # Main source root (mirrors internal Anthropic repo layout)
    │   ├── main.tsx                   # CLI entry point / Commander.js command definitions (~784 KB)
    │   ├── query.ts                   # Core API query loop — streaming, tool calls, conversation turns
    │   ├── QueryEngine.ts             # Higher-level query orchestrator (compaction, state, attribution)
    │   ├── Tool.ts                    # Tool interface definition and utilities
    │   ├── tools.ts                   # Tool registry — assembles the active tool list
    │   ├── commands.ts                # Slash-command definitions
    │   ├── context.ts                 # System/user context builder (git status, CLAUDE.md, memories)
    │   ├── cost-tracker.ts            # Token cost tracking
    │   ├── costHook.ts                # React hook for cost display
    │   ├── dialogLaunchers.tsx        # Dialog launcher helpers (Ink)
    │   ├── history.ts                 # Conversation history management
    │   ├── ink.ts                     # Ink render wrapper with ThemeProvider
    │   ├── interactiveHelpers.tsx     # Interactive REPL helper functions (~57 KB)
    │   ├── projectOnboardingState.ts  # Project onboarding state
    │   ├── query.ts                   # (see above)
    │   ├── replLauncher.tsx           # REPL screen launcher
    │   ├── setup.ts                   # One-time initialization (telemetry, config, trust)
    │   ├── Task.ts                    # Task type definitions
    │   ├── tasks.ts                   # Task registry helpers
    │   ├── assistant/                 # KAIROS assistant mode
    │   ├── bootstrap/                 # Module-level session singletons (CWD, session ID, tokens)
    │   ├── bridge/                    # Remote Control / Bridge mode
    │   ├── buddy/                     # AI companion UI
    │   ├── cli/                       # CLI argument parsing helpers
    │   ├── commands/                  # Slash-command implementations (40+ commands)
    │   ├── components/                # React/Ink UI components (messages, permissions, input)
    │   ├── constants/                 # Shared constant values
    │   ├── context/                   # React Context providers
    │   ├── coordinator/               # Multi-agent coordinator mode
    │   ├── entrypoints/               # CLI entrypoints (cli.tsx, init.ts)
    │   ├── hooks/                     # React hooks
    │   ├── ink/                       # Ink framework internals
    │   ├── keybindings/               # Keyboard shortcut definitions
    │   ├── memdir/                    # Memory directory management
    │   ├── migrations/                # Config/data migration logic
    │   ├── moreright/                 # Additional right-panel UI
    │   ├── native-ts/                 # Native TypeScript bindings
    │   ├── outputStyles/              # Output formatting styles
    │   ├── plugins/                   # Plugin system
    │   ├── query/                     # Query sub-modules
    │   ├── remote/                    # Remote session support
    │   ├── schemas/                   # Zod/JSON schemas
    │   ├── screens/                   # Full-screen Ink views (REPL, etc.)
    │   ├── server/                    # Embedded server mode
    │   ├── services/                  # API clients, MCP, analytics
    │   ├── skills/                    # Skills system
    │   ├── state/                     # App state management (Zustand-style store)
    │   ├── tasks/                     # Task management sub-modules
    │   ├── tools/                     # Tool implementations (Bash, FileEdit, Grep, MCP, 30+)
    │   ├── types/                     # TypeScript type declarations
    │   ├── upstreamproxy/             # Upstream proxy support
    │   ├── utils/                     # Utility functions (git, model, auth, env, etc.)
    │   ├── vim/                       # Vim mode
    │   └── voice/                     # Voice interaction (push-to-talk)
    ├── node_modules/                  # Installed npm dependencies for restored-src
    └── vendor/                        # Vendored binaries for restored-src
```
