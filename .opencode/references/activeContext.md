# Active Context — Better DeepSeek

## Current focus (2026-10-12)
Workspace initialized from clean checkout: `npm install` + `npm run build:chrome` verified working (dist-chrome + zip). Memory bank created (was missing).

## Recent changes (from git log / changelog)
- v0.1.15: BDS Live voice mode, context-menu actions, MIDI generation, first-run voice prompt, multi-forge import, MCP as search provider.
- Harden composer control detection across locales; composer visibility overrides; Web Audio MIDI synth engine; parser out-of-order code-range fix.

## Next steps
- TBD by user. Default verification before PR: `npm run test:unit`, `npm run test:e2e`, `npm run build`.

## Decisions / patterns
- Keep fixes minimal, co-located tests for pure functions (`*.test.js` next to source), integration under `tests/integration/`.
- Never commit secrets (GitHub PAT stays in local storage). `android/local.properties` is gitignored.
- Ponytail full: stdlib/native first, shortest diff, no speculative abstractions.
