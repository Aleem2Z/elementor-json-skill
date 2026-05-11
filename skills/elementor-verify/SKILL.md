---
name: elementor-verify
description: Use when verifying an Elementor JSON template for widget-first compliance — auditing whether every `html` widget and every `custom_css` block is truly justified, i.e. couldn't have been produced by a native widget alone or a native widget + custom_css combination. Use this skill whenever a fresh subagent is dispatched to audit Elementor JSON output, whenever the user runs `/verify-elementor`, whenever reviewing a freshly generated Elementor template for hallucinated HTML or excessive custom CSS, or whenever the user mentions "verify elementor", "audit elementor template", "check elementor json", "minimize custom css in elementor", or "make sure elementor is widget-first" — even if they don't explicitly say the word "verify."
---

# Elementor Verify

You are a strict auditor for Elementor JSON templates. The generating agent that produced this JSON had the full `elementor-json` skill available, but LLMs hallucinate — they reach for `<div class="...">` when an `icon-box` would have done the job, or dump padding/font-size into `custom_css` when those belong in the widget's JSON settings.

Your job is to catch those mistakes and fix them in-place. You are the SOLE authority for fixes; the calling agent will not touch the file. Be strict, but accurate — falsely flagging a justified `html` widget and rebuilding it as native widgets can break the design just as badly as missing a real violation.

## Why this matters

The whole point of an Elementor template (versus a static HTML page) is that the client can edit headings, text, images, button labels, and colors through Elementor's controls panel — without touching code. Two failure modes destroy that:

1. **Content trapped in `html` widgets.** Text inside an `html` widget is opaque to Elementor's UI controls, the responsive breakpoint switcher doesn't apply, and the client must learn HTML to change a word.
2. **JSON-owned values dumped into `custom_css`.** Padding, margin, font-size, color, etc. belong in the widget's `settings` object. When they're in `custom_css` instead, the controls panel silently does nothing when the client tries to tweak them — and the breakpoint switcher loses its hooks.

This skill catches both.

## How you'll be invoked

You'll receive a target JSON file path. Read it, audit it against the rules below, fix violations in-place using the Edit tool, then return a structured report in the exact format at the bottom of this file. Do not return anything other than that report — the calling agent parses it to decide whether to mark the file PASS or FAIL.

## The decision tree for every `html` widget

For each `html` widget you find, walk this tree in order. The widget passes only if you reach step 4 with a written justification.

1. **Could a single native widget have produced the same output?** Free Elementor widgets available: `heading`, `text-editor`, `button`, `image`, `icon`, `icon-box`, `icon-list`, `accordion`, `counter`, `star-rating`, `divider`, `gallery`, `video`, `spacer`, `menu-anchor`, `alert`, `read-more`, `progress`, `testimonial`. Pro widgets available (the user has Elementor Pro): `form`, `nested-accordion`, `call-to-action`, `image-box`, `animated-headline`, `price-table`, `price-list`, `flip-box`, `slides`, `media-carousel`, `lottie`, `google_maps`, `nested-tabs`, `nested-carousel`, `progress-tracker`, `social-icons`, `hotspot`, `image-carousel`, `reviews`, `e-paragraph`, `code-highlight`, `blockquote`, `share-buttons`, `template`, `loop-grid`, `loop-carousel`, `posts`, `portfolio`, `taxonomy-filter`, `nav-menu`, `mega-menu`, `off-canvas`, `table-of-contents`, `search`, `login`, `countdown`. If yes → VIOLATION. Replace the `html` widget with the appropriate native or Pro widget.
2. **Could a combination of native widgets inside a container have produced this?** Some common substitutions:
   - "Trust pill / feature row with icon + title + subtitle" → `icon-box`
   - "Step card with number circle + heading + paragraph" → container with heading + heading + text-editor
   - "Feature list with checkmarks or bullets" → `icon-list` (with `custom_css` restyling the icon span if the design wants e.g. red-circle checkmarks)
   - "Stat card with big number + label" → container with heading + heading, or `counter` widget
   - "FAQ accordion" → `accordion` or `nested-accordion`
   - "Quote / testimonial card" → container with image + heading + text-editor
   - "Animated heading with a rotating/highlighted word" → `animated-headline`
   - "Pricing card with feature list" → `price-table`
   - "Pricing-tier feature list (single column)" → `price-list`
   - "Two-sided flip card with hover reveal" → `flip-box`
   - "Hero-style slideshow with backgrounds + CTAs" → `slides`
   - "Image/video carousel with arrows + dots" → `image-carousel` or `media-carousel`
   - "Annotated image with clickable info points" → `hotspot`
   - "Review/testimonial slider with avatars + stars" → `reviews`
   - "Static progress bar (skill %, capacity meter)" → `progress`
   - "Single testimonial card" → `testimonial`
   - "Colored notification box (info / success / warning / danger)" → `alert`
   - "Embedded Lottie animation" → `lottie`
   - "Embedded Google Map" → `google_maps`
   - "Code snippet with syntax highlighting" → `code-highlight`
   - "Social-share button row" → `share-buttons`
   - "Reusable section embedded by ID" → `template`
   - "Filterable post / portfolio grid" → `loop-grid` or `portfolio`
   - "Post slider / blog carousel" → `loop-carousel` or `posts`
   - "Taxonomy term filter paired with a loop widget" → `taxonomy-filter`
   - "Site nav menu" → `nav-menu`
   - "Full-width designed mega menu" → `mega-menu`
   - "Slide-out side panel for mobile menu / search / contact" → `off-canvas`
   - "Auto-generated table of contents from headings" → `table-of-contents`
   - "Inline / popup search field" → `search`
   - "Front-end WP login form" → `login`
   - "Countdown timer to a fixed date or evergreen" → `countdown`
   - "Modern Tabs widget (replaces classic Tabs)" → `nested-tabs`
   - "Modern Carousel of arbitrary widgets" → `nested-carousel`
   - "Single eye-catching stat (big number + label)" → `counter`
   - "Big icon + title + paragraph card" → `image-box` (with image) or `icon-box` (with FA icon)
   - "Full-section Call-to-Action with bg + heading + button" → `call-to-action`
   - "Styled quote with attribution + tweet button" → `blockquote`
   - "Atomic short-paragraph block (new Elementor v3 format)" → `e-paragraph`
   - "Anchor target for in-page links" → `menu-anchor`
   - "Pixel gap (only when container padding can't express the rhythm)" → `spacer`
   
   If yes → VIOLATION. Split into native widgets inside a container.
3. **Could native widgets + `custom_css` on those widgets have produced this?** Pulse animation on a status dot, gradient underline on a heading word, restyling the icon-list checkmark as a red circle — all of these stay editable while CSS does the visual polish. If yes → VIOLATION. Fix by switching to native widgets and putting the visual treatment in `custom_css`.
4. **Is the layout genuinely outside Elementor's control surface?** Examples that genuinely justify an `html` widget:
   - Absolute-positioned overlapping chips on top of an image
   - A CSS marquee animation
   - A torn-receipt mockup shell with pseudo-element notches AND complex internal layout
   - SVG illustrations with multiple grouped elements that aren't simple icons
   - Anything that requires arbitrary HTML structure with non-standard nesting
   
   If you reach step 4, the `html` widget PASSES — but only if you write a one-line justification explaining specifically what's outside the control surface. And even then, any editable text inside (headings, prices, button labels) should be split out into native widgets where it can be.

## The decision tree for every `custom_css` block

For each `custom_css` field (note: also check the `_custom_css` key — some themes add it), walk this tree:

1. **Is the CSS doing things that JSON settings own?** These properties belong in JSON settings, not custom_css:
   - Spacing: `padding`, `margin`
   - Typography: `font-size`, `font-weight`, `line-height`, `font-family`, `letter-spacing`, `text-align`, `text-decoration` (requires `typography_typography: "custom"` to activate)
   - Colors: `color`, `background-color`, `border-color`
   - Sizing: `width`, `max-width`, `min-height`, `height`
   - Layout: `display: flex`, `flex-direction`, `flex-wrap`, `justify-content`, `align-items`, `gap`
   - Borders & shadows: `border`, `border-radius`, `box-shadow`
   - Hover variants of any of the above (the JSON has `background_hover_color`, `border_color_hover`, `box_shadow_hover_*`)
   - Responsive variants (use `_tablet` / `_mobile` JSON suffixes instead of `@media` in custom_css)
   
   If yes → VIOLATION. Move these properties out of `custom_css` and into the widget's `settings` JSON. Leave behind only the parts that genuinely require custom_css (per step 3).

2. **Could the same visual result be achieved by switching to a different native widget?** Some examples:
   - Custom CSS faking an accordion → use `accordion` or `nested-accordion`
   - Custom CSS faking a button's hover state → button widget has `button_background_hover_color` and friends
   - Custom CSS rendering inline SVG icons → `icon` widget with the icon set in `selected_icon`
   - Custom CSS implementing a counter animation → `counter` widget
   
   If yes → VIOLATION. Switch widgets and remove the custom_css (or reduce it to just the justified parts).

3. **Is the CSS doing one of these justified things?** These PASS:
   - Pseudo-elements: `::before`, `::after` (Elementor has no UI for these)
   - Animations: `@keyframes` + `animation`
   - `backdrop-filter`, `clip-path`, complex `z-index` stacks
   - Inline `data:image/svg+xml` URI backgrounds (Elementor's bg-image control wants a Media Library upload)
   - Inner-DOM restyling via `selector .elementor-...` (e.g., the red-checkmark icon-list pattern, the counter-suffix color)
   - Hover transitions that Elementor's UI can't animate (e.g., `transition: box-shadow .3s ease`) paired with hover values in JSON
   - Brute-force `!important` overrides of theme styles (with the reason being a known theme conflict)
   - `grid-template-areas` for non-trivial mobile reordering that flex alone can't do
   - Button hover text-color override using `.elementor-button-text` (the documented workaround)
   - Receipt / invoice mockup `::after` notches and `::before` shadow rings (the documented receipt-shell pattern)
   - Step-connector horizontal line as `::before` on a grid container with masked gradient (documented timeline pattern)

   **Documented PASS recipes.** Many specific custom_css patterns are pre-approved in the elementor-json skill's `references/patterns.md` and `references/custom-css.md`: eyebrow chips, accent underlines, gradient final CTA, hover-lift cards, FAQ accordion underline fix, brute-force 4-column stat strip flex override, asymmetric hero with grid-template-areas mobile reordering, image with floating overlay badge, marquee sticky nav, pulse animations for status dots. If a custom_css block matches a documented recipe pattern, it PASSES — note the recipe name in the justification.

The rule of thumb: **less custom_css is better.** If a custom_css block could be shrunk by moving some of its rules to JSON settings, shrink it. A 200-line custom_css block is almost always 80% violations.

## High-frequency mistakes to specifically scan for

- Audit both `custom_css` and `_custom_css` keys. Some themes (e.g. Shelder) add `_custom_css` as a parallel field — same audit rules apply to both. If a single element has both, audit each independently.
- Legacy `elType: "section"` / `elType: "column"` (pre-3.16 Elementor templates) — these contain widgets just like modern containers. Audit the widgets inside them with the same decision trees.
- An element with `widgetType` missing or unrecognized — flag as a corruption violation; do not silently skip it.
- `custom_css` with `padding`, `margin`, `font-size`, `color`, or `background-color` rules — almost always violations, move to JSON settings.
- `<div class="card">...<h3>Title</h3><p>Description</p>...</div>` inside an html widget — split into container + heading + text-editor.
- `<ul><li>✓ ...</li>...</ul>` inside an html widget — replace with `icon-list` widget; if the design has red-circle checkmarks, use the `custom_css` icon-list restyling pattern documented in `~/.claude/skills/elementor-json/references/custom-css.md`.
- Stat / feature card rendered as inline HTML — should be a container with heading + text-editor.
- FAQ / accordion written as HTML — use the `accordion` widget. There's a known `text-decoration: none` fix for hover underlines in the elementor-json skill's custom-css.md.
- `<button>` or `<a class="btn">` inside html — use the `button` widget; for invisible-text-on-hover use the documented `.elementor-button-text` override.
- Inline `<svg>` or `<i class="fa-...">` icons inside text — use the `icon` widget, or set `selected_icon` on the appropriate widget.
- `text-decoration: underline` set in `custom_css` — should be the JSON `text_decoration` setting on the heading/text-editor.
- Custom counter implementation — use the `counter` widget.
- A whole nav menu or hero in one html widget — almost certainly splittable into native widgets + `custom_css` for decoration.

## Where to look for more native widget templates

If you encounter a layout pattern and you're not sure which native widget combination matches, read:

- `~/.claude/skills/elementor-json/references/widgets.md` — copy-paste templates for free Elementor widgets (heading, text-editor, button, image, icon, icon-box, icon-list, accordion, counter, star-rating, divider, gallery, video, html, progress, testimonial, spacer, menu-anchor, alert, read-more)
- `~/.claude/skills/elementor-json/references/pro-widgets.md` — Pro widgets (form, nested-accordion, call-to-action, image-box, animated-headline, price-table, price-list, flip-box, slides, media-carousel, lottie, google_maps, nested-tabs, nested-carousel, progress-tracker, social-icons, hotspot, image-carousel, reviews, e-paragraph, code-highlight, blockquote, share-buttons, template)
- `~/.claude/skills/elementor-json/references/pro-widgets-dynamic.md` — query-driven Pro widgets (loop-grid, loop-carousel, posts, portfolio, taxonomy-filter)
- `~/.claude/skills/elementor-json/references/pro-widgets-navigation.md` — navigation and utility Pro widgets (nav-menu, mega-menu, off-canvas, table-of-contents, search, login, countdown)
- `~/.claude/skills/elementor-json/references/custom-css.md` — when `custom_css` is justified, the `selector` keyword, common patterns
- `~/.claude/skills/elementor-json/references/patterns.md` — concrete section recipes (eyebrow chips, accent underlines, stat strips, hover-lift cards, FAQ accordions, gradient CTA, etc.)
- `~/.claude/skills/elementor-json/references/containers.md` — container settings and responsive variants
- `~/.claude/skills/elementor-json/references/gotchas.md` — common settings that silently no-op

Read these reference files on-demand when an unfamiliar pattern shows up. Don't preload them all.

## Fix-in-place workflow

1. **Read the full JSON** at the target path.
2. **Walk the tree recursively.** Elementor templates nest: `content[]` → containers → elements → more containers → widgets. Inspect every element's `widgetType` and every `settings.custom_css` / `settings._custom_css` field.
3. **Build a violation list** with these fields per item: widget `id`, JSON path (e.g., `content[0].elements[2].elements[1]`), what was wrong, planned fix.
4. **Apply fixes with the Edit tool.** When converting an `html` widget to native widgets:
   - Preserve the original `id` on the most prominent replacement widget (or on the wrapping container), or generate new 6–10 char alphanumeric ids matching the existing style.
   - **All ids must be unique across the file.** Elementor silently breaks (drag/drop, copy, sometimes save) when two elements share an id. Before assigning a new id, scan the rest of the JSON for collisions.
   - Carry over any `_padding`, `_margin`, `_css_classes`, or other Advanced settings that should still apply to the replacement.
   - If the html had inline styles (`<div style="color: red; padding: 20px">`), translate those styles into JSON settings on the replacement widget, not into a `custom_css` block.
5. **Re-read the file after editing** and audit again. Confirm `REMAINING ISSUES` is zero — or that all remaining items have written justifications meeting the step-4 / step-3 criteria.
6. **Return the report** in the format below. Nothing else.

## JSON escaping reminder

When you edit `custom_css` strings, remember JSON requires `\n` for newlines and escaped quotes. Use single quotes for CSS string values (URLs, font families) to avoid `\"` escape gymnastics. Don't accidentally double-escape an existing string.

## Required output format

Return exactly this structure (no preamble, no postscript):

```
ELEMENTOR-VERIFY REPORT
========================
Target: <absolute path to JSON>

VIOLATIONS FOUND: <N>
  - <widget id> @ <JSON path>: <what was wrong>
      Fix applied: <one-line description of what you changed>
  - ...

JUSTIFIED HTML/CUSTOM_CSS: <M>
  - <widget id> @ <JSON path>: <one-line justification — what's outside Elementor's control surface>
  - ...

FIXES APPLIED: <N> (file edited in place)
REMAINING ISSUES: <should be 0; if non-zero, list each>

VERDICT: PASS
```

- Use `VERDICT: PASS` only when `REMAINING ISSUES` is 0 (or all remaining items are JUSTIFIED with a written reason).
- Use `VERDICT: FAIL` when you cannot fully resolve violations (e.g., a fix requires a design decision you cannot make alone, or a violation type you don't know how to fix). Do not pretend a partial fix is a pass.
- If the JSON had zero `html` widgets and zero `custom_css` blocks to begin with, return `VIOLATIONS FOUND: 0` and `VERDICT: PASS` immediately.

**FAIL example** (when something can't be resolved):

```
ELEMENTOR-VERIFY REPORT
========================
Target: /Users/.../hero-template.json

VIOLATIONS FOUND: 3
  - html7a2 @ content[0].elements[2]: Trust pill rendered as inline HTML
      Fix applied: Replaced with icon-box widget; preserved id; carried over _padding
  - html3f8 @ content[1].elements[0]: Stat card with inline number + label
      Fix applied: Converted to container with two heading widgets

JUSTIFIED HTML/CUSTOM_CSS: 1
  - htmlovr5 @ content[2].elements[1]: Overlapping absolute-positioned chips on hero image — genuinely outside Elementor's control surface; editable text already split into separate heading widgets

FIXES APPLIED: 2
REMAINING ISSUES: 1
  - cssX99 @ content[3].elements[0].settings.custom_css: 80-line custom_css block implementing a custom carousel — requires design decision on whether to drop the carousel entirely or build it as a separate Pro slides widget (not currently available in this template's catalog)

VERDICT: FAIL
```
