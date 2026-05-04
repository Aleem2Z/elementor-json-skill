---
name: elementor-json
description: Use when converting Figma/screenshot designs into Elementor template JSON for WordPress, generating .json files for Elementor's Import/Export tool, building Elementor pages from a design, or asked about Elementor widget settings, container layouts, responsive variants, or template structure.
---

# Elementor JSON

Convert designs to Elementor-compatible JSON templates that import cleanly via **Elementor → Tools → Import / Export**.

## Critical rule: settings, not CSS

Elementor stores layout in JSON settings — `padding`, `typography_font_size`, `flex_justify_content`, etc. Putting those in `custom_css` instead breaks the editor: controls won't show the values, the responsive breakpoint switcher won't work, and theme styles can override the rules.

**Always use JSON settings for:**
- Spacing (`padding`, `margin`, `_padding`, `_margin`)
- Sizing (`width`, `max_width`, `min_height`)
- Colors (`text_color`, `background_color`, `title_color`)
- Typography (`typography_font_size`, `typography_font_weight`, `typography_line_height`) — and remember `typography_typography: "custom"` is REQUIRED to activate them
- Layout (`flex_direction`, `flex_justify_content`, `flex_align_items`, `flex_gap`, `flex_wrap`)
- Visuals (`border_*`, `box_shadow_*`, `background_*`)
- Hover states (`background_hover_*`, `border_color_hover`, `box_shadow_*_hover`)

**Use `custom_css` only for:** pseudo-elements, inline SVG backgrounds, transitions Elementor can't configure, things genuinely outside the control surface. See `references/custom-css.md`.

**Use `rem` for font sizes** — they scale with the user's root font size.

## Workflow

1. **Build HTML/CSS first.** Match the design pixel-perfect in plain HTML before touching JSON. Catches layout bugs cheaply, before they're buried under verbose settings objects.
2. **Compare screenshots side-by-side.** Iterate the HTML until it matches.
3. **Translate to JSON.** Map each section to a container, each text/button/etc. to a widget. Maximize JSON settings; minimize `custom_css`.
4. **Validate by importing.** WordPress → Elementor → Tools → Import / Export → Import Templates. Upload the JSON and drop the template into a page. It's correct only if (a) the layout matches, AND (b) every value is editable through the Elementor UI controls — not just visible.
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

`type: "container"` uses the modern flex container model. Don't use the legacy `section`/`column` types unless the target site is on a pre-3.16 Elementor that hasn't migrated.

## Reference files

- **`references/containers.md`** — container settings, responsive variants (`_tablet`/`_mobile`), borders, hover states, background images, hierarchy rules, common section patterns.
- **`references/widgets.md`** — copy-paste templates for the common widgets: heading, text-editor, button, image, video, html, icon, divider, icon-list, star-rating, gallery.
- **`references/pro-widgets.md`** — Elementor Pro–only widgets: form, nested-accordion.
- **`references/custom-css.md`** — when `custom_css` is justified, the `selector` keyword, JSON escaping, common patterns, and the `custom_css` vs `_custom_css` situation.
- **`references/gotchas.md`** — common mistakes, ID conventions, settings that silently no-op without their activation flag.

Read the reference for what you're building. Don't try to remember the full schema — copy a template, then customize.
