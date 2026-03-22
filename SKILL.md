---
name: music-discography
description: Generate a comprehensive, visually rich discography for any musical artist. Use this skill whenever the user asks for an artist's discography, album history, catalog overview, "what albums did X make", "show me everything by Y", or any variation of wanting to see an artist's body of recorded work. Also trigger when the user asks to explore, browse, or learn about an artist's releases, or wants album summaries, release timelines, or recording histories. Works for any genre but has special awareness of jazz conventions (leader vs. sideman dates, session personnel, label significance).
---

# Music Discography Skill

Generate a comprehensive, beautifully presented discography for any musical artist as an interactive HTML artifact.

## Workflow

### 1. Research the Artist

Use web search to build a complete picture of the artist's official discography. Search strategy:

- Start with `[artist name] discography` to get the overview
- Follow up with `[artist name] albums` or `[artist name] discography site:wikipedia.org` for structured data
- For jazz artists, also search `[artist name] discography leader sessions` — jazz discographies distinguish between leader dates and sideman appearances; focus on leader dates unless the user asks otherwise
- For prolific artists (50+ albums), do additional searches to make sure you're not missing later or lesser-known releases
- If the artist is obscure or results are thin, try `[artist name] [record label] discography` or `[artist name] allmusic`

**What to gather per album:**
- Title
- Release year
- Record label
- Album type (studio or live)
- Brief context: what makes this album notable, where it sits in the artist's arc, critical reception, sonic character (2-3 sentences)
- Key personnel (especially for jazz: who's in the band)
- Track listing (if readily available — don't burn excessive searches on this)
- Genre tags or sonic descriptors (1-3 short tags)

### 2. Organize the Discography

**Default scope:** Studio albums and live albums. Exclude compilations, greatest hits, box sets, and reissues unless the user specifically asks for them. EPs and singles are excluded by default but can be included on request.

**Sorting:** Chronological by release year, oldest first.

**Grouping:** If the artist has both studio and live albums, present studio albums first as the primary section, then live albums in a separate section. If the artist only has one type, no need to section them.

**Information hierarchy per album card (in order of priority):**
1. Album title + year + label
2. Brief summary / context (2-3 sentences — this is the most important element)
3. Key personnel (band members, producer if notable)
4. Genre tags / sonic description (short tags like "hard bop," "modal jazz," "post-punk," "synth-pop")
5. Track listing (collapsible or secondary — don't let it dominate the card)

### 3. Genre-Aware Presentation

**Jazz mode** (auto-detect if the artist is primarily a jazz musician):
- Emphasize personnel on every album — who played what instrument
- Note the label with context (Blue Note, Prestige, Riverside, ECM, etc. carry meaning in jazz)
- Distinguish leader dates from co-leader dates if applicable
- Note significant session details (e.g., "Recorded at Van Gelder Studio")
- Use warmer, more sophisticated design language

**General mode** (rock, pop, hip-hop, electronic, classical, etc.):
- Personnel less prominent unless the user's priority ranking elevates it
- Focus on the album's place in the artist's career arc and broader cultural context
- Note significant producers, collaborators, or lineup changes

### 4. Build the HTML Artifact

Create a single-file HTML artifact with embedded CSS and minimal JS. The design should feel like a high-quality record guide or liner notes — not a generic data table.

**Design principles:**
- Album cards in a responsive grid (2-3 columns on desktop, 1 on mobile)
- Each card: album title, year, label, summary, personnel, genre tags
- Track listings should be collapsible (hidden by default, click to expand) to keep the layout clean
- Use image_search to find album cover art for each album — search `[artist name] [album title] album cover`. Display covers prominently on each card.
- Typographic hierarchy: album title large and bold, year/label as metadata, summary as readable body text, personnel and tags as secondary info
- Color palette: derive from the genre or artist's era. Jazz → warm tones (amber, cream, deep blue). Rock → higher contrast. Electronic → cooler palette. But always tasteful and cohesive.
- Include a header with the artist's name, active years, and a one-sentence overview
- Include counts: "X studio albums, Y live albums"
- If the discography is large (15+ albums), include a sticky mini-nav or decade markers

**Technical requirements:**
- Single HTML file, all CSS inline or in a `<style>` block
- No external dependencies except Google Fonts (one display font + one body font)
- Album cover images via image_search results
- Responsive: works on desktop and mobile
- Smooth expand/collapse for track listings
- Light/dark mode toggle is a nice touch but not required

### 5. Collection Cross-Reference

Before building the HTML, check `collection.json` in the project root for albums the user already owns. This file contains entries with `artist`, `album_title`, and a `source` array that can include `"discogs"` (physical/vinyl), `"local"` (digital library), or both.

**Matching:** Normalize both the artist name and album title (lowercase, strip leading articles, remove punctuation) before comparing. The collection data may use slightly different naming than your research — e.g., "Trane" vs "John Coltrane", or "A Love Supreme" vs "A Love Supreme (Deluxe Edition)". Use fuzzy matching: strip parenthetical suffixes and edition markers when comparing.

**Visual indicators on album cards:**
- 🔈 vinyl icon + "In collection" badge for `source: ["discogs"]` — physical only
- 💻 digital icon + "In library" badge for `source: ["local"]` — digital only
- 🔈💻 both icons + "Owned" badge for `source: ["discogs", "local"]` or `["local", "discogs"]` — both formats
- No indicator for albums not in the collection — instead, show a "Want" checkbox (see below)

**"To Buy" checkboxes on unowned albums:**
- Albums *not* found in `collection.json` should display a small checkbox with the label "Want" in the metadata row
- Clicking the checkbox saves the album to a shared `toBuy` list in `localStorage` under the key `discography-to-buy`
- The stored format is a JSON array of objects: `{artist, album, year, label, addedFrom}` where `addedFrom` is the artist page filename
- If the checkbox is unchecked, remove that entry from localStorage
- On page load, check localStorage and pre-check any albums already on the list
- Style the checkbox label in JetBrains Mono at 0.68rem, using `var(--text-secondary)` — when checked, the label text changes to "Want ✓" and uses `var(--amber)`
- The index page reads this same localStorage key to display a consolidated "To Buy" list across all artists

**Badge styling:**
- Small pill-shaped badge in the album card's metadata row (next to year/label)
- Use a muted gold/amber background (`rgba(200, 170, 80, 0.15)`) with matching text color so it integrates with the design language without being loud
- The badge should feel like a quiet personal annotation, not a primary UI element

**Collection stats:** Add a line in the page header, after the album count, like: "You own 18 of 28 albums (12 vinyl, 14 digital)"

If `collection.json` is not found, skip this step silently — the page should still work fine without it.

### 6. Handle Edge Cases

- **Very prolific artists** (e.g., Miles Davis, David Bowie, Frank Zappa): Focus on the most significant releases. Mention total count and note that you're highlighting the essential catalog. Offer to show the complete list if the user wants.
- **Artists with complex catalogs** (posthumous releases, disputed albums): Note any caveats. Stick to widely recognized official releases.
- **Obscure artists with little info**: Be honest about gaps. Present what you find and note where information is limited.
- **Classical musicians**: Discography structure differs — organize by composer or period rather than "studio vs. live" if that makes more sense.

## Example Output Structure

For a query like "Show me John Coltrane's discography":

```
Header: John Coltrane (1926-1967)
Subtitle: Saxophonist, composer — one of the most influential jazz musicians of the 20th century
Stats: 25+ leader albums | Active: 1957-1967 as leader

[Studio Albums - chronological grid of album cards]
  → Blue Train (1958) - Blue Note
    "Coltrane's only album as leader for Blue Note, and one of hard bop's
     defining statements. The title track's three-horn frontline set a template..."
    Personnel: Lee Morgan (tp), Curtis Fuller (tb), Kenny Drew (p)...
    Tags: hard bop, modal jazz

  → Giant Steps (1960) - Atlantic
    ...

[Live Albums]
  → Live at the Village Vanguard (1962) - Impulse!
    ...
```

## GitHub Pages Collection Structure

These discography pages are designed to accumulate into a browsable collection hosted via GitHub Pages. Follow these conventions so every page feels like part of the same site:

### File naming
Use lowercase, hyphenated artist names: `john-coltrane.html`, `radiohead.html`, `mdou-moctar.html`. For artists with common name collisions, append a disambiguator: `john-coltrane.html` not `coltrane.html`.

### Consistent structure
Every page must share:
- The same Google Fonts imports (Playfair Display, Source Serif 4, JetBrains Mono)
- The same CSS variable scheme (dark background, cream/amber palette for the base theme — genre-specific accent colors are fine)
- A "← Back to Collection" link in the header that points to `index.html`
- The same footer structure with a source note

### Navigation link
Add this element near the top of every artist page, before the header:
```html
<a href="index.html" class="back-link">← Collection</a>
```
Style it as a small, fixed-position or top-of-page link using JetBrains Mono at 0.75rem.

### Index page
When the user asks for it (or after generating several artist pages), generate an `index.html` for the collection root. It should:
- List all artist pages as cards with artist name, active years, genre, and album count
- Use the same dark/amber design language
- Be a simple static HTML page that links to each artist's file
- Sort alphabetically by default, with an option to browse by genre

The user manages the repo and pushes files — the skill just generates HTML that's ready to drop in.

### Collection data
`collection.json` lives in the project root alongside the HTML files. It is generated separately (from Discogs API + iTunes XML) and should not be modified by this skill. The skill reads it during step 5 to annotate album cards with ownership indicators.

## What NOT To Do

- Don't just dump a bulleted list of album names and years. The whole point is the presentation.
- Don't fabricate album details you're unsure about — search first, and note gaps honestly.
- Don't include every bootleg and unauthorized release unless asked.
- Don't make the track listings dominate — they're supporting detail, not the main event.
- Don't use generic AI artifact styling. This should feel curated and intentional.
