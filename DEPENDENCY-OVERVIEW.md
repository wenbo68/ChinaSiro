# Dependency Overview

This repository contains two distinct dependency contexts:

---

## 1. `package/package.json` — Published npm Package (`@anthropic-ai/claude-code@2.1.88`)

This is the **extracted original npm package**. It ships as a pre-compiled single-file bundle (`cli.js`) and declares **no runtime dependencies** — all code is bundled. The only external dependencies are optional platform-specific native image-processing binaries.

### `dependencies`

_None._ All runtime code is inlined into `cli.js` at build time.

### `optionalDependencies`

These are platform-specific builds of [sharp](https://sharp.pixelplumbing.com/), a high-performance Node.js image processing library. They are loaded at runtime only if the matching platform is detected.

| Package | Version | Platform |
|---|---|---|
| `@img/sharp-darwin-arm64` | `^0.34.2` | macOS Apple Silicon |
| `@img/sharp-darwin-x64` | `^0.34.2` | macOS Intel |
| `@img/sharp-linux-arm` | `^0.34.2` | Linux ARM 32-bit |
| `@img/sharp-linux-arm64` | `^0.34.2` | Linux ARM 64-bit |
| `@img/sharp-linux-x64` | `^0.34.2` | Linux x86-64 |
| `@img/sharp-linuxmusl-arm64` | `^0.34.2` | Linux musl ARM 64-bit (Alpine) |
| `@img/sharp-linuxmusl-x64` | `^0.34.2` | Linux musl x86-64 (Alpine) |
| `@img/sharp-win32-arm64` | `^0.34.2` | Windows ARM 64-bit |
| `@img/sharp-win32-x64` | `^0.34.2` | Windows x86-64 |

**Purpose:** Used for image processing within Claude Code (e.g., resizing/encoding screenshots for vision model input).

### Engine requirement

- `node >= 18.0.0`

---

## 2. `restored-src/` — Reconstructed Source Tree

The `restored-src/` directory contains the decompiled TypeScript sources and a `node_modules/` folder installed alongside them. No separate `package.json` manifest is present at the root of `restored-src/` in this branch — the directory is a source-map reconstruction artifact, not a standalone buildable package.

Based on the source structure, the original Claude Code source (before bundling) depends on libraries including but not limited to:

- **`@anthropic-ai/sdk`** — Anthropic API client
- **`commander`** — CLI argument parsing
- **`ink`** / **`react`** / **`react-reconciler`** — Terminal UI framework
- **`zod`** — Schema validation
- **`@modelcontextprotocol/sdk`** — MCP client/server
- **Various AWS/GCP SDKs** — Bedrock and Vertex AI provider support
- **`sharp`** — Image processing (see optional deps above)

> Exact transitive dependency versions are embedded inside `package/cli.js` (the compiled bundle) and are not enumerated separately in this repository.

---

## 3. Root-level scripts

`extract-sources.js` is a standalone Node.js script with **no external dependencies** — it uses only Node.js built-in modules (`fs`, `path`, `zlib`).
