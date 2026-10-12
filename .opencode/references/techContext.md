# Tech Context — Better DeepSeek

## Stack
Node ≥18, npm, Vite 6 + `@sveltejs/vite-plugin-svelte`, Svelte 5, Vitest 3 + jsdom + v8 coverage, Playwright 1.54 (Chrome-target bundled Chromium), Selenium WebDriver + GeckoDriver (Firefox BiDi), Gradle 8.7 + JDK 17 + Android SDK API 34 (Android shell).

## Deps (runtime)
`@mozilla/readability`, `turndown`, `marked`, `fflate`, `docx`, `pptxgenjs`, `xlsx`, `vega`/`vega-lite`/`vega-embed`, `html2canvas`, `youtube-transcript`, `ignore`.

## Commands
- `npm run dev` (watch), `npm run build` (chrome+firefox), `build:chrome|firefox|android|source`, `sanitize`, `check-locales`.
- `npm run test:unit` (vitest+coverage), `test:e2e` (playwright), `test:e2e:firefox` (vitest firefox config, `FIREFOX_BIN` must be absolute), `test:e2e:android`, `android:test`, `android:assemble:debug`, `test:ci:web|android|ci`.
- `npm test` = pretest build + unit + chrome + firefox lanes.

## Constraints
- Extension automation requires Playwright bundled Chromium (store Chrome dropped sideload flags); Firefox needs absolute `FIREFOX_BIN`, unsigned zip via `installAddon(..., true)`.
- E2E HTTP(S) fully intercepted via `tests/e2e/fixtures/fixture-resolver.js`; unmatched externals → terminal 500 + fail.
- Sandbox chunks >500kB warning is known (sandbox.js ~1.2MB). `esbuild.charset: ascii` + post-build sanitization required.
- Svelte 5 tests mount via `tests/helpers/svelte.js`; chrome APIs via `tests/mocks/chrome.js` (extend, don't fork).
