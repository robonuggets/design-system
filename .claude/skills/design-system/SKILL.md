---
name: design-system
description: Generate a full design system reference page AND a 1-page A4 brand book PDF from any site, brand, or reference the user provides. Extracts palette, type, principles, and wordmarks, then produces both artifacts. Triggers on "design system", "brand book", "make design assets", "extract design", "brand page", "one page brand".
---

# Design System + Brand Book Generator

Give Claude a reference (site URL, screenshot, brand description, or existing assets) and it produces two artifacts:

1. **`design-system.html`** — a scrollable reference page covering colors, typography, principles, components, icons, and wordmarks. Lives in the project, can be served locally.
2. **`brand-book-a4.html` → `brand-book-a4.pdf`** — a single A4 portrait page fitting the brand topline onto one shareable poster, print-ready.

Both are self-contained HTML files (Google Fonts CDN + inline CSS + inline SVG, no build step). The PDF is rendered via headless Edge/Chrome.

---

## Workflow

1. **Extract from the reference first.** If the user gives a URL, WebFetch it. If a screenshot, Read it. If an existing site, look at it. Pull out what you can see:
   - Hex colors (primary, secondary, any accents)
   - Fonts (check Google Fonts links in the source)
   - Tagline / value prop
   - Principles if documented
   - Logo / mark as inline SVG
2. **Ask only for gaps.** Don't re-ask what you can already see. Confirm the extracted palette + fonts in one short message, then ask for whatever's missing from the list below.
3. **Generate both files** — `design-system.html` and `brand-book-a4.html` into a `design/` folder at the project root (or wherever the user specifies).
4. **Render the PDF** via Edge/Chrome headless.
5. **Show the user.** Send the PDF; if there's a local server, share the design-system.html URL.
6. **Iterate.** Adjust on feedback — restyle, fill empty space, swap wordmark treatment, etc.

---

## Inputs Needed (ask only for what you can't extract)

- **Brand name(s)** — single brand, or a family (parent + product + studio)
- **Primary color** — the one that dominates (hex)
- **Secondary + tertiary colors** — supporting + success/positive (hex each)
- **Typography** — display font + body font + optional mono (Google Fonts names)
- **Tagline** — one short line describing what the brand does
- **Principles** — 4–6 design rules
- **Team or product set** (optional) — 4–8 items with a color + role each
- **Logo mark** — inline SVG or path. If the user has one, use it exactly as given. Never invent or redraw a logo.

**Never invent brand details.** Colors, fonts, names, taglines must come from the user or the reference. If something's missing and not inferable, ask.

---

## Output 1: `design-system.html` (scrollable reference page)

A single HTML page. Sections in order:

1. **Header** — brand lockup + tagline + version/date
2. **Color Hierarchy** — Primary / Secondary / Tertiary with hex + usage note each
3. **Extended Palette** (optional) — status colors, category colors
4. **Typography** — display sample, body sample, mono sample, weight ladder
5. **Principles** — bulleted rules
6. **Components** (optional) — buttons, cards, badges
7. **Icons** — inline SVG samples or custom icon set
8. **Wordmarks / Lockups** — 2–3 name treatments
9. **Footer** — file path, version, note on how this relates to any DESIGN.md

Default style tokens (override per brand):
- Dark theme: background `#000`, surface `#0a0a0a`, border `#1a1a1a`, text `#e0e0e0`, muted `#888`
- Accent = brand primary
- Section labels: monospace uppercase, 10px, 2px tracking, primary color
- Cards: 1px border, 8px radius, ~24px padding

**The template bends to the brand.** If the reference is light, use light surfaces. If the brand uses different fonts, honor those. If the brand uses gradients, curves, or a different radius language, match it. The defaults above are a starting point — the user's reference is the source of truth.

---

## Output 2: `brand-book-a4.html` → `brand-book-a4.pdf`

A **single A4 portrait page** (210mm × 297mm) — the brand topline in one glance.

### Required print CSS

```css
@page { size: A4; margin: 0; }
* { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
.page { width: 210mm; height: 297mm; padding: 18mm 16mm 14mm; }
```

### Layout (top to bottom, single A4 page)

1. **Head** — brand lockup (mark + wordmark) on the left, version + tagline on the right
2. **Color Hierarchy** — 3 swatch tiles in a row (Primary / Secondary / Tertiary)
3. **Two-column middle** — Typography card (left) + Principles card (right)
4. **Team / Product row** (optional) — 6 small tiles: icon or color dot + name + hex + role
5. **Wordmarks · Lockups** — 3 tiles showing name treatments
6. **Footer** — file path + any cross-reference note

Section headings: monospace uppercase, 10px, 2px tracking, primary color.

### Rendering to PDF (headless browser)

Windows (Edge):
```bash
"/c/Program Files (x86)/Microsoft/Edge/Application/msedge.exe" \
  --headless --disable-gpu --no-pdf-header-footer \
  --print-to-pdf="brand-book-a4.pdf" \
  "file:///absolute/path/to/brand-book-a4.html"
```

macOS (Chrome):
```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu --no-pdf-header-footer \
  --print-to-pdf="brand-book-a4.pdf" \
  "file://$PWD/brand-book-a4.html"
```

Linux (Chromium):
```bash
chromium --headless --disable-gpu --no-pdf-header-footer \
  --print-to-pdf="brand-book-a4.pdf" \
  "file://$PWD/brand-book-a4.html"
```

---

## Wordmark Patterns

Three lockup styles to offer (show all three on the brand book page):

1. **Mark + wordmark** — icon next to uppercase name, 800 weight, 4px tracking. Used for the primary product brand.
2. **Split-weight lockup** — `PART1PART2` where PART1 is 800 weight and PART2 is 300 weight in muted color. Used for umbrella/group names.
3. **Accent lockup** — `PART1PART2` where PART1 is colored with the primary and PART2 is default text color, same weight. Used for studios/sub-brands.

---

## Rules

- **Extract before asking.** Use the reference to pre-fill whatever you can.
- **Never invent brand details.** No made-up colors, fonts, names, or taglines.
- **Never redraw logos.** Use the exact SVG the user provides.
- **Fit on one A4 page.** If content overflows, tighten padding or drop a section — never spill to page 2.
- **Self-contained HTML.** Google Fonts CDN + inline CSS + inline SVG. No build step.
- **Template bends to the brand.** Defaults are a starting point; the user's reference wins.
- **No emojis in output.** SVGs only.
- **Render the PDF.** Don't just hand over HTML — finish the job with Edge/Chrome headless.

---

## Examples

See `examples/brand-book-a4.html` and `examples/design-system.html` for complete filled outputs (fictional brand "Helio Studio"). Copy the structure, replace the tokens, keep the section order.
