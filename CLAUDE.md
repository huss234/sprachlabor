# Sprachlabor

A single-file German reading lab: everything is in `index.html`, served by GitHub Pages from `main`.

## Before any visible change

Read `DESIGN.md` first and follow it exactly. It is this app's own design
language (Ulm/Swiss functionalist, hairline, one cobalt accent). It is not
Material Design, M3 Expressive, Fluent or Apple HIG, and nothing from those
belongs here.

- Use only the CSS variables and components listed in `DESIGN.md`.
- Don't invent colours, radii, shadows, fonts, easing curves or icon styles.
- Reuse an existing class or helper (`openMenu`, `askConfirm`, `toast`, `.btn`, `.panel`, `.menu`…) before writing new ones.
- If something genuinely new is needed, build it from the tokens and add it to `DESIGN.md` in the same change.
- Check light and dark themes, and 390px as well as desktop width.

## Other house rules

- The version lives in `APP_VERSION` and in the comment on line 2 of `index.html`; bump both together (3.4 → 3.5).
- Sync and storage are described in the comment block above `const GH`. Any new synced data needs a record type in `Sync.diff()`, `journalFiles()`, `writeSnapshot()` and `applyFile()`. A lesson edit must bump `updatedAt`.
- Never rename a storage key without migrating the saved data.
