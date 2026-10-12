# System Patterns — Better DeepSeek

## Architecture
- **Content script** (`src/content/`): Svelte 5 drawer/overlays, DOM utils (`dom/`), BDS tag parser (`parser/`), tool renderers (`tools/`), file readers (`files/`). Built as IIFE `content.js`, `treeshake: false` to preserve side effects.
- **Injected script** (`src/injected/`): MAIN-world `fetch`/`XHR` patch, injects system prompt + memory/skills into chat completions.
- **Background** (`src/background/`): service worker for cross-origin fetch (search, web/GitHub fetch, MCP, status API).
- **Sandbox** (`src/sandbox/` + `static/sandbox.html`): isolated iframe for pptxgenjs/SheetJS/docx generation.
- **Platform layer** (`src/platform/`): `bds-platform-globals` alias swaps `globals-chrome.js` ↔ `globals-android.js` (chrome.* polyfill → native bridge). `android-bridge-shim.js` mocks `window.AndroidBridge` in tests.
- **Shared lib** (`src/lib/`): zip, download, hashing, version (`__BDS_VERSION__` from package.json).

## Key decisions
- Vite multi-entry build (`build.js`), 3 bundles + static copy + ASCII sanitization + zip. Targets: chrome / firefox / android (`dist-<target>/`).
- Data flow: injected prompt → AI `<BDS>` tags → parser → card render at original message position; auto-tags round-trip via background.
- Storage: `chrome.storage.local` only; Firefox lane asserts storage quiescence (#108); host-ownership contract: card inside `.bds-host-wrapper` with nonzero size.
- Perf contract (#105): 200/2000-msg baselines, <2000ms each, median ratio ≤2.5×.
