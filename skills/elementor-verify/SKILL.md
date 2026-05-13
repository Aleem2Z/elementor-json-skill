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

You'll receive a target JSON file path. Optionally, you may also receive an HTML reference file path — if provided, use it to perform a third audit (design fidelity) in addition to the widget-first audit. If no HTML reference is given, skip the comparative design-fidelity checks but still enforce the universal rules (e.g. `isLinked: false` on all responsive paddings).

Read the target JSON (and HTML reference, if provided), audit against the rules below, fix violations in-place using the Edit tool, then return a structured report in the exact format at the bottom of this file. Do not return anything other than that report — the calling agent parses it to decide whether to mark the file PASS or FAIL.

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

## The universal padding/margin check (run on every audit, no HTML needed)

This check runs on EVERY audit, regardless of whether an HTML reference was provided. It's a hard-fail rule that prevents WordPress/Elementor's fake auto-padding bug on responsive breakpoints.

- Walk every widget and every container in the JSON recursively.
- For every padding/margin object — including `padding`, `padding_tablet`, `padding_mobile`, `margin`, `margin_tablet`, `margin_mobile`, `_padding`, `_padding_tablet`, `_padding_mobile`, `_margin`, `_margin_tablet`, `_margin_mobile` — verify that `"isLinked": false` is explicitly present on the object.
- If `isLinked` is missing OR `isLinked: true`: **VIOLATION**. Fix by adding/changing the field to `"isLinked": false` in-place. This is non-negotiable — Elementor's responsive padding/margin controls default to "Link values together" in tablet/iPad/mobile views, which silently injects fake 2-4px padding when individual sides differ.

These violations are reported under **UNIVERSAL ISSUES FIXED**, not the widget-first VIOLATIONS list.

**Scope: This check applies ONLY to the 12 padding/margin keys listed above.** Other linkable-corner / linkable-sides keys in Elementor JSON — `border_radius`, `border_width`, `flex_gap`, `_flex_gap`, `text_padding`, `icon_padding`, `title_padding`, `content_padding`, `image_border_radius` (and their responsive variants) — also take `isLinked`, but they are not affected by the responsive auto-link bug. They may use `isLinked: true` legitimately when the design genuinely wants all four corners/sides equal. Do not change `isLinked` on those keys.

## Design fidelity audit (only when HTML reference is provided)

If — and only if — an HTML reference file path was provided alongside the JSON target, run this third audit. Read the HTML file in full first, extract its CSS (inline `<style>`, inline `style=""` attributes, and any same-directory linked stylesheet you can resolve), then perform these three checks. All findings go under **DESIGN FIDELITY ISSUES** in the report.

### A. Color palette compliance

- Parse the HTML reference for every distinct color value used: hex (`#fff`, `#ffffff`), `rgb(...)`, `rgba(...)`, `hsl(...)`, `hsla(...)`, and named CSS colors (`white`, `transparent`, etc.). Build this as the **source palette**.
- Walk every color-setting field in the JSON: `title_color`, `text_color`, `background_color`, `background_overlay_color`, `border_color`, `button_background_color`, `button_text_color`, `icon_color`, `primary_color`, `secondary_color`, `heading_color`, `description_color`, `_background_color`, all `*_hover_*` color variants, and any `color`/`background-color`/`border-color` declarations inside surviving `custom_css` blocks. **Exclusion: Colors that appear only inside `url("data:image/svg+xml,...")` strings (whether URL-encoded as `%23xxxxxx` or plain `#xxxxxx` inside an encoded SVG) are part of an SVG asset, not a widget-applied color. They were already justified at the custom_css decision tree. Do NOT include those in the palette check, do NOT flag them as violations, do NOT auto-fix them.
- Every color value in the JSON MUST be in the source palette. Allow small variance for transparency/alpha when the base hex matches (e.g. `#3b82f6` in HTML → `rgba(59, 130, 246, 0.1)` in JSON is acceptable; `#3b82f6` in HTML → `#1e40af` in JSON is NOT).
- If a JSON color is not in the source palette:
  1. **If the HTML had an explicit color rule for the corresponding element** (you can identify which HTML rule applied) and the JSON drifted away from it: **VIOLATION**. Fix in-place to the HTML color.
  2. **If the JSON color is within ~10% RGB distance of an HTML palette color**: **VIOLATION**. Auto-fix to the nearest palette color.
  3. **If the HTML left the color unspecified for this element AND the JSON color is not within ~10% of any palette color**: do NOT auto-substitute (you'd be guessing). Mark as **JUSTIFIED with an INFERENCE note**, e.g. "Color `#3B82F6` on widget `icon7a2` was inferred — HTML had no explicit color rule for this icon. User confirmation needed if this is wrong." This is NOT a REMAINING ISSUE — it does not trip VERDICT: FAIL. It surfaces in the report so the user can spot and override if needed, but the verifier does not block on inferences alone.
  4. **If the JSON has multiple plausible palette matches and you cannot pick deterministically**: same as case 3 — mark JUSTIFIED with an INFERENCE note listing the candidates.

### B. Spacing compliance

- Extract padding, margin, and gap values from the HTML CSS — both shorthand (`padding: 20px 24px`) and longhand (`padding-top: 20px`). Build a list of spacing tokens used by the design.
- Walk every padding, margin, and gap field in the JSON. Padding/margin keys are the 12 listed in the universal check above. Gap keys to walk: `flex_gap`, `_flex_gap`, `gap`, `_gap`, `row_gap`, `column_gap`, and their responsive variants (`*_tablet`, `*_mobile`).
- Compare each padding/margin/gap in the JSON to that token list.
- A JSON value deviates significantly (>20% off the nearest source token, OR a default Elementor 10px appearing where the HTML had no spec at all) → **VIOLATION**. Fix it to the source value.
- Special attention: in multi-card rows (pricing tiers, feature grids), check that inner padding is consistent across siblings. If the middle card is taller than its siblings because its inner padding was dropped while siblings kept theirs, that's a VIOLATION — restore the inner padding on the missing card.

### C. Decorative effect coverage

- Find every `::before` and `::after` rule in the source CSS.
- Find every `@keyframes` definition and every `:hover` rule that uses `transition`, `transform`, color change, or any other animated property.
- For each, locate the corresponding JSON widget (by class name, id, position, or content match) and verify it has a `custom_css` block implementing the effect.
  - Missing `::before`/`::after` on the corresponding widget → **VIOLATION**. Add the `custom_css` block per the patterns in `~/.claude/skills/elementor-json/references/custom-css.md`.
  - Missing `:hover` transition/transform → **VIOLATION**. This typically requires BOTH (a) hover-state values in the widget's JSON settings (e.g. `background_hover_color`, `_transform_translateY_effect_hover`) AND (b) a `custom_css` `transition` rule for properties Elementor can't animate via JSON. Generators commonly do one without the other — flag and fix both.
  - Missing `@keyframes` animation → **VIOLATION**. Add the keyframes + `animation` rule as a `custom_css` block on the right widget.

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
- Padding/margin object missing `isLinked: false` on a responsive variant (`padding_tablet`, `padding_mobile`, etc.) — auto-creates fake 2-4px padding in Elementor's editor.
- Color value in JSON that doesn't appear anywhere in the source HTML's palette — generator hallucinated a theme default.
- Source HTML had a `::before`/`::after` decorative element that the JSON dropped — must be added as a `custom_css` block on the right widget.
- Source HTML had a `:hover` transition/transform effect that the JSON dropped — must be added as widget hover-state JSON settings + a `custom_css` transition for properties Elementor can't animate via JSON.

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

Return exactly this structure (no preamble, no postscript). The report has three audit sections: **VIOLATIONS FOUND** (widget-first), **UNIVERSAL ISSUES FIXED** (isLinked and other always-checked items), and **DESIGN FIDELITY ISSUES** (only populated when an HTML reference was provided — otherwise show `DESIGN FIDELITY ISSUES: N/A (no HTML reference provided)`). Inside **DESIGN FIDELITY ISSUES**, a sub-bucket **INFERENCES (justified, user may want to review)** lists items where the verifier could not deterministically pick a value (e.g. case 3 / case 4 color inferences). Inferences are JUSTIFIED — they are listed for user visibility but are NOT counted as REMAINING ISSUES and do NOT trip `VERDICT: FAIL`.

**PASS example** (clean run, HTML reference provided):

```
ELEMENTOR-VERIFY REPORT
========================
Target: <absolute path to JSON>
HTML Reference: <absolute path to HTML, or "none">

VIOLATIONS FOUND: <N>
  - <widget id> @ <JSON path>: <what was wrong>
      Fix applied: <one-line description of what you changed>
  - ...

JUSTIFIED HTML/CUSTOM_CSS: <M>
  - <widget id> @ <JSON path>: <one-line justification — what's outside Elementor's control surface>
  - ...

UNIVERSAL ISSUES FIXED: <U>
  - <widget id or container id> @ <JSON path>.<field>: <e.g. "padding_tablet missing isLinked: false">
      Fix applied: <e.g. "Set isLinked: false on padding_tablet object">
  - ...

DESIGN FIDELITY ISSUES: <D>   (or: N/A (no HTML reference provided))
  - Color: <widget id> @ <JSON path>.<field>: <e.g. "title_color #1e40af not in HTML palette; HTML used #3b82f6">
      Fix applied: <e.g. "Updated title_color to #3b82f6 to match source">
  - Spacing: <widget id> @ <JSON path>.<field>: <e.g. "padding 10px but HTML middle card had padding 32px 24px">
      Fix applied: <e.g. "Set padding to 32px 24px to match siblings">
  - Decorative: <widget id> @ <JSON path>: <e.g. "HTML had ::before gradient connector line dropped in JSON">
      Fix applied: <e.g. "Added custom_css block with ::before per timeline pattern in references/custom-css.md">
  - Hover: <widget id> @ <JSON path>: <e.g. "HTML had :hover transform: translateY(-4px) + box-shadow transition dropped">
      Fix applied: <e.g. "Added _transform_translateY_effect_hover JSON setting + custom_css transition for box-shadow">
  - ...

  INFERENCES (justified, user may want to review): <I>
    - Color: <widget id> @ <JSON path>.<field>: <e.g. "icon_color #3B82F6 was inferred — HTML had no explicit color rule for this icon. User confirmation needed if this is wrong.">
    - ...

FIXES APPLIED: <total across all three sections> (file edited in place)
REMAINING ISSUES: <should be 0; if non-zero, list each. INFERENCES are NOT counted here.>

VERDICT: PASS
```

- Use `VERDICT: PASS` only when `REMAINING ISSUES` is 0 (or all remaining items are JUSTIFIED with a written reason). INFERENCE notes are JUSTIFIED by definition and do NOT block a PASS verdict — a clean run with one or more INFERENCES still passes.
- Use `VERDICT: FAIL` when you cannot fully resolve violations (e.g., a fix requires a design decision you cannot make alone, or a violation type you don't know how to fix). Do not pretend a partial fix is a pass. Do NOT use FAIL for INFERENCE notes alone — inferences are surfaced for user visibility, not as blockers.
- If the JSON had zero `html` widgets and zero `custom_css` blocks AND zero universal/fidelity issues to begin with, return all-zero counts and `VERDICT: PASS` immediately.

**FAIL example** (when something can't be resolved, with HTML reference):

```
ELEMENTOR-VERIFY REPORT
========================
Target: /Users/.../pricing-template.json
HTML Reference: /Users/.../pricing-source.html

VIOLATIONS FOUND: 2
  - html7a2 @ content[0].elements[2]: Trust pill rendered as inline HTML
      Fix applied: Replaced with icon-box widget; preserved id; carried over _padding
  - html3f8 @ content[1].elements[0]: Stat card with inline number + label
      Fix applied: Converted to container with two heading widgets

JUSTIFIED HTML/CUSTOM_CSS: 1
  - htmlovr5 @ content[2].elements[1]: Overlapping absolute-positioned chips on hero image — genuinely outside Elementor's control surface; editable text already split into separate heading widgets

UNIVERSAL ISSUES FIXED: 6
  - cont_a1 @ content[0].settings.padding_tablet: missing isLinked: false
      Fix applied: Added "isLinked": false
  - cont_a1 @ content[0].settings.padding_mobile: isLinked was true
      Fix applied: Set "isLinked": false
  - wid_b7 @ content[1].elements[0].settings._margin_tablet: missing isLinked: false
      Fix applied: Added "isLinked": false
  - (3 more similar fixes elided for brevity in this example)

DESIGN FIDELITY ISSUES: 4
  - Color: wid_c3 @ content[2].elements[1].settings.title_color: "#1e40af" not in HTML palette
      Fix applied: Updated to "#3b82f6" to match source HTML's brand color
  - Spacing: wid_d5 @ content[3].elements[1].settings.padding: middle pricing card had no inner padding while siblings had 32px 24px
      Fix applied: Set padding to {top:32,right:24,bottom:32,left:24,unit:"px",isLinked:false}
  - Decorative: cont_e9 @ content[3]: HTML had ::before horizontal gradient connector line between numbered step circles, dropped in JSON
      Fix applied: Added custom_css block on container with ::before gradient line per timeline pattern in references/custom-css.md
  - Hover: wid_f4 @ content[4].elements[0]: HTML had :hover with color change + transform translateY(-2px), JSON had translateY but no color hover
      Fix applied: Added title_color_hover in JSON settings + custom_css transition for color/transform

  INFERENCES (justified, user may want to review): 1
    - Color: icon7a2 @ content[2].elements[3].settings.icon_color: "#3B82F6" was inferred — HTML had no explicit color rule for this icon. User confirmation needed if this is wrong.

FIXES APPLIED: 12
REMAINING ISSUES: 1   (INFERENCES are NOT counted here; the verdict below is driven by the unresolved carousel, not by the inference above)
  - cssX99 @ content[3].elements[0].settings.custom_css: 80-line custom_css block implementing a custom carousel — requires design decision on whether to drop the carousel entirely or build it as a separate Pro slides widget (not currently available in this template's catalog)

VERDICT: FAIL
```
