---
name: elementor-json
description: Use when converting Figma/screenshot designs into Elementor template JSON for WordPress, generating .json files for Elementor's Import/Export tool, building Elementor pages from a design, or asked about Elementor widget settings, container layouts, responsive variants, or template structure.
---

# Elementor JSON

Convert designs to Elementor-compatible JSON templates that import cleanly into WordPress via **Elementor → Tools → Import / Export**.

## Critical rule: settings, not CSS

Elementor stores layout in JSON settings — `padding`, `typography_font_size`, `flex_justify_content`, etc. Putting those in `custom_css` instead breaks the editor: Elementor's controls panel won't show the values, the responsive breakpoint switcher won't work, and theme styles can override the CSS rules.

**Always use JSON settings for:**
- Spacing (`padding`, `margin`, `_padding`, `_margin`)
- Sizing (`width`, `max_width`, `min_height`)
- Colors (`text_color`, `background_color`, `title_color`)
- Typography (`typography_font_size`, `typography_font_weight`, `typography_line_height`)
- Layout (`flex_direction`, `flex_justify_content`, `flex_align_items`, `flex_gap`)
- Visuals (`border_radius`, `box_shadow_*`)

**Use `custom_css` only for:** pseudo-elements (`::before`, `::after`), inline SVG backgrounds, hover transitions Elementor can't configure, things genuinely outside Elementor's control surface. See `references/custom-css.md`.

**Use `rem` for font sizes** — they scale with the user's root font size.

## Workflow

1. **Build HTML/CSS first.** Match the design pixel-perfect in plain HTML before touching JSON. Catches layout bugs cheaply, before they're buried under Elementor's verbose settings objects.
2. **Compare screenshots side-by-side.** Iterate the HTML until it matches.
3. **Translate to JSON.** Map each section to a container, each text/button/etc. to a widget. Maximize JSON settings; minimize `custom_css`.
4. **Validate by importing.** In WordPress: Elementor → Tools → Import / Export → Import Templates. Upload the JSON, drop the template into a page. It's correct only if (a) the layout matches, AND (b) every value is editable through the Elementor UI controls — not just visible.
5. **Test responsive at 1024px and 767px** in Elementor's preview.

## Top-level shape

```json
{
  "content": [/* container objects */],
  "page_settings": {"hide_title": "yes"},
  "version": "0.4",
  "title": "template-name",
  "type": "container"
}
```

`type: "container"` uses Elementor's modern flex container model. Don't use the legacy `section`/`column` types unless the target site is on a pre-3.16 Elementor that hasn't migrated.

## Reference files

- **`references/containers.md`** — container settings, responsive variants (`_tablet`/`_mobile`), nesting/hierarchy rules, common section patterns (hero, card grid, two-column).
- **`references/widgets.md`** — copy-paste templates for: heading, text-editor, button, image, video, html, form (Pro), nested-accordion (Pro). Each shows the full settings object with realistic values.
- **`references/custom-css.md`** — when `custom_css` is justified, the `selector` keyword, JSON escaping rules, common patterns.

Read the reference for the widget/container you're building. Don't try to remember the full schema — copy a template, then customize.

## Common mistakes

- **Forgetting `typography_typography: "custom"`** — typography settings are silently ignored without this flag.
- **`isLocked` mismatch on accordions** — `nested-accordion` requires every item in `items[]` to have a matching `isLocked: true` container in `elements[]`. Off-by-one breaks the whole accordion.
- **`isInner: true` on top-level containers** — top-level is always `false`; only nested containers are `true`.
- **Hardcoding pixel font sizes** — use `rem`. The site's base size scales them globally.
- **Putting `custom_css` on settings that have a JSON equivalent** — the editor won't reflect the value, future UI edits silently fail.
- **`_custom_css` instead of `custom_css`** — Custom CSS does NOT take the underscore prefix, even though it's on the Advanced tab next to `_padding`/`_margin` which do.

## ID convention

Use semantic prefixes so the JSON is greppable: `hero001`, `card001`, `form001`, `acc001`, `btn001`, `head001`. They only have to be unique within one template, not globally.
