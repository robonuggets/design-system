---
name: design-system
description: Extract a brand's design from any reference the user provides (site URL, screenshot, or existing assets) and generate a full design system reference page AND a 1-page A4 brand book PDF. Triggers on "design system", "brand book", "make design assets", "extract design", "brand page", "one page brand".
---

# Design System + Brand Book Generator

Give Claude a reference (site URL, screenshot, or existing brand assets) and it produces two artifacts matching that brand:

1. **`design-system.html`** — a scrollable reference page covering colors, typography, principles, components, icons, and wordmarks.
2. **`brand-book-a4.html` → `brand-book-a4.pdf`** — a single A4 portrait page fitting the brand topline onto one shareable poster, print-ready.

Both outputs are self-contained HTML (Google Fonts CDN + inline CSS + inline SVG, no build step). The PDF is rendered via headless Edge/Chrome.

**This skill has no opinions about fonts, colors, or themes.** Everything comes from the user's reference. If there's no reference, ask for one — don't invent.

---

## Workflow

1. **Get the reference.** URL, screenshot, existing site, or a description of the brand. If the user hasn't provided one, ask.
2. **Extract.** If URL → WebFetch it and inspect the HTML/CSS. If screenshot → Read it and identify colors/fonts visually. Pull out:
   - Hex colors (primary, secondary, any accents the brand actually uses)
   - Fonts (check `<link>` tags, CSS font-family declarations, or visual identification)
   - Tagline / value prop
   - Any documented principles
   - Logo / mark (grab as inline SVG if the reference has one)
   - Theme direction (light / dark / which surfaces are used)
3. **Confirm extraction.** Short message listing what you found. Ask only for the gaps.
4. **Generate both files** into a `design/` folder.
5. **Render the PDF** via headless Edge/Chrome.
6. **Show the user.** Send the PDF and the design-system.html.
7. **Iterate** on feedback.

---

## What to ask for (only if not extractable)

- **Brand name(s)** — single brand or a family (parent + product + studio)
- **Tagline** — one short line
- **Principles** — 4–6 design rules (if the brand doesn't publish any, skip the section rather than invent)
- **Team or product set** (optional) — 4–8 items with a color + role each
- **Any colors/fonts you can't see** in the reference

**Never invent brand details.** Colors, fonts, names, taglines, logos, and principles all come from the user or the reference. If a section has no source material, drop it — don't fill it with made-up content.

**Never redraw logos.** Use the exact SVG from the reference. If the logo is a raster image, ask the user for the SVG or leave a placeholder and note it.

---

## Output 1: `design-system.html` (scrollable reference page)

Sections in this order (drop any for which there's no source material):

1. **Header** — brand lockup + tagline + version/date
2. **Color Hierarchy** — the primary, secondary, tertiary colors the brand actually uses, with hex + usage note
3. **Extended Palette** — any additional brand colors (status, category, etc)
4. **Typography** — display, body, mono samples using the brand's actual fonts; weight ladder
5. **Principles** — whatever the brand documents
6. **Components** — buttons, cards, badges styled in the brand's language
7. **Icons** — whatever icon language the brand uses (inline SVG, pixel art, line, filled, etc)
8. **Wordmarks / Lockups** — 2–3 name treatments based on the brand's mark + wordmark
9. **Footer** — file path, version, any cross-reference note

Match the brand's surfaces (light vs dark), radius, border treatment, and type hierarchy. If the brand is rounded and soft, don't default to sharp. If the brand is maximalist, don't make it minimal.

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

1. **Head** — brand lockup on the left, version + tagline on the right
2. **Color Hierarchy** — 3 swatch tiles (Primary / Secondary / Tertiary) using the brand's actual colors
3. **Two-column middle** — Typography card (brand's fonts) + Principles card (brand's documented principles)
4. **Team / Product row** (optional) — 6 small tiles if the brand has a product family
5. **Wordmarks · Lockups** — 3 tiles showing name treatments
6. **Footer** — file path + cross-reference note

### Rendering to PDF

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

Three lockup styles you can offer (adapt to the brand's tone):

1. **Mark + wordmark** — icon next to the brand name. Weight/tracking matches the brand's own wordmark.
2. **Split-weight lockup** — `PART1PART2` where one half is heavy and the other half is light. Useful for umbrella/group names.
3. **Accent lockup** — `PART1PART2` where one half is colored with the brand primary. Useful for studios/sub-brands.

Only include lockup variants that make sense for the brand. A single-word brand doesn't need a split-weight treatment.

---

## Skeleton Template

See `examples/template.html` for a structural skeleton with placeholder tokens (`{{PRIMARY}}`, `{{DISPLAY_FONT}}`, etc). Copy it, swap the tokens with values from your extraction, add or drop sections to match the brand.

---

## Rules

- **Extract before asking.** Use the reference to pre-fill whatever you can.
- **Never invent brand details.** No made-up colors, fonts, names, taglines, principles, or logos.
- **Never redraw logos.** Use the exact SVG the user provides.
- **Drop sections you can't source.** Better to skip a section than fill it with fiction.
- **Match the brand, don't impose a style.** The template is structural — visual language comes from the reference.
- **Fit on one A4 page.** If content overflows, tighten padding or drop a section.
- **Self-contained HTML.** Google Fonts CDN + inline CSS + inline SVG. No build step.
- **Render the PDF.** Don't just hand over HTML — finish the job with headless Edge/Chrome.
