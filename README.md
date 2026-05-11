# elementor-json

Claude Code plugin for turning designs into production-grade Elementor templates for WordPress, with a built-in auditor that catches the failure modes generators are prone to (hallucinated HTML widgets, layout values dumped into `custom_css` instead of JSON settings).

## What's in the box

**Two skills + one slash command**, designed to work together:

### `elementor-json` — the generator skill

Auto-triggers when you ask Claude to turn a screenshot, Figma file, or HTML mockup into an Elementor template that imports cleanly via **Elementor → Tools → Import / Export**.

- **The widgets-first rule.** Inside an HTML widget, content is opaque to Elementor's controls panel — clients can't edit headings, swap images, or tweak colors without learning HTML. The skill enforces a strict decision tree: native widget first, then native widgets + `custom_css`, and only then (with written justification) an HTML widget for the decorative shell.
- **The JSON-settings rule.** Layout values like padding, font-size, and flex direction belong in JSON settings — not in `custom_css`. The skill ships activation-flag rules (`typography_typography: "custom"`, `background_background: "classic"`, `border_border: "solid"`, etc.) so settings don't silently no-op.
- **56-widget catalog.** Every common Elementor widget, free and Pro, documented with a real-world pruned template + a "when to use" line + gotcha notes. Split across four reference files so the always-loaded SKILL.md stays lean.

### `elementor-verify` — the strict auditor

Fires automatically (via the elementor-json workflow) whenever the generator wrote an HTML widget — and any time the user invokes `/verify-elementor` manually. Always runs in a **fresh subagent** so the auditor has no carry-over context from the generator's prompt — that's what lets it catch hallucinations the writer can't see.

- Walks every `html` widget through a 4-step decision tree (single native widget? combination? widgets + custom_css? truly outside Elementor's control surface?)
- Audits every `custom_css` and `_custom_css` block for layout values that belong in JSON settings
- Fixes violations **in-place** using Edit, preserving IDs, advanced settings (`_padding`, `_margin`, `_css_classes`), and original design intent
- Returns a structured PASS/FAIL report

### `/verify-elementor` — the slash command

Run after generating an Elementor template to dispatch a fresh-subagent audit pass against the latest `.json` in your cwd:

```
/verify-elementor                  # newest *.json with Elementor markers
/verify-elementor hero             # newest *.json matching "hero" in filename
```

## Install

```
/plugin marketplace add aleem2z/elementor-json-skill
/plugin install elementor-json@aleem2z-skills
```

Then just ask Claude to build an Elementor template — the skill auto-triggers.

## Manual install

```bash
git clone https://github.com/aleem2z/elementor-json-skill.git ~/.claude/skills/elementor-json-skill
ln -s ~/.claude/skills/elementor-json-skill/skills/elementor-json ~/.claude/skills/elementor-json
ln -s ~/.claude/skills/elementor-json-skill/skills/elementor-verify ~/.claude/skills/elementor-verify
ln -s ~/.claude/skills/elementor-json-skill/commands/verify-elementor.md ~/.claude/commands/verify-elementor.md
```

## Layout

```
skills/
├── elementor-json/
│   ├── SKILL.md                          # Rules, workflow, top-level shape (always-loaded)
│   └── references/                       # Loaded on-demand
│       ├── containers.md                 # Container settings, responsive, hierarchy
│       ├── widgets.md                    # 20 free widgets — heading, button, image, accordion, counter, alert, spacer, progress, testimonial, …
│       ├── pro-widgets.md                # 24 Pro widgets — form, call-to-action, price-table, flip-box, slides, lottie, animated-headline, hotspot, reviews, …
│       ├── pro-widgets-dynamic.md        # 5 query-driven widgets — loop-grid, loop-carousel, posts, portfolio, taxonomy-filter
│       ├── pro-widgets-navigation.md     # 7 navigation/utility widgets — nav-menu, mega-menu, off-canvas, table-of-contents, search, login, countdown
│       ├── custom-css.md                 # When custom_css is justified + recipes (pulse dots, gradient underlines, icon-list restyling, brute-force flex, grid-template-areas, receipt shells, timeline connectors)
│       ├── patterns.md                   # Section recipes (eyebrow chips, accent underlines, hero variants, stat strips, hover-lift cards, gradient CTA, founder blockquote, signature row)
│       └── gotchas.md                    # Activation flags, ID conventions, FAQ underline fix, button hover-text invisibility, mobile flex reordering
├── elementor-verify/
│   └── SKILL.md                          # The auditor — decision trees, high-frequency mistakes, output format
commands/
└── verify-elementor.md                   # /verify-elementor slash command
```

SKILL.md files stay lean (always loaded into any triggered conversation). Reference files are pulled in only when the agent is actually building or auditing that thing.

## Widget coverage

**Free Elementor (20):** heading, text-editor, button, image, icon, icon-box, icon-list, accordion, counter, star-rating, divider, gallery, video, html, progress, testimonial, spacer, menu-anchor, alert, read-more

**Pro core (24):** form, nested-accordion, call-to-action, image-box, animated-headline, price-table, price-list, flip-box, slides, media-carousel, lottie, google_maps, nested-tabs, nested-carousel, progress-tracker, social-icons, hotspot, image-carousel, reviews, e-paragraph, code-highlight, blockquote, share-buttons, template

**Pro dynamic (5):** loop-grid, loop-carousel, posts, portfolio, taxonomy-filter

**Pro navigation/utility (7):** nav-menu, mega-menu, off-canvas, table-of-contents, search, login, countdown

**Total: 56 widgets.** Every entry has a pruned real-world JSON template + when-to-use note + paired-structure rules where applicable (nested-accordion / nested-tabs / nested-carousel `items[]` count must match `isLocked: true` container count; loop-grid / loop-carousel / posts / portfolio need a separate loop-item template ID + query params; off-canvas trigger button + `isLocked` body container; etc.).

## Contributing

Found a widget setting that's documented wrong? A breakpoint quirk? Open an issue or PR. Ground-truth comes from actual `.json` files exported from Elementor — when in doubt, export a template you've configured in the editor and check what keys/values Elementor itself writes.

## License

MIT
