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
- Changing screens is a fade-through in two strokes that never overlap. `leave(el)` pins the outgoing screen where it stands (fixed, clipped to the window, `inert`), fades it out still in `--out` (.1s, leaving curve) and then gives its inline style back. Only after `--out` does the new screen arrive: the direct blocks of its `.wrap` rise in with `lift`, 40ms apart. Shared chrome (`.appbar`, `.practab`) stays put; Home runs its own `.hhome.is-enter` cascade, offset by `--out`. A file still slides in from the right (`setpage`) and the shelf back from the left (`setmenu`), both after `--out`. Settings sections fade through the same way (the list first, the section 40ms later), and so do the menu and a section on a phone. No crossfades and no directional slides between tabs. Home ↔ Library leaves a copy of the app bar in the outgoing screen so its content does not jump.
- Theme change: `retheme(t)`, one .42s crossfade of the whole page through a view transition (`::view-transition-old/new(root)`); without view transitions, `:root.is-retheme` eases every colour over instead. Start-up applies the theme with no fade.
- Keyframes to reuse: `pop` (menus: fade, 4px rise, scale .97), `menuout`, `deal` (modals: 14px rise, scale .985), `fade`, `rise`, `slide` / `slideout` (toasts), `leave` (the outgoing screen or section), `pulse` (busy dot), `lift` (content blocks: fade, 8px rise), `growy` / `growx` (bars and meters from their base), `wipe` (a strip revealed left to right), `draw` (a `pathLength="1"` line writing itself), `calslide` (a calendar month sliding the way you went).
- Data that arrives draws itself. On Progress, `progMotion()` plays the difference after every rebuild: `is-grow` on a host makes its bars, meters and lines grow, `is-lift` makes its cards rise in with a 40ms stagger, rings run from their last value (`data-rk`), and plain integers count up (`data-cu`, `countTo()`). Entering the page draws from zero; a quiet rebuild moves only what changed.
- Hover effects that lift or scale sit inside `@media (hover:hover)` so they don't stick after a tap.
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
| Tab bar | `.rail` > `.rail__btn[data-nav]`, `.rail__ind`, `railPlate()` | four tabs: Home, Library, Practice, Progress, and on the side rail (above 860px) a fifth, Settings, alone at the bottom under a hairline (`.rail__sep`). The rail carries no theme switch and no wordmark. On the bottom bar Settings is hidden and reached from the ⋮ menu, and while it is open no tab is lit. An open file is a page inside Library (`tabOf("lesson")` is `library`); it slides in with `setpage` (`.view.is-push`) and the shelf slides back with `setmenu` (`.is-back`). The current tab sits on one `--signal-soft` plate, clipped to the tab, that travels edge by edge (the leading edge first, the far edge 18% later, 420ms, both on `--ease`). Each icon has a rest shape and a current shape with the same path structure (`.mo`, CSS `d:`), so the icon changes shape when its tab becomes current (420ms, 60ms after the plate) and returns in 260ms. The Settings gear turns 60° onto its next tooth |
| Settings list | `.setnav` > `.setnav__group` > `.setnav__list` > `button[data-sect]`, `.setnav__ind`, `setPlate()`, `litNav()` | answers like the tab bar. On a wide screen one `--signal-soft` plate travels from row to row by its edges (`movePlate()`, shared with the rail; rows at a card's top or bottom take its 13px corners), and the lit row's icon tile turns `--signal`. Each icon has a rest and a lit shape (`.mo`, CSS `d:` or a transform; 420ms, 60ms after the plate; back in 260ms): Appearance's dark half turns over, Cards' front card tips off the stack, Studying's hands go once round, Voice's waves open out, Add lessons' arrow lifts out of the tray, Your files' lid lifts and level rises, Sync's arrows go half round, Reference's book opens, About's i stands up. A phone has no plate and lights nothing in the menu, except the row you tap: it lights, its icon changes shape (300ms), and the menu holds for .16s (`.is-hold`, `--out:.26s`) before it fades through to the section |
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
| Disclosure | `.fold` > `.fold__btn` + `.fold__body` (the `.syncp__log` pattern outside a menu) | eyebrow row + count badge + chevron rotating 180°; body reveals with `grid-template-rows 0fr→1fr` plus fade; closed by default; `aria-expanded`; `inert` while closed |
| Empty state | `.empty` / `.pickdeck` | serif headline, 13–14px explanation, dashed rule border on `--surface-2` |
| Meter | `.meter` > `i` | 3px track, `--signal` fill, `--ok` when full |
| Milestone rows | `.miles` > `.mile` | inset list; label left, mono `now / next` right, a `.meter`, then "N to go" in `--ink-3` (`--signal` when 80% there, `.is-near`) |
| Fresh marker | `.wchip.wchip--fresh` | `--signal-soft` pill with `--signal` text: a record or milestone reached this week |
| Page tabs | `.ptabs` + `.ptabs__ind` | text tabs over a rule; one 2px `--signal` underline travels between them (`translateX` + `scaleX`, 300ms `--ease`); the row is built once and only updated |
| App bar | `.appbar` (name, sync pill, ⋮ `appMenu`) | one node, moved by the router into whichever of Home and Library is showing. The ⋮ menu holds Connect GitHub (only until connected; syncing lives on the pill), Import lessons (opens the file picker), Dark mode, Full screen (a plain row that reads Leave full screen while on, `ICO_FS_IN` / `ICO_FS_OUT`; hidden where the browser has no full screen), Settings. A session exits full screen when it ends only if it was not already on |
| Home | `renderHome()` in `#homeBody` > `.hhome` | a dashboard, figures first and no action button at the top. **Instrument** (`.hhero`): the one dark block in the app — `--ink` ground (dark theme: `--surface-2` + rule), no texture. Top: serif German greeting with one quiet sentence under it, eyebrow date right. Then a dial of two rings of one weight (cards outside in `currentColor`, minutes inside in `--stress`, tracks at 9%), one number in the middle (`.hfig`: Archivo 600, tabular lining figures, −.045em; smaller from three digits, `.is-long`) over a one-word eyebrow ("Cards", or "Met") that must fit inside the inner ring, and a legend under the dial instead of text inside it; the streak as a second `.hfig`; the week as seven small rings (met = a `--stress` disc with a tick, rest = dotted track, today's letter `--stress`; on phones the week takes its own row under a hairline); a readout `dl` (`.hfacts`, desktop only); thirty days as an even strip of cells (opacity = heat level, today `--stress`, outlined when empty) with the first date and "Today" under the ends. Big figures in the instrument use `.hfig`, not mono, because Plex Mono's dotted zero reads as code at that size. Then **figures** (`.hkpi` ×4: words known with a status mix bar, this week with a ▲/▼ delta, accuracy with a small ring, time this week), **Pick up** (`.hsess` unfinished run with a ring, `.hread` last file with a serif German excerpt in „…“) beside **Your cards** (`.hbar` status bar, `.htile` ×3 with a coloured top spine, Needs work `.pickup`), and a swipeable **Recently opened** shelf (`.hshelf` > `.hbook`, scroll-snap, small ring). `--stress` is the instrument's needle colour and appears nowhere else on the page, apart from the name's stress band in the app bar |
| Rings and arcs | `hArc()`, `hRing()` | SVG circles with `pathLength="1"` and `stroke-dasharray:1 1`; the value is the inline `stroke-dashoffset` (1 − p). An arc at zero gets `.is-zero` so its round cap does not leave a dot |
| Home motion | `.is-enter`, `.is-arriving` | blocks rise in turn (`lift`, `backwards` fill only, 70ms apart; tiles and books 45ms apart); `.is-arriving` holds arcs at 1, week discs at scale .4 and heat cells at opacity 0 with `!important` for two frames, then lets them run to their inline values (arcs 1.1s, week rings 60ms apart, heat cells 14ms apart); figures count up (`data-hcu`). Tiles lift 3px on hover and press to .97 |
| Resume block | `.hsess` | `--signal` left spine, ring of the share done, primary Resume, ghost Let it go. Leaving: `.is-leaving` fades on the leaving curve, then every `[data-k]` block below glides to its new place (WAAPI, no fill) |
| Icon | `.bmark` (inline SVG, viewBox 48) and the favicon | a speech bubble tile (three rounded corners, the bottom-left one square as its tail) holding three rounded bars, the syllables Sprach·la·bor as a voice trace: tall `--stress`, short, middle. Tile `--ink` with `--paper` bars; dark theme: `--surface-2` tile with a `--rule` hairline and `--ink` bars. The favicon is the light version in raw hex. Shown in the browser tab, the rail and About, never on Home. `.is-play` grows the bars from their centres in turn (`growy`) |
| Name | `.wmark`, built by `wordmark(node)`, replayed by `playMark()` | the word in `--serif`: "Sprach" 500, "labor" 400, one `--stress` band just under the baseline of "Sprach" (the stress, as in the pronunciation strip). Sits in the app bar (25px, 23px on phones) and in the About card. `.is-play`: the syllables rise out of a mask one after another, held apart by `--ink-3` dictionary dots (each fades out on the leaving curve and is gone the moment the syllables start to close, so a dot never meets a letter), close into the word, then the band `wipe`s in (about 2s, all `backwards` fill). Plays on app start and on arriving at About |
| About card | `.about__hero` | the two marks together: icon (64px, 56px on phones) top left, version top right as an eyebrow over a 24px Archivo figure, the name below at the card's full width (42px, 36px on phones) so its spread syllables never meet anything, a one-line tagline, then a hairline and the name's dictionary entry (`.art--das`, serif word, `.say` strip, gloss in `--ink-3`). `.is-play` on the card grows the icon's bars, lifts the version, tagline and entry in turn, and the name starts after the page has settled (`--wd:.35s` delays every wordmark step) |
| Page header | `.page-head` | title, subtitle max 52ch, actions right, rule underneath |
| Lesson header | `.masthead` + `.reader__bar` | eyebrow (level · topic) and mono `known/total` on one rule; serif title; one line of subtitle (German, then English in `--ink-3`); actions: one primary, one secondary, then a 38px dots button (`.masthead__more`) opening an `openMenu` for rarer actions. The sticky reader bar holds only the English `.seg` (eyebrow label) and a 36px `Aa` button |

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
