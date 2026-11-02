# Generated mobile projects

- **`android/`** — Created by `pnpm exec tauri android init`. Safe to commit; Gradle `build/` output is gitignored inside that tree.
- **`apple/`** — Created on **macOS only** by `pnpm exec tauri ios init`. The Tauri CLI does not expose `tauri ios` on Linux or Windows. After init on a Mac, commit `apple/` so teammates can run `pnpm run tauri:ios:dev` / `tauri:ios:build` without re-scaffolding.

See [docs/mobile.md](/docs/mobile.md).
