# Music Discography Skill — Handoff to Claude Code

## What's Done

### SKILL.md (draft v1)
A skill that generates comprehensive, visually rich HTML discography pages for any artist. Key features:
- Web search research workflow
- Jazz-aware mode (auto-detects jazz artists, emphasizes personnel/labels/sessions)
- General mode (rock/pop/etc — emphasizes producers, chart positions, career arc)
- HTML artifact output with album cards, collapsible track listings, genre tags, era navigation
- GitHub Pages collection structure (consistent fonts, CSS variables, back-links, filename conventions)

### Test Cases Built
1. **John Coltrane** (`coltrane-discography.html`) — Jazz mode. 28 studio + 14 live albums organized by label era (Prestige → Blue Note → Atlantic → Impulse!). Includes posthumous studio and live sections.
2. **Radiohead** (`radiohead-discography.html`) — General mode. 9 studio + 2 live albums organized by career era. Different accent color, producer credits instead of personnel, chart positions.

### Design Language
- Dark background (#0f0e0c), cream/amber typography
- Fonts: Playfair Display (display), Source Serif 4 (body), JetBrains Mono (metadata)
- Color-coded label badges per record label
- Sticky era navigation bar
- Subtle noise texture overlay
- Highlighted card treatment for landmark albums (A Love Supreme, OK Computer)
- "← Collection" back-link for GitHub Pages integration

## What's Next (for Claude Code)

### 1. Collection Cross-Reference
Add "you own this" indicators on album cards by merging two data sources:
- **Discogs API** — Token: `QUtVSwsswxBTqjbxcjNYCVkZtkFceyloglvXHsxX`. Pull full collection, extract artist/album/format.
- **iTunes XML on T9 drive** — Same source as Sideman (~4,600 albums as Apple Lossless). Parse for artist/album pairs.
- Merge into `collection.json` with `{artist, album_title, source: ["local"|"discogs"|"both"]}`.
- Update SKILL.md to instruct Claude to check this file and add visual indicators (e.g., vinyl icon for Discogs, digital icon for local, both for both).

### 2. GitHub Pages Scaffolding
- Create repo structure: `index.html` + individual artist pages
- Index page: artist cards with name, years, genre, album count, linking to each page
- Same design language as the discography pages
- Ready for `clwesterl` GitHub account (user already has GitHub Pages experience with Zwift tracker)

### 3. Finalize & Package Skill
- Update SKILL.md with collection-aware instructions
- Test with collection data
- Package as `.skill` file

## User Context
- Chad, academic in Tucson
- Jazz collector with ~4,600 album digital library + Discogs physical collection
- Maintains Sideman app (Flask, SQLite, FTS5) for jazz musician/album search
- GitHub: clwesterl
- Has Claude Code set up and has used it for Sideman development
