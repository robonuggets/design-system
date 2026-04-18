# Design System Toolkit

Give Claude a brand reference, get a complete design system page and a print-ready A4 brand book in one shot.

## What you get

1. **`design-system.html`** — a scrollable reference page covering colors, typography, principles, components, icons, and wordmarks.
2. **`brand-book-a4.html` + `brand-book-a4.pdf`** — a single A4 portrait page fitting the brand topline onto one shareable poster.

Both outputs are self-contained HTML (Google Fonts CDN + inline CSS + inline SVG). No build step. The PDF is rendered via headless Edge/Chrome.

**The toolkit has no opinions about fonts, colors, or themes.** Everything comes from the reference you give Claude — the extracted brand is what you get back.

## Quick start

```bash
git clone https://github.com/robonuggets/design-system
```

Then in Claude Code, add this as a working directory (or drop the `.claude/skills/design-system/` folder into your project's `.claude/skills/`). Ask Claude:

> Make a design system and brand book from [site URL / screenshot / description]

Claude will extract what it can, ask for any gaps, generate both files, and render the PDF.

## What's included

```
design-system/
├── .claude/
│   └── skills/
│       └── design-system/
│           ├── SKILL.md                       # The skill definition
│           └── examples/
│               └── template.html              # Structural skeleton with {{TOKENS}} to fill
├── CLAUDE.md
└── README.md
```

## How it works

1. You give Claude a reference (URL, screenshot, existing site, or brand description).
2. Claude extracts colors, fonts, tagline, principles, and logo SVG.
3. Claude confirms what was extracted and asks only for the gaps.
4. Claude writes both HTML files into a `design/` folder.
5. Claude renders `brand-book-a4.pdf` via headless Edge/Chrome.
6. You iterate — restyle, fill empty space, swap wordmark treatments.

## Example prompts

```
Make a design system and brand book for linear.app
```

```
Here's a screenshot of my product. Extract the brand and build the design system + A4 brand book.
```

```
My brand is "Acme" — primary #ff4444, secondary #666, fonts Inter + JetBrains Mono, tagline "Ship faster, break less". Build the assets.
```

## Rendering the PDF

The skill walks Claude through rendering via headless browser. Manual command (Windows):

```bash
"/c/Program Files (x86)/Microsoft/Edge/Application/msedge.exe" \
  --headless --disable-gpu --no-pdf-header-footer \
  --print-to-pdf="brand-book-a4.pdf" \
  "file:///absolute/path/to/brand-book-a4.html"
```

macOS/Linux equivalents are in `SKILL.md`.

## License

MIT
