# TypeWords Desktop (Tauri 2)

This fork packages the static Nuxt site as a native desktop app for **Windows**, **macOS**, and **Linux** using [Tauri 2](https://v2.tauri.app/). Mobile targets are not supported here.

The UI is the same as the web app: `pnpm run generate` writes assets to `.output/public`, and Tauri loads that folder at runtime (local `asset://` protocol). Practice data stays in **IndexedDB / localStorage** on the device; optional Supabase sync still works when configured.

## Layout

| Path | Role |
|------|------|
| `src-tauri/` | Rust shell, `tauri.conf.json`, icons |
| `.output/public/` | Frontend bundle (created by `pnpm run generate`, not committed) |
| `src-tauri/tauri.conf.json` → `build.frontendDist` | `../.output/public` |

**App identifier:** `cc.typewords.desktop`  
**Product name:** TypeWords

## Prerequisites

### All platforms

- [Node.js](https://nodejs.org/) (LTS) and **pnpm**
- [Rust](https://rustup.rs/) (stable toolchain; Tauri 2 needs a recent stable release)
- Platform-specific dependencies below

From the repository root:

```bash
pnpm install
```

### Linux (Debian / Ubuntu)

```bash
sudo apt update
sudo apt install -y \
  libwebkit2gtk-4.1-dev \
  libgtk-3-dev \
  libayatana-appindicator3-dev \
  librsvg2-dev \
  libssl-dev \
  build-essential
```

Other distros: see [Tauri — Linux prerequisites](https://v2.tauri.app/start/prerequisites/#linux).

### macOS

- Xcode Command Line Tools: `xcode-select --install`
- See [Tauri — macOS prerequisites](https://v2.tauri.app/start/prerequisites/#macos)

### Windows

- [Microsoft C++ Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/) (Desktop development with C++)
- [WebView2](https://developer.microsoft.com/en-us/microsoft-edge/webview2/) (usually preinstalled on Windows 11)
- See [Tauri — Windows prerequisites](https://v2.tauri.app/start/prerequisites/#windows)

## Commands

Run from the **repository root**:

| Script | Description |
|--------|-------------|
| `pnpm run tauri:dev` | Runs `pnpm run dev` (Nuxt on port **5567**) and opens the Tauri window against the dev server |
| `pnpm run tauri:build` | Runs `pnpm run generate`, then builds release installers/binaries |
| `pnpm run generate` | Static site only → `.output/public` |
| `pnpm exec tauri build --bundles deb` | Linux `.deb` only (example) |

### Development

```bash
pnpm run tauri:dev
```

### Production build

```bash
pnpm run tauri:build
```

Artifacts are under `src-tauri/target/release/bundle/` (`.deb` / `.AppImage` on Linux, `.msi` / `.exe` on Windows, `.dmg` / `.app` on macOS depending on OS and Tauri bundle settings).

## Offline behavior

- The desktop app serves prebuilt files from `.output/public` (dictionaries, audio, images, JS/CSS).
- No server is required for core word/article practice after build.
- External links (GitHub, translation APIs, Supabase, etc.) still need network access when those features are used.

## Verified on Linux

On Ubuntu 24.04 (WebKitGTK 4.1, GTK 3, Rust stable 1.99):

```bash
pnpm install
pnpm exec tauri build --bundles deb
```

That command runs `pnpm run generate`, compiles the release binary, and writes:

- `src-tauri/target/release/typewords-desktop` (ELF; frontend assets embedded)
- `src-tauri/target/release/bundle/deb/TypeWords_0.1.0_amd64.deb`

`pnpm run tauri:build` uses `bundle.targets: "all"`, which also tries AppImage and rpm on Linux. AppImage needs a working `fuse` userspace; this environment could not install `fuse3`, so only the `.deb` target was built here.

**Windows and macOS were not built in this environment.** Use the same `pnpm run tauri:build` on those OSes (with the prerequisites above) to produce `.msi` / NSIS and `.dmg` / `.app`.

## Troubleshooting

- **`frontendDist` missing or empty:** run `pnpm run generate` before `tauri build`, or use `pnpm run tauri:build` which runs generate automatically.
- **Linux `pkg-config` / `webkit2gtk` errors:** install the apt packages listed above.
- **Rust edition / version errors:** `rustup update stable` and ensure `rustc --version` is a current stable release.
