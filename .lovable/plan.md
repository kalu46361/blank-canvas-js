
# Complete Frontend Redesign — C++ Mastery (Electron)

Note: this is an Electron desktop app (`renderer/index.html`, `renderer/css/styles.css`, `renderer/js/app.js`). The recent Lovable web build errors (`build:dev`) don't apply here — the app runs via `npm start` (electron). No web framework changes.

## Design Direction

- **Palette (Midnight Indigo):** bg `#0a0a1a`, surface `#141432`, elevated `#1e1e5a`, accent/primary `#4f46e5`, with softer indigo glows for depth.
- **Typography:** SF Pro Display (headings), SF Pro Text (body), SF Mono (code). Fallback stack: `-apple-system, BlinkMacSystemFont, "Segoe UI", Inter` for non-macOS. Monaco keeps SF Mono / Menlo / Consolas.
- **Layout:** Persistent left sidebar + main content, with a compact custom titlebar retained.
- **Feel:** Professional, modern, calm; subtle depth via layered surfaces, hairline borders (`rgba(255,255,255,0.06)`), soft indigo shadows, 200–250ms cubic-bezier transitions. No neon, no heavy gradients.

## Scope

Full visual redesign of the renderer + JS restructure into ES modules, while preserving every DOM id, event binding, and IPC contract that `verify_dom_bindings.js`, `tests/*`, `preload.js`, and `main.js` depend on.

## Files Changed

1. **`renderer/index.html`** — rewritten markup with new design system classes, semantic sections, refined titlebar, sidebar, dashboard, lesson view, editor panel, terminal panel, modal shells. All existing element `id`s kept (`btn-example-code`, `terminal-panel`, `terminal-transcript`, `terminal-input`, `search-input`, `user-level`, editor mount nodes, tab buttons with `data-tab`, etc.).
2. **`renderer/css/styles.css`** — replaced with a token-based system:
   - CSS variables for color, spacing, radius, shadow, typography scale
   - Sidebar (fixed 260px, collapsible to 64px icon-rail with a persistent toggle)
   - Cards, buttons, badges, progress bars, tabs, code blocks, terminal, modals
   - Focus rings, hover states, scrollbar theming
   - Reduced-motion + high-contrast fallbacks
3. **`renderer/js/app.js`** — restructured into logical sections (still one file to avoid changing script tag / CSP): state, dom refs, navigation, curriculum, editor, execution, terminal, checker glue, event wiring. Public function names (`loadExampleCode`, `clearOutput`, `resetTerminal`) and all `getElementById` lookups preserved so `verify_dom_bindings.js` passes.
4. **`renderer/js/bootstrap.js`** — light cleanup to match new module boundaries; no behavior change.
5. No changes to `main.js`, `preload.js`, `lib/execution-manager.js`, curriculum data, or tests.

## Invariants (must not break)

- `id="btn-example-code"` present and wired to `loadExampleCode` via `addEventListener('click', loadExampleCode)`.
- Terminal ids `terminal-panel`, `terminal-transcript`, `terminal-input` and `data-tab="terminal"` remain.
- `clearOutput()` still calls `resetTerminal()`.
- Every `getElementById(...)` in `app.js` corresponds to an id in `index.html` (except allowed `fallback-editor`).
- CSP untouched; Google Fonts + Monaco worker still allowed.
- `npm run verify` (syntax check + tests + DOM bindings + curriculum validate) still passes.

## Sidebar Behavior

- Expanded (260px) shows profile, search, nav labels, progress; collapsed (64px) shows icons + tooltips.
- Toggle button in titlebar area; state persisted in `localStorage`.
- Active nav item highlighted with indigo accent bar + subtle surface fill.

## Verification

- `npm run check:syntax`
- `node verify_dom_bindings.js`
- `npm test`
- Manual smoke via `npm start`: dashboard renders, lesson loads, example code button works, editor mounts, run/clear/terminal functional, tab switching, modals.

## Out of Scope

- Curriculum content, execution manager logic, Electron main process, tests, packaging.
