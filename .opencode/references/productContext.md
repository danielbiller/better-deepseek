# Product Context — Better DeepSeek

## Why it exists
DeepSeek chat lacks file export, code execution, memory, and rich visuals. BDS injects these client-side without a backend.

## Key behaviors
- Hidden system prompt injected into every chat request (editable, schedulable: always / first / every-N).
- `<BDS:...>` tags parsed from AI output and rendered as cards: visualizer, create_file, pptx/excel/docx, chart (Vega-Lite), image (Wikimedia), MIDI, code_runner, web/GitHub fetch, memory_write, character_create, LONG_WORK (zip), MCP, DeepCode auto-tools (FILE_READ/LIST_DIR/SEARCH_IN_DIRECTORY).
- Drawer UI (Svelte 5) via floating BDS button: settings, skills, memory, characters, projects, MCP servers, search providers, voice, custom CSS.
- "+" attach menu: folder upload, multi-forge repo import (GitHub/GitLab/HuggingFace/Codeberg), web fetch. Context-menu: Ask DeepSeek / Summarize.
- Voice: STT/TTS + BDS Live (Chromium only, headphones recommended).
- DeepCode (experimental): plan + hand off to DeepSeek Harness via `dsh-better-deepseek` plugin.

## UX goals
Non-intrusive: host containers next to messages, hide raw tag markdown, "Working..." indicator for LONG_WORK. Works on Firefox with File System Access fallback.
