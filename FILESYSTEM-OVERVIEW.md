# Filesystem Overview

- Date: 2026/06/07
- Summary: `claude-code-sourcemap` (a.k.a. ChinaSiro) is an **unofficial** repository that reconstructs the TypeScript source of Anthropic's Claude Code CLI (`@anthropic-ai/claude-code`, version `2.1.88`) by extracting the `sourcesContent` field from the published npm package's `cli.js.map` source map. It contains the original npm tarball, the unpacked `package/` (bundled `cli.js`, source map, vendor binaries), a small extraction script, and the resulting `restored-src/` tree of ~1900 reconstructed `.ts`/`.tsx` files plus restored `node_modules`. It is for research/study only and does not represent Anthropic's real internal repo layout.

## Structure

```
ChinaSiro/
├── README.md                       — Project intro: explains this is a source-map reconstruction of Claude Code 2.1.88 (bilingual, research-only disclaimer)
├── claude-code-2.1.88.tgz          — The original published npm tarball for @anthropic-ai/claude-code@2.1.88 (source of everything reconstructed)
├── extract-sources.js              — Node script using `source-map` to read package/cli.js.map and write each sourcesContent entry into restored-src/
├── .git/                           — Git repository metadata (dot-folder leaf, not expanded; ~74M of history)
├── package/                        — Unpacked contents of the npm tarball (bundled CLI + maps + vendor binaries)   (expanded)
│   ├── LICENSE.md                  — Anthropic PBC proprietary license notice
│   ├── README.md                   — Upstream npm package README for Claude Code
│   ├── bun.lock                    — Bun lockfile for the package (only optional @img/sharp platform binaries)
│   ├── cli.js                      — The shipped, bundled & minified Claude Code CLI executable (~13MB; `claude` bin entry)
│   ├── cli.js.map                  — Source map for cli.js (~60MB); its sourcesContent is what extract-sources.js mines
│   ├── package.json                — npm manifest for @anthropic-ai/claude-code@2.1.88 (bin, engines, optional sharp deps)
│   ├── sdk-tools.d.ts              — TypeScript declarations for the Claude Code SDK tool definitions
│   └── vendor/                     — Prebuilt native binaries shipped with the CLI, per OS/arch   (expanded)
│       ├── audio-capture/          — Native audio-capture .node addons (voice input) per platform
│       │   ├── arm64-darwin/audio-capture.node   — macOS arm64 audio capture addon
│       │   ├── arm64-linux/audio-capture.node    — Linux arm64 audio capture addon
│       │   ├── arm64-win32/audio-capture.node    — Windows arm64 audio capture addon
│       │   ├── x64-darwin/audio-capture.node     — macOS x64 audio capture addon
│       │   ├── x64-linux/audio-capture.node      — Linux x64 audio capture addon
│       │   └── x64-win32/audio-capture.node      — Windows x64 audio capture addon
│       └── ripgrep/                — Bundled ripgrep search binaries per platform (used by the Grep tool)
│           ├── COPYING             — ripgrep license file
│           ├── arm64-darwin/rg     — macOS arm64 ripgrep binary
│           ├── arm64-linux/rg      — Linux arm64 ripgrep binary
│           ├── arm64-win32/rg      — Windows arm64 ripgrep binary
│           ├── x64-darwin/rg       — macOS x64 ripgrep binary
│           ├── x64-linux/rg        — Linux x64 ripgrep binary
│           └── x64-win32/rg        — Windows x64 ripgrep binary
└── restored-src/                   — Reconstructed source tree produced by extract-sources.js   (expanded)
    ├── node_modules/               — Reconstructed third-party module sources recovered from the source map (~197 packages, 26M)
    │                                  Representative packages: @anthropic-ai/{sdk,bedrock-sdk,vertex-sdk,foundry-sdk,mcpb,sandbox-runtime},
    │                                  @ant/{computer-use-mcp,computer-use-input,computer-use-swift,claude-for-chrome-mcp},
    │                                  @modelcontextprotocol, @aws-sdk, @azure, @opentelemetry, @commander-js, @inquirer,
    │                                  axios, chalk, chokidar, ajv, react/ink stack, etc. (not individually expanded)
    ├── src/                        — The reconstructed Claude Code application source (~1900 .ts/.tsx files across 37 areas)   (expanded one level)
    │   ├── QueryEngine.ts          — High-level conversation orchestrator wrapping the core query loop
    │   ├── Task.ts                 — Task type/abstraction used by the agent/task subsystem
    │   ├── Tool.ts                 — Core Tool interface definition and tool-lookup utilities
    │   ├── commands.ts             — Slash-command registry assembly
    │   ├── context.ts              — Builds system/user context (git status, date, CLAUDE.md) for API calls
    │   ├── cost-tracker.ts         — Tracks token/cost usage across a session
    │   ├── costHook.ts             — Hook glue for cost reporting
    │   ├── dialogLaunchers.tsx     — Helpers to launch interactive Ink dialogs
    │   ├── history.ts              — Conversation/session history management
    │   ├── ink.ts                  — Ink render wrapper with theme injection
    │   ├── interactiveHelpers.tsx  — Shared helpers for interactive REPL flows
    │   ├── main.tsx                — Primary Commander.js CLI definition and command registration (~800KB reconstructed)
    │   ├── projectOnboardingState.ts — Tracks first-run/onboarding state per project
    │   ├── query.ts                — Core API query loop: streaming, tool-call processing, turn management
    │   ├── replLauncher.tsx        — Entry helper that launches the interactive REPL screen
    │   ├── setup.ts                — One-time setup / installation logic
    │   ├── tasks.ts                — Task subsystem entry helpers
    │   ├── tools.ts                — Tool registry assembling the available tool list
    │   ├── assistant/              — KAIROS assistant-mode logic
    │   ├── bootstrap/              — Session-global singletons (session id, cwd, project root)
    │   ├── bridge/                 — Remote control / bridge mode (sessions, auth, transport; ~31 files)
    │   ├── buddy/                  — AI "buddy" companion UI (~6 files)
    │   ├── cli/                    — CLI plumbing: handlers/ and transports/ subfolders (~19 files)
    │   ├── commands/               — ~200 slash-command implementations (config, doctor, resume, agents, voice, stats, skills, tasks, share, vim, …)
    │   ├── components/             — ~390 Ink/React UI components (permissions, design-system, messages, PromptInput, Settings, mcp, diff, wizard, …)
    │   ├── constants/              — Shared constants (tool whitelists, defaults; ~21 files)
    │   ├── context/                — React Context providers (~9 files)
    │   ├── coordinator/            — Multi-agent coordinator mode
    │   ├── entrypoints/            — Process entrypoints (cli bootstrap, MCP/daemon modes; ~8 files)
    │   ├── hooks/                  — React hooks incl. toolPermission/ and notifs/ (~104 files)
    │   ├── ink/                    — Forked Ink framework: components/, hooks/, layout/, events/, termio/ (~96 files)
    │   ├── keybindings/            — Keyboard binding definitions and handling (~14 files)
    │   ├── memdir/                 — Memory-directory / persistent memory storage (~8 files)
    │   ├── migrations/             — Config/state migration routines (~11 files)
    │   ├── moreright/              — Misc feature module
    │   ├── native-ts/              — TypeScript wrappers around native addons (~4 files)
    │   ├── outputStyles/           — Output styling presets
    │   ├── plugins/                — Plugin system core (~2 files)
    │   ├── query/                  — Query-loop helper modules (~4 files)
    │   ├── remote/                 — Remote session handling (~4 files)
    │   ├── schemas/                — Shared data schemas
    │   ├── screens/                — Top-level Ink screens incl. the REPL (~3 files)
    │   ├── server/                 — Local server mode (~3 files)
    │   ├── services/               — Backend services: api/, mcp/, oauth/, lsp/, analytics/, compact/, MagicDocs/, … (~130 files)
    │   ├── skills/                 — Skill system implementation (~20 files)
    │   ├── state/                  — App state store, selectors, providers (~6 files)
    │   ├── tasks/                  — Task runners: RemoteAgentTask/, LocalAgentTask/, LocalShellTask/, DreamTask/, … (~12 files)
    │   ├── tools/                  — ~50 built-in tool implementations (Bash, FileEdit, FileRead, Glob, Grep, MCP, WebFetch, Agent, Task*, Skill, …; ~184 files)
    │   ├── types/                  — Shared TypeScript type definitions (~11 files)
    │   ├── upstreamproxy/          — Upstream proxy support (~2 files)
    │   ├── utils/                  — ~560 utility modules: model/, permissions/, git/, mcp/, shell/, sandbox/, telemetry/, computerUse/, …
    │   ├── vim/                    — Vim-mode editing support (~5 files)
    │   └── voice/                  — Voice interaction support
    └── vendor/                     — Reconstructed vendor source wrappers for native NAPI addons   (expanded)
        ├── audio-capture-src/index.ts      — TS source for the audio-capture native addon wrapper
        ├── image-processor-src/index.ts    — TS source for the image-processor native addon wrapper
        ├── modifiers-napi-src/index.ts     — TS source for the keyboard-modifiers native addon wrapper
        └── url-handler-src/index.ts        — TS source for the URL-scheme-handler native addon wrapper
```
