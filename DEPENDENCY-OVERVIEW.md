# Dependency Overview

- Date: 2026/06/07
- Sources: `package/package.json` (manifest), `package/bun.lock` (lockfile mirror)

> Note: This repo is a source-map reconstruction of the published `@anthropic-ai/claude-code@2.1.88` npm package. The only dependency manifest is the upstream package's `package/package.json`. It declares **no runtime `dependencies`** (everything is pre-bundled into `package/cli.js`) and only a set of **optional, platform-specific `@img/sharp` native binaries**. `package/bun.lock` mirrors this exactly (`packages: {}`, only the optional sharp entries). The `restored-src/node_modules/` tree (~197 packages such as the Anthropic SDKs, MCP SDK, AWS/Azure SDKs, axios, chalk, the React/Ink stack) was recovered from the source map but is **not** declared in any manifest, so per instructions those are not enumerated here.

## List

### `package/package.json` — optionalDependencies

All entries are platform-specific prebuilt binaries of the **sharp** image-processing library (libvips bindings). The correct one for the host OS/architecture is selected at install time; the rest are skipped. In this project, sharp powers image handling — resizing/normalizing pasted and read images (`src/utils/imageResizer.ts`, `src/tools/FileReadTool/imageProcessor.ts`) and image-paste/heap-dump utilities.

- `@img/sharp-darwin-arm64`: `^0.34.2`
    - sharp native binary for macOS on Apple Silicon (arm64); used for image resize/convert in the image tools.
- `@img/sharp-darwin-x64`: `^0.34.2`
    - sharp native binary for macOS on Intel (x64).
- `@img/sharp-linux-arm`: `^0.34.2`
    - sharp native binary for Linux on 32-bit ARM.
- `@img/sharp-linux-arm64`: `^0.34.2`
    - sharp native binary for Linux on arm64.
- `@img/sharp-linux-x64`: `^0.34.2`
    - sharp native binary for Linux on x64 (glibc).
- `@img/sharp-linuxmusl-arm64`: `^0.34.2`
    - sharp native binary for musl-based Linux (e.g. Alpine) on arm64.
- `@img/sharp-linuxmusl-x64`: `^0.34.2`
    - sharp native binary for musl-based Linux (e.g. Alpine) on x64.
- `@img/sharp-win32-arm64`: `^0.34.2`
    - sharp native binary for Windows on arm64.
- `@img/sharp-win32-x64`: `^0.34.2`
    - sharp native binary for Windows on x64.

## Taxonomy

- Image processing (the entire declared dependency set): platform-specific prebuilt native binaries of the `sharp` (libvips) image library, used by the CLI's image-handling utilities and the FileRead tool to decode/resize/convert images. Exactly one is loaded per host platform; the others are optional fallbacks.
   - `@img/sharp-darwin-arm64`
   - `@img/sharp-darwin-x64`
   - `@img/sharp-linux-arm`
   - `@img/sharp-linux-arm64`
   - `@img/sharp-linux-x64`
   - `@img/sharp-linuxmusl-arm64`
   - `@img/sharp-linuxmusl-x64`
   - `@img/sharp-win32-arm64`
   - `@img/sharp-win32-x64`
