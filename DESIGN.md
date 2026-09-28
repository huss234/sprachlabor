# Sprachlabor design language

This is the standard every screen, menu and control in `index.html` follows.
It describes what the app already does. Nothing here is aspirational. If a
new part needs something this file does not cover, reuse the nearest
existing pattern and add it here in the same change.

**Lineage:** Ulm-school functionalism (HfG Ulm, Otl Aicher, Braun) and the
Swiss International Typographic Style, with German road-sign colour.
Calm paper, graphite ink, hairline rules, one cobalt accent. It is **not**
Material Design, Material 3 Expressive, Fluent or Apple HIG. Never borrow
their shapes, ripples, springs, tonal containers, FABs or icon fonts.

---

## 1. Tokens (use the variables, never the raw values)

All tokens live on `:root`, and `:root[data-theme="dark"]` overrides them.
A new colour, radius, shadow or font is a bug. Pick the nearest token.

### Colour

| Token | Light | Dark | Job |
|---|---|---|---|
| `--paper` | `#ECEEF0` | `#0F1214` | page background |
| `--paper-2` | `#E1E5E8` | `#0B0E10` | deeper page areas |
| `--surface` | `#FFFFFF` | `#171B1E` | cards, panels, menus, buttons |
| `--surface-2` | `#F5F7F8` | `#1E2327` | inset blocks, hover fill, field fill, modal footer |
| `--ink` | `#14181B` | `#E8EBEE` | primary text |
| `--ink-2` | `#4A5359` | `#A5AFB6` | secondary text |
| `--ink-3` | `#7C878E` | `#78838A` | labels, meta, icons at rest, hover border |
| `--rule` | `#D3D9DD` | `#2B3237` | every border |
| `--rule-2` | `#E4E9EC` | `#232A2E` | dividers inside a surface |
| `--signal` | `#1338BE` | `#7D9BFF` | the one accent: primary button, focus, on-states, links |
| `--signal-soft` | `#E7EBFA` | `#1B2338` | accent tint: focus halo, selected chip |
| `--stress` / `--stress-ink` | `#FFC800` / `#1A1400` | same | stressed syllable, text selection, filename tag |
| `--der` `--die` `--das` | cobalt / red / green | lighter | German gender colours |
| `--ok` | `#17795E` | `#5CCFA6` | success, "synced" |
| `--warn` | `#B4620A` | `#E5A159` | waiting, busy |
| `--die` | `#C0264C` | `#FF8FA8` | also the error and danger colour |
| `--hard` `--learn` `--known` (+ `-soft`) | red / amber / green | lighter | card status; `-soft` is the tinted pill background |
| `--heat-0…4` | grey → cobalt | dark ramp | calendar heat map only |

The primary button text is `#fff` in light theme and `#0B0E10` in dark.

### Shape, depth, layout

- Radius: `--r-sm:4px` (tags, inner segments) · `--r-md:8px` (buttons, fields, menus, inset blocks) · `--r-lg:14px` (cards, panels, modals). Pills and dots use `100px`. Nothing else.
- Border: always `1px solid var(--rule)`. A thicker left spine (`3px`, and `5px` on hover) carries status on cards and warning boxes.
- Shadow: `--shadow` (resting lift) and `--shadow-lift` (menus, modals, toasts, hovered cards). Surfaces are flat by default and are separated by hairlines, not shadows.
- Layout: `.wrap` max-width 1120px with 40px side padding (20px at ≤860px). The desktop rail is `--rail:76px`. Reading measure is `--measure:66ch`.
- Breakpoints in use: 860px (rail becomes a bottom bar), 720px, 640px, 520px. Always check 390px wide.

### Type

| Family | Token | Job |
|---|---|---|
| Archivo | `--sans` | all UI and English |
| Newsreader | `--serif` | German source text, empty-state headlines |
| IBM Plex Mono | `--mono` | numbers, counts, ids, times, pronunciation |

- Page title: 27px / 600 / letter-spacing −.022em.
- Section title (`.setsec__head h2`): 19px / 600 / −.018em. Panel `h3`: 15px / 600 / −.01em.
- Body: 16px. UI text: 13–13.5px. Secondary text: 12–13px in `--ink-2`.
- **Eyebrow label** (`.eyebrow`, `dt`, `.field label`): 10–11px, weight 700, letter-spacing .11–.16em, UPPERCASE, `--ink-3`. This is the signature label style. Use it for every small heading and key.
- Any number the user reads as data is set in mono.

### Motion

- One easing: `--ease: cubic-bezier(.2,.7,.3,1)` for everything that arrives or moves.
- Leaving: `cubic-bezier(.4,0,1,1)`, about 25% faster than arriving.
- Durations: press .1s · hover and colour .14–.16s · small things appearing .14–.22s · panels and modals .22–.26s · meters .5s.
- Press feedback: `transform:scale(.97)`, or `.94` on small icon buttons, or `translateY(1px)` on `.btn`.
- Hover on a card: `translateY(-3px)`, `--shadow-lift`, border `--ink-3`.
- Keyframes to reuse: `pop` (menus: fade, 4px rise, scale .97), `menuout`, `deal` (modals: 14px rise, scale .985), `fade`, `rise`, `slide` / `slideout` (toasts), `pulse` (busy dot).
- Name the exact properties in `transition`, never `all`. Animate `transform` and `opacity`. Remove things only after their exit animation ends.
- Every new animation needs a `@media (prefers-reduced-motion:reduce)` rule that keeps the fades and drops the movement.
- No springs, bounces, overshoot or ripples.
- Icon shape changes: give the two shapes the same path commands and switch `d:` in CSS, or turn a `<g>` with `transform-box:fill-box`. A stroke that writes itself uses `pathLength="1"`, `stroke-dasharray:1 2` and `stroke-dashoffset` 1→0.

---

## 2. Components (reuse these classes before writing new CSS)

| Need | Use | Anatomy |
|---|---|---|
| Button | `.btn`, plus `.btn--primary`, `.btn--ghost`, `.btn--danger`, `.btn--sm` | 38px high (30px small), `--r-md`, 1px rule border, 13px/600, 15px icon before the text |
| Segmented toggle | `.seg` / `.seg--ico` | joined buttons 36px high, hairline dividers; selected = `--ink` fill with `--paper` text |
| Screen switch | `.practab` + `practab(node, from)` | a two-way `.seg` whose `--ink` fill is one thumb; each screen carries its own copy and the thumb and label colours slide over from the screen you came from (300ms `--ease`). Used for Cards / Spelling under the Practice tab |
| Tab bar | `.rail` > `.rail__btn[data-nav]`, `.rail__ind`, `railPlate()` | four tabs: Library, Lesson, Practice, Progress. Settings is reached from the ⋮ menu only. The current tab sits on one `--signal-soft` plate, clipped to the tab, that travels edge by edge (the leading edge first, the far edge 18% later, 420ms, both on `--ease`). Each icon has a rest shape and a current shape with the same path structure (`.mo`, CSS `d:`), so the icon changes shape when its tab becomes current (420ms, 60ms after the plate) and returns in 260ms |
| Pill / tag | `.chip`, `.chip--level` | 24px, radius 100px, 11px/600 with .04em spacing |
| Count badge | `.shelf-label b` style | mono 10px/600, 1px rule border, radius 100px, padding 1px 7px |
| Popup menu | `openMenu(anchor, items, cls)`, `.menu` | surface, rule border, `--r-md`, `--shadow-lift`, 5px padding, rows 8×10px 13px, 15px icons in `--ink-3`, `hr` dividers, `.is-danger`, switch rows (`on:`), `.menu__meta` mono meta. Grows from its button; closes on outside tap, Escape and scroll |
| Rich popover | `.menu` plus a modifier (see `.menu--sync`) | 18px padding, head / inset block / actions / disclosure. Register as `menuNode` so it closes like a menu |
| Modal | `askConfirm({title, desc, ok, danger})`, `askText(...)`, `.scrim` + `.modal` | 460px, `--r-lg`, head 20/22px, body 18/22px, footer on `--surface-2` with right-aligned buttons |
| Toast | `toast(msg, bad)` | ink block bottom-left, slides in; `bad` = `--die` |
| Settings block | `.panel` inside `.setsec` | surface, `--r-lg`, 22px padding, `h3` plus 13px `--ink-2` intro paragraph |
| Form field | `.field` > `label` + input, `.hint` | 40px input on `--surface-2`; focus = `--signal` border and 3px `--signal-soft` halo |
| Switch | `.switch` > `input` + `i` + `span` | 40×23 track, `--signal` when on |
| Facts | `.stats` (big mono numbers) · inset `dl` rows (see `.syncp__rows`) | rows: eyebrow `dt` left, mono `dd` right, `--rule-2` dividers, block on `--surface-2` |
| List rows | `.rows` > `.row` | 11×14px, `--rule-2` dividers |
| Warning box | `.warnbox` | `--surface-2`, 3px `--warn` left spine |
| Status dot | `.dot` + `--ok` / `--wait` / `--busy` / `--bad` | 9px circle; busy pulses |
| Disclosure | see `.syncp__log` | eyebrow row + count badge + chevron rotating 180°; body reveals with `grid-template-rows 0fr→1fr` plus fade; closed by default; `aria-expanded`; `inert` while closed |
| Empty state | `.empty` / `.pickdeck` | serif headline, 13–14px explanation, dashed rule border on `--surface-2` |
| Meter | `.meter` > `i` | 3px track, `--signal` fill, `--ok` when full |
| Page header | `.page-head` | title, subtitle max 52ch, actions right, rule underneath |

**Icons:** inline SVG, 24×24 viewBox, `fill="none" stroke="currentColor"`,
stroke-width 1.7–1.8 for normal icons (2 for small chevrons and marks), round caps and joins. Reuse the ones in `MI` and
`ICO_*` before drawing new ones. No icon fonts and no filled glyph sets.

---

## 3. Rules of thumb

1. **Hairlines over shadows, flat over layered.** A new grouping is an inset block on `--surface-2` with a `--rule-2` border, not a raised card inside a card.
2. **Give content room.** Nothing touches a container edge. Use 14–18px inside popovers, 22px in panels, and inset blocks for key/value facts.
3. **One accent.** `--signal` marks the single primary action and the active state. Everything else is ink greys. Status colours only mean status.
4. **Labels whisper, data speaks.** Keys are uppercase eyebrows in `--ink-3`; values are mono in `--ink`.
5. **Things that grow are folded.** Logs, histories and long lists sit behind a disclosure, closed by default, so a panel stays the same size.
6. **Every tap answers.** Hover change, press scale, and a toast or visible state change after an action.
7. **Dark mode is not optional.** Only tokens, so dark works by itself. Check it anyway.
8. **Copy voice.** Plain, calm, second person, full sentences, no exclamation marks. Say what happened and that nothing is lost. For example: "GitHub asked for a pause. Nothing is lost; it tries again in 3 min."
9. **Code style.** Double quotes, `const`, small comment blocks that explain *why*, CSS written one rule per line in the file's compact style, new CSS placed next to the component it extends.
