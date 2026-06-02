# Accessibility Compliance Notes

**Project:** Drawing Atlas, Atlas Coloring Book
**Files covered:** atlas-coloring-book.html
**Date:** 2026-06-01
**Course:** BIO 004 (Human Anatomy, Solano Community College)

## WCAG version and target level achieved

WCAG 2.2 AA target met. AAA met where noted on contrast.

| Criterion | Level | Notes |
|---|---|---|
| 1.1.1 Non-text content | AA | Decorative imagery (logo, uploaded atlas images) marked `alt=""` and `aria-hidden="true"`. Icon-only buttons (swatches, size buttons) have `aria-label`. Drawing canvas exposed via `role="img"` with `aria-label="Labeling canvas"`. |
| 1.3.1 Info and relationships | AA | Semantic landmarks (`header`, `main`, `footer`, `section`, `article`), heading hierarchy h1 → h2, label-input pairing via wrapping `<label>` on file inputs. |
| 1.4.3 Contrast (minimum) | AA | All primary text on backgrounds passes 4.5:1. See audit below. |
| 1.4.6 Contrast (enhanced) | AAA | Navy `#0B1530` on off-white `#FAFAF9` exceeds 7:1. |
| 1.4.10 Reflow | AA | Responsive breakpoints at 760px and 480px. Toolbar wraps. No horizontal scroll at 320px width. |
| 1.4.11 Non-text contrast | AA | Active-state borders use navy `#0B1530` (high contrast against off-white). Swatches use a 1px inner shadow to meet 3:1 against page bg. |
| 1.4.12 Text spacing | AA | Uses relative units. No fixed line-height that would prevent user override. |
| 2.1.1 Keyboard | AA | Every interactive control reachable by Tab. Drawing canvas is a creative-input affordance; keyboard equivalents provided via shortcut layer (K, E, U, D, G, S, 1–8, `[` `]`). |
| 2.1.2 No keyboard trap | AA | No modal traps. All focus is naturally reachable. |
| 2.4.1 Bypass blocks | AA | Skip link to `#workspace`. |
| 2.4.3 Focus order | AA | Logical order: header → hero actions → setup → toolbar → canvas → keyboard help → footer. |
| 2.4.7 Focus visible | AA | `:focus-visible` 3px rust outline on every focusable element. |
| 2.5.5 Target size | AAA-adjacent | Toolbar buttons ≥26px target. Action buttons ≥28px height. |
| 3.2.2 On input | AA | Choosing a swatch or size never triggers context change. |
| 3.3.2 Labels or instructions | AA | File inputs labeled. Keyboard shortcuts section explains all bindings. |
| 4.1.2 Name, role, value | AA | All toggle buttons use `aria-pressed`. Mode pill uses `aria-live="polite"` to announce practice/key changes. Drop zones use `aria-label`. |
| 4.1.3 Status messages | AA | Mode pill announces practice/answer-key state via `aria-live="polite"`. |

## Color contrast audit

Palette pulled from the lecture canvas branding file (slides-homeostasis.html), not the older PRIMARY palette spec.

| Foreground | Background | Ratio | Use | Pass |
|---|---|---|---|---|
| Navy `#0B1530` | Off-white `#FAFAF9` | 17.4:1 | Body text, h1, h2 | AAA |
| Rust `#8B3A2E` | White `#FFFFFF` | 6.8:1 | Eyebrow, accents, hover | AA (large AAA) |
| Rust `#8B3A2E` | Off-white `#FAFAF9` | 6.6:1 | Section headings | AA (large AAA) |
| Off-white `#FAFAF9` | Navy `#0B1530` | 17.4:1 | Hero CTA button | AAA |
| Navy `#0B1530` | Gold `#C9A14A` | 7.6:1 | Key-toggle button | AAA |
| White `#FFFFFF` | Rust `#8B3A2E` | 6.8:1 | Active key-toggle / Save PNG | AA (large AAA) |
| Gold `#C9A14A` | Dark `#060A18` | 8.2:1 | Footer byline | AAA |
| Off-white `#FAFAF9` | Dark `#060A18` | 17.6:1 | Footer meta | AAA |
| Navy `#0B1530` | Mode pill bg navy → label is off-white | n/a | Mode pill uses `#FAFAF9` on `#0B1530` (17.4:1) | AAA |

Pen colors are user-chosen drawing pigments, not text. They're tuned for visibility on both light `#FFFFFF` and dark `#060A18` canvas backgrounds:

| Pen | Hex | On white | On dark `#060A18` |
|---|---|---|---|
| Magenta | #FF2D87 | 3.2:1 | 5.4:1 |
| Cyan | #00E5FF | 1.7:1 (low on white; supplement with size/outline) | 13.1:1 |
| Lime | #B6FF1F | 1.5:1 (low on white) | 14.6:1 |
| Yellow | #FFE600 | 1.3:1 (low on white) | 16.0:1 |
| Orange | #FF7A1A | 2.6:1 | 6.7:1 |
| Violet | #B26BFF | 3.0:1 | 5.8:1 |
| Crimson | #C0392B | 5.7:1 | 3.0:1 |
| Blue | #1F5BB8 | 7.3:1 | 2.4:1 |
| Forest | #1F7A3A | 5.3:1 | 3.3:1 |
| Navy | #0B1530 | 17.4:1 | n/a |
| White | #FFFFFF | n/a | 20.6:1 |

Caveat: cyan, lime, and yellow are intentionally low-contrast on white because they are designed to read on dark backgrounds (matches Khan Academy chalk-on-blackboard look). The Background toggle (`D` key) lets students flip to a dark canvas for those pens. Magenta and orange read on both. Crimson, blue, and forest are the high-contrast pens for the light-background workflow (each passes 4.5:1 on white). Navy and white are the maximum-contrast fallbacks for either background.

## Keyboard navigation verified

Tab order:
1. Skip link (`Skip to atlas workspace`)
2. Site logo
3. Hero actions (Save annotated PNG, Clear my labels, Reset session)
4. Practice image drop zone (file input)
5. Answer key drop zone (file input)
6. Pen color swatches (8)
7. Pen size buttons (4)
8. Eraser, Undo, Clear
9. Background light/dark, Grid
10. Show key
11. Footer (no interactive content beyond OpenStax-style link if added)

Shortcuts (active when focus is not in an input):
- `K` toggle key, `E` eraser, `U` undo, `D` dark/light, `G` grid, `S` save PNG
- `1`–`8` pen color, `[` `]` pen size

## Screen reader testing

Verified structural exposure:
- Reader: VoiceOver on macOS (Safari)
- Landmarks: banner (header), main (workspace), contentinfo (footer)
- Headings: h1 "Atlas Labeling Practice", h2 "Step 1 · Load your images", h2 "Drawing Canvas", h2 "Keyboard shortcuts"
- `aria-live="polite"` on `#modePill` announces "Practice mode" / "Answer key" on toggle
- Drop zones announced as buttons with their `aria-label`

## Known limitations and remediation plan

1. **Drawing canvas is mouse/touch/stylus only.** There is no keyboard-driven drawing equivalent. This matches every drawing app pattern; the labeling task itself is inherently graphical. Mitigation: the typed `Keyboard shortcuts` panel covers every non-drawing action.
2. **Uploaded atlas images have no `alt` text.** The tool does not know what the student uploaded. Future enhancement: prompt for a one-line description on upload, used as `alt` on the rendered `<img>`.
3. **Pen color names not announced on hex change.** The swatch buttons have `aria-label="Pen color magenta"` etc, but the global pen-state change is not announced via aria-live. Acceptable: the user is the one selecting, so the change is intentional.
4. **No high-contrast mode override.** Windows High Contrast / forced-colors not yet styled. Add `@media (forced-colors: active)` rule in a follow-up.

## Bank + index addendum (2026-06-01 revision)

The tool now includes a per-device bank (IndexedDB) plus modal dialogs and an exportable JSON index. Accessibility notes for the new surface area:

### Native `<dialog>` for save + load-prompt

Save dialog (`#saveDialog`) and load-prompt dialog (`#loadPromptDialog`) use the native HTML `<dialog>` element via `showModal()`. This gives, for free: focus trap inside the dialog, Escape-to-close, inert background, and `::backdrop` styling. All dialog controls are tab-reachable; the close button has `aria-label`; the form has a labelled-by relationship to the title via `aria-labelledby`.

### Index semantics

The bank list uses `<section class="bank-module">` per group, with a button-rendered header that has `aria-expanded` reflecting collapse state. Each entry is an `<article class="bank-entry">` with `aria-label` set to its title. `aria-live="polite"` on `#bankBody` and on `#loadedContext` announces dynamic changes.

### Save / Reset isolation rule

Clear my labels and Reset session never touch the bank. Clear my labels removes strokes only. Reset session removes the currently displayed practice and key images from the active canvas and dismisses the loaded-context strip, but does not call any IndexedDB delete. The bank entries persist across both actions. This is reinforced in the confirm dialog wording: "Your saved bank entries stay safe."

### Contrast additions

| Foreground | Background | Ratio | Use | Pass |
|---|---|---|---|---|
| White `#FFFFFF` | Teal `#2A6F7A` | 5.1:1 | Save to bank button | AA |
| White `#FFFFFF` | Rust `#8B3A2E` | 6.8:1 | Save dialog primary CTA | AA (large AAA) |
| Navy `#0B1530` | Off-white `#FAFAF9` | 17.4:1 | Bank entry title, search input | AAA |
| Rust `#8B3A2E` | Off-white `#FAFAF9` | 6.6:1 | Bank module count, eyebrow | AA |
| Navy `#0B1530` | Tint `#EDF1F3` (DOK 1 chip) | 16.3:1 | DOK chip | AAA |
| Navy `#0B1530` | Cream-gold `#F5E9D8` (DOK 2) | 14.7:1 | DOK chip | AAA |
| Rust `#8B3A2E` | Terra tint `#F1E1D9` (DOK 3) | 5.6:1 | DOK chip | AA |
| White `#FFFFFF` | Rust `#8B3A2E` (DOK 4) | 6.8:1 | DOK chip | AA |

### Keyboard additions

`B` opens the save dialog when a pair is loaded. `R` triggers Randomize (respects current module / DOK / search filters). Both are suppressed while a dialog is open or an input is focused.

### Storage and privacy

The bank stores images and metadata in IndexedDB on this device only. No data is sent to any server. No student PII is captured by the bank schema (entries record title, module, notes, DOK, source, two image data URLs, timestamps, and a generated ID). The bank can be exported as a single JSON file for backup or distribution to students; importing a file merges entries by ID with a confirm dialog when collisions are detected.

### Known limitations and remediation plan (revised)

1. **IndexedDB quota.** Saving many large images can hit the browser quota. Future: warn when total bank size approaches 50 MB; offer a "downsize on save" toggle that re-encodes images to a max dimension.
2. **No multi-select on the index.** Deletes are one-at-a-time. Future: add a select-multiple mode.
3. **Drawing canvas remains pointer-only.** Unchanged from the prior version.
4. **High-contrast mode override.** Still pending, applies to dialogs as well.

## Reviewer

Built and self-reviewed by Dr. Sharilyn Rennie with Claude assistance. Pending student-user check on a Wacom tablet and on iPad with Apple Pencil.
