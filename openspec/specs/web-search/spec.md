# web-search Specification

## Purpose
Web search lets the assistant fetch fresh results from external search providers (Bing, DuckDuckGo) and inject them into the conversation, working identically across all supported Chromium browsers including Brave.

## Requirements

### Requirement: Search provider requests avoid CORS preflight

Search provider fetches SHALL use only CORS-safelisted request headers so no preflight `OPTIONS` request is issued against providers that do not answer preflights.

#### Scenario: Bing fetch issues a simple request

- **WHEN** the system fetches results from the Bing provider
- **THEN** the request carries no `Cache-Control` header and no `Pragma` header

#### Scenario: DuckDuckGo fetches issue simple requests

- **WHEN** the system fetches results from either DuckDuckGo provider
- **THEN** the request carries no `Cache-Control` header and no `Pragma` header

#### Scenario: Search works where preflights are enforced

- **WHEN** a browser enforces CORS preflight checks on extension fetches (e.g. Brave)
- **THEN** provider searches complete the same way as on browsers that exempt extensions

### Requirement: Search results stay fresh without cache headers

The system SHALL fetch provider pages with caching disabled at the fetch-mode level so results are never served from cache.

#### Scenario: Fresh provider response

- **WHEN** the system fetches any search provider page
- **THEN** the fetch bypasses the HTTP cache and does not store the response

### Requirement: Provider fallback chain is preserved

The system SHALL try each configured search provider in order and move to the next one when a provider fails, times out, returns a bot challenge, or yields no qualifying results.

#### Scenario: Failed provider falls through to next

- **WHEN** the first configured provider fails or returns no qualifying results
- **THEN** the system attempts the next configured provider before reporting failure

#### Scenario: Per-provider timeout budget

- **WHEN** a provider does not respond within the timeout budget
- **THEN** the system abandons that provider and continues with the next one

### Requirement: Search failures are diagnosable

When all providers fail, the system SHALL report which providers failed and why, including the HTTP status where one was received, in the persisted failure record shown to the user.

#### Scenario: Failure names providers and statuses

- **WHEN** every configured provider fails
- **THEN** the failure record lists each attempted provider with its error and any HTTP status received

#### Scenario: Empty results stay distinct from errors

- **WHEN** providers respond but yield no results for the query
- **THEN** the system reports no-results-found rather than a request failure
