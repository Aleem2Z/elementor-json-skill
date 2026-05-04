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
- Pro-only widgets (`form`, `nested-accordion`) on a free Elementor install — placeholder "missing widget" cards in the editor.
