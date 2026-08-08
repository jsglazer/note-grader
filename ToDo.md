# note-grader — To-Do

## Scaffolding
- [x] `package.json` — npm init with Obsidian + Anthropic SDK deps
- [x] `tsconfig.json` — TypeScript config
- [x] `esbuild.config.mjs` — build script
- [x] `manifest.json` — Obsidian plugin manifest (id, name, version 1.0.0)
- [x] `.gitignore` — node_modules, dist, main.js

## Settings (`src/settings.ts`)
- [x] Define `NoteGraderSettings` interface (all 10 fields)
- [x] Implement `NoteGraderSettingTab` with:
  - [x] Anthropic API key (password field)
  - [x] Claude model dropdown
  - [x] Grade threshold slider (0–100, default 75)
  - [x] Grading mode dropdown (Note-only / PDF-assisted)
  - [x] PDF extraction script path
  - [x] Use venv toggle
  - [x] venv path field (disabled when Use venv is off)
  - [x] Use OCR toggle (default on)
  - [x] Zotero key field (default `$itemKey`)
  - [x] Claude-note format textarea

## Claude Service (`src/claude-service.ts`)
- [x] `gradeNote(noteText, pdfText?)` → `{ grade, feedback, scores }`
- [x] `generateClaudeNotes(noteText, pdfText?)` → structured analysis (Summary + all sections)
- [x] `generateCorrections(noteText, pdfText?)` → per-section missed points and errors
- [x] Grading prompt with 4×25-pt criteria (main idea, major points, accuracy, depth)
- [x] JSON response parsing + validation
- [x] Error handling (API key missing, network failure, bad JSON)

## Note Parser (`src/note-parser.ts`)
- [x] Parse active note frontmatter (`$itemKey`, `$libraryID`)
- [x] Detect and extract required sections: Core Claims, Methodology, Counter Arguments, General Notes, References
- [x] Return error if any section is missing (with section name in message)

## Zotero Service (`src/zotero-service.ts`)
- [x] `openInZotero(itemKey)` — fires `zotero://select/library/items/<key>`
- [x] `getPDFPath(itemKey, libraryID)` — BBT JSON-RPC `item.attachments` → returns local PDF file path
- [x] Connection check (BBT running at `http://127.0.0.1:23119/better-bibtex/cayw?probe=true`)

## PDF Extractor (`src/pdf-extractor.ts`)
- [x] `extractText(pdfPath, settings)` — spawns `python3` (or venv python) with script path + pdf path
- [x] Parse stdout for `Markdown saved → <path>` line
- [x] Read output `.md` file, strip image markdown links
- [x] Delete temp `.md` file
- [x] Pass `--no-ocr` flag when OCR toggle is off

## Modals
### Grade Modal (`src/grade-modal.ts`)
- [x] Display total grade (large)
- [x] Display per-criterion sub-scores (main idea, major points, accuracy, depth)
- [x] Display Claude feedback text
- [x] Four buttons: Stop / Retry / Claude-note / Claude-note+corrections
- [x] Retry → call `openInZotero()`
- [x] Claude-note → call `generateClaudeNotes()` → `noteAppender.appendClaudeNotes()`
- [x] Claude-note+corrections → call `generateCorrections()` → `noteAppender.appendCorrections()`

### Fail Modal (`src/fail-modal.ts`)
- [x] Display total score (e.g. "Score: 62/100")
- [x] Display "Do better!" heading
- [x] Display Claude feedback text
- [x] OK button → call `openInZotero()`

## Note Appender (`src/note-appender.ts`)
- [x] `appendClaudeNotes(file, analysis)` — appends `# Claude Notes` section (Summary + all sections)
- [x] `appendCorrections(file, corrections)` — appends `# Claude Review` section (per-section missed/errors)
- [x] Format: `**Missed:**` / `**Error:**` prefixes; `*(none)*` when clean

## Main Entry Point (`src/main.ts`)
- [x] Register ribbon icon
- [x] Register command: "Review note with Claude"
- [x] On trigger: validate settings → parse note → (fetch PDF if mode=PDF-assisted) → call Claude → show modal
- [x] Loading notice while Claude is working

## Deploy
- [x] `cp main.js / manifest.json / styles.css` → `VaultDEV/.obsidian/plugins/note-grader/`
- [ ] Test end-to-end with a real Zotero-synced note

## v1.1.0 (future)
- [ ] PDF-assisted mode full integration
- [ ] OCR toggle wired through to subprocess call

## v1.2.0 (future)
- [ ] External analysis mode (user pastes pre-computed summary)
