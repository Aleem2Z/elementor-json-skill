# Containers, responsive, hierarchy

## Container template

```json
{
  "id": "hero001",
  "elType": "container",
  "settings": {
    "flex_direction": "row",
    "flex_justify_content": "center",
    "flex_align_items": "center",
    "flex_gap": {"column": "50", "row": "50", "isLinked": true, "unit": "px", "size": 50},
    "content_width": "full",
    "width": {"unit": "px", "size": 550},
    "max_width": {"unit": "px", "size": 1200},
    "min_height": {"unit": "vh", "size": 100},
    "padding": {"unit": "px", "top": "40", "right": "40", "bottom": "40", "left": "40", "isLinked": false},
    "background_background": "classic",
    "background_color": "#FFFFFF",
    "border_radius": {"unit": "px", "top": "20", "right": "20", "bottom": "20", "left": "20", "isLinked": true},
    "box_shadow_box_shadow_type": "yes",
    "box_shadow_box_shadow": {"horizontal": 0, "vertical": 10, "blur": 60, "spread": 0, "color": "rgba(0,0,0,0.15)"},
    "overflow": "hidden",
    "_title": "Section Name"
  },
  "elements": [],
  "isInner": false
}
```

`_title` is the friendly name shown in Elementor's navigator panel — set it on every meaningful section.

`content_width: "full"` removes the default 1140px max. Pair with `max_width` for a custom inner cap.

`background_background: "classic"` is the trigger that activates `background_color` / `background_image`. Forgetting it makes the color setting silently no-op.

## Responsive variants

Append `_tablet` or `_mobile` to any setting. The base value applies above 1024px; `_tablet` from 768–1024px; `_mobile` below 768px.

```json
{
  "width": {"unit": "px", "size": 550},
  "width_tablet": {"unit": "%", "size": 100},
  "flex_direction": "row",
  "flex_direction_tablet": "column",
  "typography_font_size": {"unit": "rem", "size": 3.5},
  "typography_font_size_tablet": {"unit": "rem", "size": 2.75},
  "typography_font_size_mobile": {"unit": "rem", "size": 2.25}
}
```

Padding and margin objects are responsive in full — provide a complete `padding_tablet` object, not just one side:

```json
{
  "padding": {"unit": "px", "top": "80", "right": "40", "bottom": "80", "left": "40", "isLinked": false},
  "padding_tablet": {"unit": "px", "top": "60", "right": "20", "bottom": "60", "left": "20", "isLinked": false},
  "padding_mobile": {"unit": "px", "top": "40", "right": "16", "bottom": "40", "left": "16", "isLinked": false}
}
```

## Hierarchy rules

| Level | `isInner` | Notes |
|-------|-----------|-------|
| Top-level container | `false` | Direct child of `content[]` |
| Nested container | `true` | Container inside another container |
| Widget | `false` | Always `false` regardless of nesting depth |
| Locked accordion item container | `true` + `"isLocked": true` | One per `items[]` entry |

## Section patterns

**Hero (full-bleed with form card):**
- Outer container: `min_height: 100vh`, background image/color, `flex_align_items: center`
- Inner row container: `max_width: 1200`, `flex_direction: row`, `flex_gap: 50`
- Left column container: heading + text-editor + button widgets
- Right column container: form widget with elevated background

**Card grid:**
- Outer container: `flex_direction: row`, `flex_wrap: "wrap"`, `flex_gap: {column: 24, row: 24}`
- Repeating card containers: `width: 33.33%`, `width_tablet: 50%`, `width_mobile: 100%`, with background, shadow, padding
- Widgets inside each card

**Two-column with sticky aside:**
- Outer row container
- Main column: `width: 66%`
- Aside column: `width: 33%`, `position: "sticky"`, `position_top: 100`
