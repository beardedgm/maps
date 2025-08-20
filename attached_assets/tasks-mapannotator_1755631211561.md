# Task List for MapAnnotator (Based on `prd-mapannotator.md`)
**Generated:** 2025-08-19  
**Tech stack:** Vanilla JS, HTML5, Bootstrap 5.3, client-side storage (localStorage, IndexedDB, SQLite via sql.js [optional])

## Relevant Files

- `index.html` - Main single-page UI; Bootstrap layout; toolbar; map container; modals/offcanvas; export dialog.
- `pages/` - *(optional)* Extra HTML if the app splits flows (not required for v1.1 SPA).
- `css/custom-styles.css` - Custom CSS for map canvas stacking, tooltip arrow, selection ring, touch target padding, dark-mode tweaks.
- `js/mapannotator.js` - App entry; initializes modules, wires global events, loads prefs.
- `js/map/canvas-view.js` - Map image loader; zoom/pan (wheel + buttons + pinch), coordinate math, transform state.
- `js/labels/label-model.js` - Label schema, defaults, validation, JSON (de)serialization.
- `js/labels/label-controller.js` - Create/edit/delete/select/drag; keyboard shortcuts; icon-size sync with font.
- `js/ui/editor-modal.js` - Modal (desktop) + Offcanvas (mobile bottom sheet) editor; live preview; validation; char counter.
- `js/ui/tooltip.js` - Tooltip controller (hover delay, tap-to-toggle, positioning, a11y paths).
- `js/export/exporter.js` - Offscreen canvas renderer; PNG export at native/scale/target width; error handling.
- `js/storage/localStorage-manager.js` - Utilities for small preferences (theme, last export choice, last size/color/icon).
- `js/storage/indexedDB-manager.js` - IndexedDB wrapper; optional caching of large images/projects; async API.
- `js/storage/sqlite-manager.js` - sql.js wrapper (optional) for relational queries/advanced search.
- `js/utils/helpers.js` - Utilities: clamp, throttle/debounce, uuid, DOM helpers, async helpers.
- `js/tests/mapannotator.test.js` - Unit tests for coordinate math, label model, exporter sizing.
- `js/tests/storage.test.js` - Unit tests for storage utils (localStorage/IndexedDB).
- `api/endpoints.js` - Placeholder for future server APIs (unused in v1.1).
- `api/endpoints.test.js` - Placeholder tests for future API layer.
- `README.md` - Project setup, testing instructions, browser support notes.
- `tasks-prd-mapannotator.md` - This task list (kept up to date as progress is made).

### Notes

- HTML uses Bootstrap grid/components. Keep custom CSS minimal (prefer utilities).
- ES6 modules via `<script type="module">`; no build step.
- **Data storage strategy:** JSON files for project persistence; localStorage for preferences; IndexedDB/sql.js optional.
- Recommended tests with **Jest**. Run with `npm test` once configured.
- Use **Pointer Events** for cross-input support; animate with `requestAnimationFrame`.
- Accessibility goal: **WCAG 2.1 AA**; ensure keyboard routes for all actions.
- Cross-browser: Chrome/Edge/Firefox/Safari (latest 2); iOS Safari 16+, Android Chrome current.

## Tasks

- [ ] 1.0 Set up HTML structure and Bootstrap layout
  - [ ] 1.1 Create `index.html` with Bootstrap 5.3 CDN, Bootstrap Icons, meta viewport, base containers
  - [ ] 1.2 Build header toolbar: Load Map (file input), Export, Save JSON, Load JSON, Clear Map, Remove Labels, Zoom In/Out/Reset, Theme toggle (optional)
  - [ ] 1.3 Add main canvas area: map container, label layer, status bar (zoom %), accessible roles/labels
  - [ ] 1.4 Add **Label Editor** markup: Bootstrap Modal (desktop) and Offcanvas bottom sheet (mobile) with fields: text, font size (8–96), color input, 12-icon selector grid, tooltip textarea + char counter, live preview, Create/Cancel
  - [ ] 1.5 Add **Export Dialog** (simple modal): native/scale factor (0.5×–4×), or target width (px) with validation and remember-last-selection note
  - [ ] 1.6 Link JS modules with `type="module"`; include `css/custom-styles.css`; verify DOM IDs/classes for scripting
  - [ ] 1.7 Add ARIA attributes and keyboard traps for modal/offcanvas; ensure focus management
  - [ ] 1.8 Manual check: basic layout across Bootstrap breakpoints (xs–xxl)

- [ ] 2.0 Implement data storage layer
  - [ ] 2.1 Create `js/storage/localStorage-manager.js` with safe get/set, namespace, try/catch, and quota handling
  - [ ] 2.2 Create `js/storage/indexedDB-manager.js` with init/open, object stores (e.g., `images`, `projects`), basic put/get/delete, and Promise-based API
  - [ ] 2.3 Create `js/storage/sqlite-manager.js` (optional) scaffolding using sql.js; expose init/db load and simple query API
  - [ ] 2.4 Storage abstraction notes in README; decide when to use which store; implement fallbacks
  - [ ] 2.5 Add unit tests `js/tests/storage.test.js` for localStorage/IndexedDB helpers

- [ ] 3.0 Implement JavaScript functionality (Map & Labels)
  - [ ] 3.1 `js/map/canvas-view.js`: load image from file input (FileReader → data URL), track natural size, center content
  - [ ] 3.2 Implement zoom controls: buttons + Alt/⌘ + wheel; clamp 0.25×–4×; display zoom %; persist in localStorage
  - [ ] 3.3 Implement pinch-zoom and two-finger pan (Pointer Events); throttle with rAF; prevent background scroll
  - [ ] 3.4 Coordinate math: maintain image-space coordinates; functions to convert between screen ↔ image space
  - [ ] 3.5 `js/labels/label-model.js`: define label schema (id, x, y, text, color, fontSize, iconId, tooltip, timestamps) + validation (font range, tooltip ≤500)
  - [ ] 3.6 `js/ui/editor-modal.js`: open on click/tap to place; live preview; enforce validation; char counter; icon-size sync to font
  - [ ] 3.7 `js/labels/label-controller.js`: on confirm, place label at image-space coords; drag to move with rAF; selection highlight
  - [ ] 3.8 Keyboard shortcuts: Escape (deselect/close), Delete (remove selected with confirm), Enter (stay in text field)
  - [ ] 3.9 Re-open editor on label click/tap for edit/delete; persist updates
  - [ ] 3.10 Save/Load JSON (project schema v1): export/import functions; robust error messages on invalid JSON
  - [ ] 3.11 Clear Map & Remove All Labels actions with confirm dialogs; ensure state resets correctly

- [ ] 4.0 Tooltip System (Hover/Delay, Tap-to-Toggle, Positioning, A11y)
  - [ ] 4.1 `js/ui/tooltip.js`: hover-delay controller (1000±100 ms), hide-on-mouseout (~150 ms), timers and state
  - [ ] 4.2 Touch behavior: tap-to-toggle tooltip; tap outside to dismiss; ensure it doesn’t block drag
  - [ ] 4.3 Tooltip rendering: width 280–360px, wrapping, arrow, z-index; avoid viewport clipping with smart placement
  - [ ] 4.4 Accessibility: `aria-describedby`, keyboard route to show details (non-hover fallback), Escape to dismiss
  - [ ] 4.5 Performance: throttle/debounce pointer events; avoid layout thrash

- [ ] 5.0 Export & Persistence (PNG Scaling, JSON Save/Load, Storage Managers)
  - [ ] 5.1 `js/export/exporter.js`: offscreen canvas renderer; draw base image then icons (SVG) and text at image-space positions
  - [ ] 5.2 Implement export options: native 1×, scale factor (0.5×–4×), target width (auto height), remember last choice (session/localStorage)
  - [ ] 5.3 Ensure crisp rendering: re-render vectors/text at output size; verify edges are sharp at 2×
  - [ ] 5.4 Memory/error handling: try/catch around large canvases; friendly error UI suggesting smaller sizes
  - [ ] 5.5 Integrate Save/Load JSON with storage managers where useful (e.g., caching images); keep offline-first

- [ ] 6.0 Mobile/Touch & Accessibility
  - [ ] 6.1 Bottom-sheet editor on small screens (Bootstrap Offcanvas bottom); disable background scroll; trap focus
  - [ ] 6.2 Touch targets ≥ 40×40px; ensure visible focus/active states; larger buttons on mobile
  - [ ] 6.3 Screen reader: label form inputs, icon selector radios, tooltip linkage; high-contrast option
  - [ ] 6.4 Reduced motion: respect `prefers-reduced-motion` for transitions/animations
  - [ ] 6.5 Dark mode: respect `prefers-color-scheme`; ensure label colors remain legible

- [ ] 7.0 Testing, QA & Performance
  - [ ] 7.1 Set up Jest config (if used); scaffold tests in `js/tests/*.test.js`
  - [ ] 7.2 Write tests: coordinate conversions, label validation/serialization, exporter dimension calculations, storage utils
  - [ ] 7.3 Manual test checklist (README): cross-browser matrix, responsive layouts, pointer gestures, keyboard routes
  - [ ] 7.4 Performance passes: verify 60fps during drag on reference images; memory profiles during large export
  - [ ] 7.5 Accessibility review: keyboard-only run, screen reader spot-check, color contrast checks
  - [ ] 7.6 Final cleanup: remove console.logs, comment cruft; validate HTML; check browser console for errors

