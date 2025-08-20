# Product Requirements Document (PRD): MapAnnotator

**Version:** 1.1  
**Date:** 2025-08-19  
**Owner:** TBD

---

## 1. Introduction / Overview

**MapAnnotator** is a lightweight, client-side web application for adding labeled annotations to static map images (JPG/PNG). It runs entirely in the browser using **HTML5**, **CSS (Bootstrap 5)**, and **vanilla JavaScript**—no build step or backend.

This version (v1.1) upgrades the core labeling workflow with richer metadata and platform ergonomics:
- A **placement modal/bottom sheet** appears when the user click/taps the map to create a label, allowing selection of an **icon (12 Bootstrap Icons)**, label **text**, **font size**, **color**, and **tooltip notes** (up to **500 characters**).
- **Tooltips** appear after a **~1000 ms hover delay** on desktop; on touch, tapping a label reveals a popover-style tooltip.
- **Icons scale with font size** so the icon remains visually proportional to the label text.
- **Mobile/touch** support (pinch-zoom, two-finger pan, tap behaviors) and an **export scaling** option (native 1×, scale factor, or target width) ensure high-quality outputs.

**Problem solved:** Users can attach contextual notes, icons, and styling to map labels while keeping export simple and offline-first.

**Primary users:** Broad audience needing flexible, offline map annotation (RPG GMs/players, educators, hobbyist cartographers).

---

## 2. Goals

1. Allow users to place labels via **click/tap** and immediately configure them in a **modal or bottom sheet** (icon, text, font size, color, tooltip notes).
2. Show tooltip notes on **hover after ~1000 ms** on desktop; on touch, provide **tap-to-toggle** tooltips; hide on mouse out or tap outside.
3. Ensure **font size** changes proportionally scale the **icon** (icon sits to the left of the label text).
4. Maintain existing features: drag labels, edit/delete labels, zoom controls, keyboard shortcuts, export to PNG, save/load JSON, clear/reset.
5. Preserve all label metadata (icon, text, font size, color, tooltip) in JSON save/load.
6. Keep the experience **fully client-side**, performant, and accessible across modern browsers.
7. Provide **mobile/touch ergonomics** (gesture zoom/pan, larger touch targets, bottom sheet editor) with parity to desktop.
8. Support **export scaling** options (native, scale factor, target pixel width) with crisp text/icon rendering.

---

## 3. User Stories

- As a user, I can load a JPG/PNG map image and zoom/pan comfortably.  
- As a user, I can click/tap the map to create a label and configure it via a modal/bottom sheet (icon, text, font size, color, tooltip).  
- As a user, I can hover (desktop) or tap (touch) to **view tooltip notes** up to 500 characters.  
- As a user, I can drag labels to reposition them and tap/click a label to **edit** or **delete** it.  
- As a user, I can **export** the annotated map as a PNG at 1× or a chosen scale/width (tooltips excluded).  
- As a user, I can **save** my project (map + labels + zoom) to **JSON** and **load** it later with no data loss.  
- As a user, I can use **keyboard shortcuts** to speed up common actions (Escape, Delete, Enter; Alt/⌘ + wheel for zoom).

---

## 4. Functional Requirements

### 4.1 Map Loading
1. The system must allow the user to load a local JPG/PNG file.  
2. The system must display the image as the map background, preserving aspect ratio.  
3. The system must support zoom in/out/reset and persist zoom level in project state.

**Acceptance Criteria**  
- Loading a valid JPG/PNG displays without distortion.  
- Zoom in/out (buttons + Alt/⌘ + wheel) works; reset returns to 100% (1.0).  
- Zoom range: 0.25×–4× (step ~0.1). Zoom level persists in JSON.

### 4.2 Label Creation (Modal / Bottom Sheet)
4. On map click/tap, open a **modal** (desktop) or **bottom sheet** (mobile) to configure the label **before finalizing placement**.  
5. Fields must include: **Label Text** (single-line), **Font Size** (8–96px; default 16px), **Label Color** (color input; default #222), **Icon Selector** (fixed set of **12 Bootstrap 5 Icons**), **Tooltip Notes** (textarea; max **500 chars**; counter shown).  
6. A **live preview** (icon + text) must update as the user changes settings.  
7. **Icon scaling:** icon size must track the selected font size.  
8. Provide **Create** and **Cancel** actions. Cancel discards the label.  
9. Validation: enforce tooltip length; clamp font size; allow empty text with warning.

**Acceptance Criteria**  
- Map click/tap opens editor focused on Label Text.  
- Selecting an icon updates preview immediately.  
- Increasing font size proportionally increases icon size.  
- Submitting with >500 chars in tooltip is blocked with error/counter.  
- Cancel closes editor without creating a label.

### 4.3 Label Placement & Editing
10. On confirm, place the label at the clicked/tapped coordinates (adjusted for current zoom).  
11. Labels must be **draggable** to reposition; positions must persist.  
12. Selecting a label must re-open the editor with existing values for **edit** or **delete**.  
13. Visual selection state must be apparent (focus ring/highlight).

**Acceptance Criteria**  
- Drag/drop is smooth on desktop/mobile up to ~8MP images and ≤200 labels.  
- Edits are immediately reflected; delete removes label + tooltip.

### 4.4 Tooltip Behavior
14. Desktop: show tooltip after **1000 ±100 ms** hover; hide on mouseout within ~150 ms.  
15. Touch: tapping a label reveals tooltip; tapping outside hides it.  
16. Tooltip width constrained to ~280–360px; text wraps; z-index above map.  
17. Positioning avoids viewport clipping when possible.

**Acceptance Criteria**  
- Tooltips respect max length (input capped at 500 chars).  
- Tooltip appearance/dismissal timing matches spec on supported browsers.

### 4.5 Keyboard Shortcuts
18. **Escape** deselects active label or closes editor if open.  
19. **Delete** removes selected label (with confirm).  
20. **Enter** keeps focus in the label input within the editor.  
21. **Alt/⌘ + mouse wheel** zooms in/out; buttons provide zoom/reset.

**Acceptance Criteria**  
- Shortcuts work in Chrome, Firefox, Safari, Edge.  
- Zoom shortcut does not scroll the page unintentionally.

### 4.6 Export & Persistence
22. Export to **PNG** must include map, icons, and label text as rendered (no tooltips).  
23. Save Project to **JSON** must include: map reference/data, labels (text, color, fontSize, iconId, tooltip), positions, zoom level.  
24. Load Project from JSON must restore rendering, tooltip behavior, and zoom state.  
25. Clear Map removes the map and labels; Remove All Labels clears labels only (with confirm).

**Acceptance Criteria**  
- Export at native resolution produces correct placement.  
- JSON round-trip preserves all fields.  
- Clear actions ask for confirmation.

### 4.7 Mobile / Touch Behavior
26. **Touch input** is first-class: single tap to place (opens bottom sheet), tap to edit, drag to move.  
27. **Pinch to zoom** (centered on pinch midpoint) and **two-finger pan**; on-screen zoom buttons remain available.  
28. **Tap-to-toggle tooltip** on touch; tap outside to dismiss.  
29. **Hit targets ≥ 40×40 px** on touch devices.  
30. Bottom sheet disables background scroll and traps focus.

**Acceptance Criteria**  
- Pinch zoom + two-finger pan work on iOS Safari and Android Chrome.  
- Bottom sheet is screen-reader friendly and keyboard-accessible.  
- Touch targets meet size minimums with visible focus/active styles.

### 4.8 Export Scaling Options
31. Export dialog must offer: **Native (1×)**, **Scale Factor** (0.5×–4× in 0.25 increments), **Target Width** (user-entered px; height auto).  
32. Rendering to an offscreen canvas at the selected output size must **redraw** the map, text, and **SVG icons** to remain crisp.  
33. Labels scale proportionally; icon strokes stay sharp.  
34. Large exports should degrade gracefully with clear errors if memory limits are hit.

**Acceptance Criteria**  
- Output PNG matches requested dimensions within ±1 px.  
- 2× exports show visibly sharper label/icon edges than 1×.  
- Exports up to ~8000 px width succeed on modern hardware or show a helpful error.

---

## 5. Non-Goals (Out of Scope)

- Real-time collaboration or shared links.  
- Advanced drawing/layers or external basemap APIs.  
- Custom font uploads (system/default fonts only).  
- **Custom icon uploads** (fixed set of 12 Bootstrap 5 Icons in v1.1).  
- Server-side storage or authentication.

---

## 6. Design Considerations

**Bootstrap Components (v5.3):**  
- **Modal** (desktop editor), **Offcanvas (bottom)** for mobile editor, **Forms** (inputs, validation), **Buttons/Button Group**, **Input Group**, **Grid** layout, **Tooltip/Popover** styles (used with custom JS timing), **Alert/Toast** for errors, **Navbar** (optional simple toolbar).

**Responsive Behavior & Breakpoints:**  
- **xs/sm (mobile):** bottom-sheet editor; large buttons; single-column layout; sticky zoom toolbar.  
- **md/lg (tablet):** two-column modal layout if space permits; increased canvas area.  
- **xl/xxl (desktop):** side preview in modal; persistent toolbar; expanded icon grid.

**Custom CSS (beyond Bootstrap):**  
- Map canvas stacking context and transform container.  
- Tooltip arrow, shadow, and max-width; high z-index.  
- Selected-label focus ring; drag cursor.  
- Touch target padding utilities and dense spacing variants.

**Color & Typography:**  
- Default Bootstrap theme with dark-mode support via `prefers-color-scheme`.  
- Label color picker; UI respects Bootstrap variables for consistent theming.

**Mockups:**  
- TBD (wireframes for modal/bottom sheet, tooltip, export dialog).

---

## 7. Technical Considerations

**HTML Structure:**  
- Semantic landmarks: `<header>` (toolbar), `<main>` (canvas), `<aside>` (optional panels), `<footer>` (status).  
- Labels as DOM elements positioned within a transformable container.

**JavaScript Approach:**  
- Vanilla ES6+ with a modular pattern (IIFE or ES Modules without build step).  
- **Event delegation** for label interactions; **Pointer Events** for cross-input support.  
- **Passive listeners** for touch/scroll; **requestAnimationFrame** for drag/zoom updates.  
- Utility modules: geometry/coordinates, serialization (JSON), export renderer, tooltip controller, storage helpers.

**Bootstrap Version:**  
- **Bootstrap 5.3** and **Bootstrap Icons** (12 curated SVGs embedded locally).

**Browser Compatibility:**  
- Chrome/Edge/Firefox/Safari (latest 2 versions); iOS Safari 16+, Android Chrome current.

**Performance Targets:**  
- Smooth drag at ≥ 60 fps on desktop for ≤200 labels and ~8MP images; comparable smoothness on modern mobile.  
- Tooltip show/hide within spec timing; export within reasonable memory/time bounds.

**Dependencies:**  
- Bootstrap 5.3 (CSS + JS), Bootstrap Icons (bundled SVGs). No other runtime deps.

**Data Storage Architecture:**  
- **Files:** Primary persistence via **JSON project files** (download/upload).  
- **localStorage:** Small preferences (theme mode, last export choice, last icon/color/size).  
- **IndexedDB (optional future):** Cache thumbnails or very large maps if needed.  
- **Storage Abstraction:** Lightweight helper with namespaced keys; versioned schema for JSON projects.

**Offline Strategy:**  
- App is offline by design (no network). Optional future **Service Worker** for asset caching and PWA install.

**Data Migration:**  
- JSON project includes `"version": 1`. Future schema bumps must include migration logic and validation; reject unknown major versions with helpful error.

**Testing Approach:**  
- Manual test checklist for core flows.  
- Lightweight JS assertions for coordinate math and JSON round-trip (no build step).

---

## 8. Success Metrics

- ≥ 95% of attempted exports succeed without visual defects.  
- 100% JSON round-trip fidelity for label fields and positions.  
- Tooltips appear within 1.0–1.1s on desktop; tap-to-toggle works on touch.  
- Interactive performance meets targets on reference devices.  
- Achieve full **mobile responsiveness** across breakpoints.

---

## 9. Accessibility Requirements

- **WCAG 2.1 AA** intent.  
- Keyboard navigation for all controls; modal/bottom sheet traps focus; ESC closes.  
- ARIA roles/labels for modal/offcanvas, icons (labeled by text), and tooltips (`aria-describedby` where applicable).  
- Screen-reader access to label metadata via an “Inspect label” detail view (non-hover fallback).  
- Color contrast ≥ 4.5:1 for text and UI; visible focus indicators.

---

## 10. Open Questions

1. Finalize the exact **12 Bootstrap Icons** (names/mapping).  
2. Do we need a **legend export** in v1.x, or keep in roadmap?  
3. Should we expose **bold/italic** text controls?  
4. Any minimum mobile viewport to support (e.g., 360px wide)?  
5. Do we introduce a minimal **Service Worker** now or defer to v1.2?

---

## Appendix A — JSON Project Schema (v1)

```json
{
  "version": 1,
  "map": {
    "name": "my-map.png",
    "dataUrl": "data:image/png;base64,..."
  },
  "view": {
    "zoom": 1.0
  },
  "labels": [
    {
      "id": "uuid-1",
      "x": 123.45,
      "y": 678.90,
      "text": "Harbor District",
      "color": "#222222",
      "fontSize": 16,
      "iconId": "pin",
      "tooltip": "Fisher guild HQ; busy mornings.",
      "createdAt": "2025-08-19T00:00:00Z",
      "updatedAt": "2025-08-19T00:00:00Z"
    }
  ]
}
```

---

## Appendix B — Suggested Enhancements / Roadmap

- **Undo/Redo** history for edits and moves.  
- **Snap & Guides** (grid, smart alignment, snap-to-pixel).  
- **Grouping & Multi-Select** for bulk edits.  
- **Label Backgrounds** (rounded chip with opacity).  
- **Legend Generator** (icon → label list as PNG/panel).  
- **Search / Jump-to Label**; autofocus zoom to selection.  
- **Minimap Navigator** for quick panning.  
- **Import from CSV/JSON** to bulk-create labels.  
- **Template Presets** (style presets: size/color/icon).  
- **Localization** and **Theming** (light/dark).  
- **Autosave to localStorage**, schema versioning, integrity checks.
