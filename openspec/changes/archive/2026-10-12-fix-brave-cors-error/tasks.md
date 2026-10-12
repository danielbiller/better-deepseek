# Tasks

## 1. CORS-simple search fetch

- [x] 1.1 Remove `Cache-Control` and `Pragma` from `SEARCH_FETCH_OPTIONS` in `src/content/files/search-reader.js`, keeping `Accept`, `Accept-Language`, `cache: "no-store"`, `credentials: "omit"`, `redirect: "follow"`, `timeoutMs`; verify by reading the resulting options object
- [x] 1.2 Thread the background HTTP status into the per-provider failure strings in `searchWeb` (keeping the `Search failed: ` prefix), and verify the `search_error_*.txt` attachment text includes provider + status for a mocked failing provider
- [x] 1.3 Grep for existing consumers that string-match the failure text (`Search failed`, `search_error_`) and confirm none break; verify with grep output

## 2. Regression tests

- [x] 2.1 Extend the safe-fetch assertion in `src/content/files/search-reader.test.js` to deep-equal the full `headers` object (`Accept` + `Accept-Language` only) and assert absence of `Cache-Control`/`Pragma` with a Brave-preflight comment; verify with `npx vitest run src/content/files/search-reader.test.js`
- [x] 2.2 Add a search-failure case asserting the status-enriched message for an all-providers-fail run; verify with `npx vitest run src/content/files/search-reader.test.js`
- [x] 2.3 Run the adjacent suites (`tests/integration/background-fetch-timeout.test.js`, `tests/integration/auto.test.js`) and verify no regressions

## 3. Build and browser verification

- [x] 3.1 Run `npm run build:chrome` and verify `dist-chrome/` + zip build cleanly
- [x] 3.2 Manual Brave/Windows check: load `dist-chrome/` unpacked in Brave with Shields UP, run `/search git branch repair` with default, Bing-only, and DDG-only provider orders, and verify results render with no CORS error in the background worker console
- [x] 3.3 Manual Chrome sanity check: repeat one `/search` run in Chrome and verify behavior is unchanged
