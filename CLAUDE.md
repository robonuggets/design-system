# Design System Toolkit

Give Claude a brand or site reference → get back a full design system reference page AND a 1-page A4 brand book PDF.

## What this toolkit does

1. Extracts colors, fonts, principles from any reference you provide (URL, screenshot, existing site, or description)
2. Generates `design/design-system.html` — a scrollable reference page
3. Generates `design/brand-book-a4.html` — a single A4 portrait page
4. Renders `design/brand-book-a4.pdf` via headless Edge/Chrome

The skill is at `.claude/skills/design-system/SKILL.md`. Read it first, then follow the workflow there.

## Rules

- **Extract from the reference first.** Don't ask the user what you can see yourself.
- **Never invent brand details.** Colors, fonts, names, taglines come from the user or the reference.
- **Never redraw logos.** Use the exact SVG the user provides.
- **Template bends to the brand.** Dark is the default, but match the reference if it differs.
- **Fit on one A4 page.** If content overflows, tighten or drop a section.
- **Render the PDF.** Don't hand over HTML without the rendered output.
