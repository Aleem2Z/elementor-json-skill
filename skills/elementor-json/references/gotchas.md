# Gotchas and conventions

## Activation flags (silent no-ops without them)

Several Elementor settings are gated behind a sibling "activation" key. Without the flag, the value is silently ignored — no error, just nothing happens.

| Want to set | Required flag |
|---|---|
| `typography_*` (font size, weight, etc.) | `typography_typography: "custom"` |
| `background_color` / `background_image` | `background_background: "classic"` (or `"gradient"`) |
| `background_hover_color` | `background_hover_background: "classic"` (or `"gradient"`) |
| `box_shadow_box_shadow` | `box_shadow_box_shadow_type: "yes"` |
| `box_shadow_box_shadow_hover` | `box_shadow_box_shadow_type_hover: "yes"` |
| `border_width` / `border_color` | `border_border: "solid"` (or `"dashed"`, `"dotted"`, `"double"`, `"groove"`) |

`border_radius` is NOT gated — it works on its own.

If a setting silently has no effect after import, the activation flag is the first thing to check.

## `isInner` rules

| Element | Value |
|---|---|
| Top-level container (direct child of `content[]`) | `"isInner": false` |
| Nested container (inside another container) | `"isInner": true` |
| Any widget | `"isInner": false` (always, regardless of nesting depth) |
| Accordion item body container | `"isInner": true` + `"isLocked": true` |

Top-level container with `isInner: true` will fail to import correctly. Nested container with `isInner: false` may render but won't show in Elementor's nav panel as a child.

## Underscore prefix conventions

Elementor uses an underscore prefix for "advanced" settings on each element:

- `_padding`, `_margin` — element's own outer/inner spacing (Advanced tab)
- `_element_id`, `_css_classes` — element's HTML id and class
- `_z_index` — stacking
- `_title` — friendly name shown in the navigator (containers only)
- `_flex_size`, `_flex_align_self`, `_flex_order` — how this element behaves inside its parent flex container

But **`custom_css` does NOT take the underscore** — even though it lives on the same Advanced tab. See `custom-css.md` for the `custom_css` vs `_custom_css` story.

## ID conventions

Use semantic prefixes — they make the JSON greppable:

```
hero001  card001  form001  acc001
btn001   head001  text001  img001
list001  icon001  rate001  div001
```

IDs only need to be unique within one template, not globally. Elementor regenerates collisions on import without complaining.

## Responsive units

- **Font sizes**: `rem` — scales with the user's root font size.
- **Container max-widths, gaps**: `px` — predictable across users.
- **Container widths in card grids**: `%` — `33.33`, `50`, `100`.
- **Hero `min_height`**: `vh` — `100` for full-screen heroes.
- **Padding/margin**: `px` is fine; `em`/`rem` are accepted but harder to debug.

Always provide `_tablet` and `_mobile` overrides for any setting that visibly changes between breakpoints — flex_direction, font sizes, padding, width.

## Responsive padding/margin: always set `isLinked: false` or get fake 2–4px padding

Symptom: in the published site (or even in the Elementor editor when switching to tablet/iPad/mobile views), containers and widgets get an unexpected ~2–4px padding on all four sides — even though nothing was set in the JSON. The client sees buttons and content looking unnaturally inset from container edges, especially on iPad.

Cause: Elementor's editor auto-enables the "Link values together" toggle on padding/margin controls the moment the user (or the renderer) switches to tablet/iPad/mobile mode. When `isLinked` is not explicitly `false` in the JSON, Elementor treats the unset state as linked and falls back to whatever value WordPress's stylesheet baselines (typically 2–4px) across all four sides. The desktop variant usually escapes this, but the responsive variants get bitten.

Fix: every padding and margin object in your JSON must include `"isLinked": false` explicitly — no exceptions. This covers `padding`, `padding_tablet`, `padding_mobile`, `margin`, `margin_tablet`, `margin_mobile`, **and** the underscore-prefixed Advanced-tab variants `_padding`, `_padding_tablet`, `_padding_mobile`, `_margin`, `_margin_tablet`, `_margin_mobile`. Applies to ALL widgets (containers, buttons, headings, icon-boxes, icon-lists, text-editors, etc.) — not just containers.

Real-world failure mode: a container holding a phone button and a "Request Quote" button got fake padding around the buttons in iPad view, so the buttons looked unnaturally inset from the container edges. The client had to manually toggle "Link values together" off in the Elementor editor for every responsive variant — annoying and easily missed.

Example of the corrected pattern:

```json
{
  "padding": {"unit": "px", "top": "60", "right": "0", "bottom": "60", "left": "0", "isLinked": false},
  "padding_tablet": {"unit": "px", "top": "40", "right": "0", "bottom": "40", "left": "0", "isLinked": false},
  "padding_mobile": {"unit": "px", "top": "30", "right": "0", "bottom": "30", "left": "0", "isLinked": false}
}
```

The verifier (elementor-verify skill) will flag any padding/margin object missing `isLinked: false` on a responsive variant.

## Common silent-failure causes

- `typography_*` without `typography_typography: "custom"` — typography ignored.
- `background_color` without `background_background: "classic"` — color ignored.
- `box_shadow_box_shadow` without `box_shadow_box_shadow_type: "yes"` — shadow ignored.
- `_custom_css` on a portable template that gets imported into a site without the theme that defines it — CSS silently absent.
- Hardcoded pixel font sizes — looks fine on dev, breaks the user's accessibility scaling. Use `rem`.
- `selected_icon` block with empty `value` — shows a missing-icon placeholder. Drop the whole block instead.

## Common loud-failure causes

- `nested-accordion` with `items[]` count ≠ `isLocked: true` container count — accordion fails to render or shows ghost items.
- `type: "section"` at the top level on a modern Elementor (3.16+) — imports as a deprecated section block; needs manual conversion to containers.
- Missing `version` or `version` not `"0.4"` — import may reject the file.
- Pro-only widgets (`form`, `nested-accordion`) on a free Elementor install — placeholder "missing widget" cards in the editor. (If the user has Pro confirmed, this isn't an issue — go ahead and use them.)

## HTML-widget overuse (the most common mistake)

Symptoms: client can't edit a heading because it's inside an HTML widget; mobile breakpoints don't reflow the layout; theme styles don't apply; the responsive controls panel is empty for that block.

Cause: reached for the `html` widget when a combination of native widgets + `custom_css` would have worked.

Fix: re-read the "widgets first" decision tree in `SKILL.md`. Trust pills are `icon-box`. Feature lists are `icon-list`. FAQ Qs are `accordion`. Stat numbers are `counter`. Card titles + subtitles are `heading` + `text-editor`. Eyebrow chips are a `heading` widget with `custom_css` doing the pill background and pulse dot. The only legitimate HTML widgets are decorative shells where every editable value has been pushed out to its own native widget inside the shell.

## FAQ accordion question gets underlined on hover

Symptom: in Elementor's preview the accordion title looks fine, but on the published site the question text gets a `text-decoration: underline` when hovered.

Cause: many WordPress themes apply `text-decoration: underline` to `.elementor-tab-title a` or `.elementor-tab-title:hover` via theme CSS that outranks Elementor's defaults.

Fix: add this `custom_css` to the accordion widget:

```css
selector .elementor-accordion-title,
selector .elementor-tab-title,
selector .elementor-tab-title a,
selector .elementor-tab-title *,
selector .elementor-tab-title:hover,
selector .elementor-tab-title:hover * {
  text-decoration: none !important;
  border-bottom: 0 !important;
}
```

The `:hover *` descendant selector is the part most people forget — the title text is in an inner span, and overriding only `.elementor-tab-title:hover` leaves the span's underline intact.

## Button hover text becomes invisible

Symptom: an outline-style button looks correct at rest, but on hover the background swaps to white/navy and the text disappears (because the text stayed its base color, which now matches the new background).

Cause: Elementor's `hover_color` setting works for buttons in some themes but is overridden by the theme's `.elementor-button-text` color rule in others.

Fix: don't rely on `hover_color` alone. Add explicit `custom_css`:

```css
selector .elementor-button:hover {
  background: #FFFFFF !important;
  color: #0B2C4D !important;
  border-color: #FFFFFF !important;
}
selector .elementor-button:hover .elementor-button-text {
  color: #0B2C4D !important;
}
```

The inner `.elementor-button-text` span needs its own color override because its rule wins specificity over the outer button.

## Stat grid stacks vertically when you set `flex_direction: row`

Symptom: you build a 4-column stat strip with `flex_direction: "row"` in JSON settings, but the columns stack vertically on render.

Cause: Elementor's inner `.e-con-inner` wrapper applies its own column flex, and the outer container's row direction doesn't propagate to the inner wrapper.

Fix: force both layers with `!important` in `custom_css`:

```css
selector,
selector > .e-con-inner {
  display: flex !important;
  flex-direction: row !important;
  flex-wrap: nowrap !important;
  align-items: center !important;
  gap: 24px !important;
}
selector > .e-con,
selector > .e-con-inner > .e-con {
  flex: 1 1 0 !important;
  max-width: 25% !important;
  width: 25% !important;
}
```

The `>.e-con-inner` part is the key — without it, only the outer container changes and the inner wrapper still imposes column.

## Mobile reordering: hero image needs to be between heading and paragraph

Symptom: on desktop the hero is text-left + image-right (two columns). On mobile when the columns stack, you want the order to be: heading → image → paragraph + CTA. Natural flex column order gives heading → paragraph + CTA → image, which is wrong.

Fix: structure the desktop layout as **three** sibling containers, not two: `lt` (heading), `right` (image), `lb` (paragraph + CTA + meta). Use `grid-template-areas` to lay them out as two columns on desktop (`lt` and `lb` stack in the left column, `right` spans both rows in the right column). On mobile, switch to flex column and use the `order` property to put `right` between `lt` and `lb`. See `custom-css.md` for the full pattern.

## Mobile hero needs different layout (full-bleed dark image overlay)

Symptom: the desktop hero is a clean text-on-light split, but on mobile the same layout looks empty/awkward — you want a full-bleed image background with a navy overlay and white text instead.

Fix: don't redo it with `custom_css`. Use Elementor's native mobile-variant background controls:

```json
{
  "background_image_mobile": {"url": "https://.../bg.jpg"},
  "background_overlay_color_mobile": "rgba(11,44,77,0.85)",
  "background_overlay_background_mobile": "classic"
}
```

For the text-color flip on mobile (everything goes white), set `title_color_mobile`, `text_color_mobile` on each widget. These are native settings, editable through the Elementor controls panel under the mobile breakpoint icon.

If you must hide the desktop portrait card entirely on mobile (because the design is fundamentally different), use the widget's `hide_mobile: "hidden-mobile"` setting — that's the native way, no `display:none` in `custom_css` needed.

## Counter widget shows 0 in editor

Symptom: in Elementor's editor, the `counter` widget always displays `0+` regardless of `ending_number`. Looks broken.

Cause: not broken — counters animate from `starting_number` only when scrolled into view on the front-end. The editor doesn't trigger the scroll-into-view event.

Fix: verify on the published page, not in the editor. If the user reports "the counter shows 0," walk them through viewing the live preview.

## Non-numeric "stats" (24/7, $100K, 4.9★) don't work with counter

The counter widget animates from 0 and renders integers/decimals only. For values like `24/7`, `$100K`, or `4.9★`, use a `heading` widget instead. Style the suffix with an inline `<small>` span and color it red via `custom_css` on `selector small`:

```json
{
  "title": "4.9<small>★</small>",
  "custom_css": "selector small{color:#E63946;font-size:24px;margin-left:2px;font-weight:800}"
}
```

This produces the same visual as a counter's suffix styling but doesn't require numeric animation.
