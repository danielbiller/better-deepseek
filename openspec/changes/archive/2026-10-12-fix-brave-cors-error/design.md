# Design

## Context

See proposal.md (Why) for motivation. Current state:

- `src/content/files/search-reader.js` defines `SEARCH_FETCH_OPTIONS` with four request headers (`Accept`, `Accept-Language`, `Cache-Control: no-cache, no-store`, `Pragma: no-cache`) plus `cache: "no-store"`, `credentials: "omit"`, `redirect: "follow"`, `timeoutMs: 15000`. It is the only `bds-fetch-url` caller sending non-safelisted headers.
- `src/background/index.js` `fetchPageContent` forwards `headers` verbatim into the service-worker `fetch()`, so the header set is fully determined by the caller.
- Per the Fetch standard, `Cache-Control` and `Pragma` are not CORS-safelisted: their presence forces an `OPTIONS` preflight. Bing and the DDG hosts do not answer preflights with `Access-Control-Allow-Origin`, so any browser that enforces preflight on extension fetches (Brave) fails every provider request. Chrome exempts MV3 service workers with `<all_urls>` host permission, masking the bug.
- Existing regression coverage (`search-reader.test.js`) asserts the safe `GET/no-store/omit/follow/timeoutMs` shape and absence of `User-Agent`, but does not pin the header set.

## Goals / Non-Goals

**Goals:**

- Make every search-provider fetch a CORS simple request (GET, safelisted headers only) so it succeeds with or without extension CORS exemption.
- Preserve freshness (no cached provider pages) and the 15s per-provider budget.
- Make future provider failures diagnosable from the persisted failure record (provider + HTTP status), without requiring a background-console probe.

**Non-Goals:**

- No change to provider order, fallback logic, ranking, parsing, retry policy, or manifest permissions.
- No Brave-specific UA sniffing or per-browser branches.
- No proxy, `no-cors` mode, or declarativeNetRequest workarounds.

## Decisions

### 1. Delete `Cache-Control` / `Pragma` headers; rely on `cache: "no-store"`

`cache: "no-store"` is a fetch mode, not a header: it bypasses the HTTP cache in both directions without triggering preflight, which is exactly what the removed headers were trying to achieve. `Pragma: no-cache` is an HTTP/1.0 legacy directive with no effect over `fetch`. Alternatives considered:

- Keep headers and add a headerless retry on preflight failure: rejected — doubles requests on every Brave search, adds state, and still pays a failing preflight per provider.
- Route search through a CORS-friendly proxy: rejected — new infrastructure/dependency for a two-line client fix, plus privacy cost (queries leave the user's browser via a third party).

### 2. Keep `Accept` and `Accept-Language` as the only headers

Both are CORS-safelisted with the values in use (ASCII, short, matching safelisted ABNF), so the request stays preflight-free. Dropping them instead (bare GET) was considered but rejected: `Accept: text/html…` documents intent and `Accept-Language` keeps provider language behavior unchanged.

### 3. Include HTTP status in the failure record (minimal string enrichment)

`fetchPageContent` already returns `{ok, error, status}`; the search loop currently keeps only the message text in `errors[]`. Threading the status into the joined failure string (and thus the `search_error_*.txt` attachment and `/search` toast) makes the next provider outage triageable. Alternative (structured error objects through the chain) rejected as over-engineering for a diagnostic nicety.

### 4. Regression test pins the exact header set

Extend the existing safe-fetch assertion in `search-reader.test.js` to deep-equal the full `headers` object and assert absence of `Cache-Control`/`Pragma`. jsdom cannot enforce real CORS, so the static header contract is the hermetic regression. No new E2E lane (Playwright's bundled Chromium cannot emulate Brave Shields; manual Brave verification covers it).

## Risks / Trade-offs

- [Risk] A provider starts requiring a non-safelisted header (e.g. auth) → preflight returns. Mitigation: none needed now; the pinned test will flag any header addition for explicit CORS review.
- [Risk] Removing `Cache-Control` changes caching on some intermediary → Mitigation: `cache: "no-store"` already forbids cache reads and writes at the fetch layer; verified equivalent.
- [Risk] Brave Shields blocks provider fetches for non-CORS reasons (adblock list match on `bing.com/search`) → Mitigation: manual Shields-UP verification step in tasks; if it persists, the now-visible status/error text will identify it and it becomes a separate allowlist/documentation change.
- [Risk] Failure-text change breaks a consumer that string-matches `Search failed:` → Mitigation: keep the `Search failed: ` prefix intact; only append status detail. Grep for the prefix during implementation.

## Migration Plan

Ship in the normal extension release (no migration, no rollback beyond reverting the single hunk). Verification: `test:unit` (search-reader + background-fetch-timeout suites), `npm run build:chrome`, manual Brave/Windows check with Shields UP per tasks.md.

## Open Questions

None — the only unknown (whether Shields blocks beyond CORS) is answered by the manual verification step and does not change specs, approach, or tasks either way.
