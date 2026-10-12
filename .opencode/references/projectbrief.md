# Project Brief — Better DeepSeek

Browser extension (Chrome / Edge / Firefox + Android WebView shell) that enhances `chat.deepseek.com` with tool tags, persistent memory, skills/characters/projects, voice, file generation, MCP, DeepCode, and DeepResearch.

- Source of truth: `package.json` version (currently 0.1.15). `static/manifest.json` and `android/app/build.gradle.kts` carry copies.
- Core goal: local-first, non-intrusive DOM augmentation. All user data in `chrome.storage.local`. No telemetry.
- Scope: `src/` extension code, `android/` shell, `static/` manifest+sandbox, `scripts/` + `build.js` toolchain, `tests/` suites. Out of scope: DeepSeek backend itself, store publishing.
- Entry points: `src/content/index.js` (content script), `src/background/` (service worker), `src/injected/` (MAIN-world fetch/XHR patch), `src/sandbox/` (PPTX/Excel/DOCX gen).
