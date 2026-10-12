# Proposal

## Why

Web search (Bing, DuckDuckGo Lite/HTML) always fails on Brave for Windows while working on Chrome. The reported failure is a CORS preflight rejection (`No 'Access-Control-Allow-Origin' header`) against `https://www.bing.com/search`, and the current code gives the user no actionable failure detail — the cause is swallowed into a generic `Search failed` string.

## What Changes

- Strip the search provider fetch options to a CORS-safelisted simple request: remove the `Cache-Control` and `Pragma` request headers from `SEARCH_FETCH_OPTIONS`, keeping `Accept` and `Accept-Language`.
- Keep the existing freshness guarantee via the `cache: "no-store"` fetch mode (no behavior loss; `Pragma` is HTTP/1.0 legacy with no effect over `fetch`).
- No changes to provider order, fallback chain, ranking, timeout budget (15s), response parsing, or UI cards.
- Surface the per-provider HTTP status in the persisted search-failure attachment so future provider failures are diagnosable without a background-console probe.
- Add a regression test locking the safelisted-header contract.

## Capabilities

### New Capabilities

- `web-search`: requirements for web-search provider fetching — which request shape providers must be fetched with (CORS-simple), the provider fallback chain and timeout budget, and how failures are reported.

### Modified Capabilities

(none — no existing specs in this project)

## Impact

- Affected code: `src/content/files/search-reader.js` (`SEARCH_FETCH_OPTIONS`), `src/content/auto.js` (failure-attachment text), `src/content/files/search-reader.test.js` (regression assertions).
- No API, dependency, manifest-permission, or UI-layout changes. Chrome behavior unchanged (it already bypassed CORS); Brave search starts working. Reduced staleness risk: none — `cache: "no-store"` already prevents cached responses.
