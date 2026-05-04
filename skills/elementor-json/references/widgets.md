# Widget catalog

Every widget settings object accepts responsive suffixes (`_tablet`, `_mobile`) and the advanced `_padding` / `_margin` settings. See `containers.md` for those.

Every widget has `"elements": []` and `"isInner": false` (yes, even when nested inside a container — the `isInner` flag is for containers, but widgets repeat it as `false`).

## heading

```json
{
  "id": "head001",
  "elType": "widget",
  "widgetType": "heading",
  "settings": {
    "title": "Heading Text",
    "header_size": "h1",
    "align": "left",
    "align_tablet": "center",
    "title_color": "#FFFFFF",
    "typography_typography": "custom",
    "typography_font_size": {"unit": "rem", "size": 3.5},
    "typography_font_size_tablet": {"unit": "rem", "size": 2.75},
    "typography_font_weight": "700",
    "typography_line_height": {"unit": "em", "size": 1.2},
    "_margin": {"unit": "px", "top": "0", "right": "0", "bottom": "25", "left": "0", "isLinked": false}
  },
  "elements": [],
  "isInner": false
}
```

`typography_typography: "custom"` is REQUIRED — without it, every other typography setting is silently ignored.

## text-editor

```json
{
  "id": "text001",
  "elType": "widget",
  "widgetType": "text-editor",
  "settings": {
    "editor": "<p>Text content</p>",
    "align": "left",
    "text_color": "rgba(255, 255, 255, 0.8)",
    "typography_typography": "custom",
    "typography_font_size": {"unit": "rem", "size": 1.125},
    "typography_line_height": {"unit": "em", "size": 1.7},
    "_margin": {"unit": "px", "top": "0", "right": "0", "bottom": "30", "left": "0", "isLinked": false}
  },
  "elements": [],
  "isInner": false
}
```

`editor` takes raw HTML. Use `<p>`, `<strong>`, `<em>`, `<a>`, `<ul>` — what TinyMCE would output.

## button

```json
{
  "id": "btn001",
  "elType": "widget",
  "widgetType": "button",
  "settings": {
    "text": "Button Text",
    "link": {"url": "#", "is_external": "", "nofollow": ""},
    "align": "left",
    "button_text_color": "#FFFFFF",
    "background_color": "#E63946",
    "hover_color": "#FFFFFF",
    "button_background_hover_color": "#D32F3F",
    "typography_typography": "custom",
    "typography_font_size": {"unit": "rem", "size": 1},
    "typography_font_weight": "600",
    "border_radius": {"unit": "px", "top": "30", "right": "30", "bottom": "30", "left": "30", "isLinked": true},
    "text_padding": {"unit": "px", "top": "12", "right": "24", "bottom": "12", "left": "24", "isLinked": false},
    "selected_icon": {"value": "fas fa-star", "library": "fa-solid"},
    "icon_align": "left",
    "icon_indent": {"unit": "px", "size": 8},
    "icon_color": "#FBBF24"
  },
  "elements": [],
  "isInner": false
}
```

Button uses `text_padding` (not `padding`) for inner padding. Hover color is `button_background_hover_color`, not `background_hover_color`.

Drop the `selected_icon` block entirely if there's no icon — leaving it with empty `value` shows a missing-icon placeholder.

## image

```json
{
  "id": "img001",
  "elType": "widget",
  "widgetType": "image",
  "settings": {
    "image": {"url": "", "id": ""},
    "image_size": "full",
    "align": "center",
    "width": {"unit": "px", "size": 200},
    "border_radius": {"unit": "px", "top": "10", "right": "10", "bottom": "10", "left": "10", "isLinked": true}
  },
  "elements": [],
  "isInner": false
}
```

Leave `image.url` and `image.id` empty in templates — the user replaces them with a Media Library image after import. `image_size: "full"` uses the original; `"large"`/`"medium"`/`"thumbnail"` use WordPress-generated sizes.

## video

```json
{
  "id": "vid001",
  "elType": "widget",
  "widgetType": "video",
  "settings": {
    "video_type": "youtube",
    "youtube_url": "https://www.youtube.com/watch?v=XHOmBV4js_E",
    "show_image_overlay": "yes",
    "image_overlay": {"url": "", "id": ""},
    "show_play_icon": "yes",
    "play_icon_color": "#1a1a2e",
    "lightbox": "yes",
    "aspect_ratio": "169"
  },
  "elements": [],
  "isInner": false
}
```

`aspect_ratio` is the digits without the colon: `"169"` = 16:9, `"219"` = 21:9, `"43"` = 4:3.

For Vimeo, use `video_type: "vimeo"` and `vimeo_url`. For self-hosted, `video_type: "hosted"` and `hosted_url`.

## html (escape hatch)

```json
{
  "id": "html001",
  "elType": "widget",
  "widgetType": "html",
  "settings": {"html": "<div>...</div>"},
  "elements": [],
  "isInner": false
}
```

Use only when the design genuinely can't be expressed with native widgets. The `html` widget is opaque to the Elementor UI — values inside aren't editable through controls and don't respond to responsive breakpoints unless you write your own media queries inside the HTML's `<style>` block.

## form (Elementor Pro)

```json
{
  "id": "form001",
  "elType": "widget",
  "widgetType": "form",
  "settings": {
    "form_name": "Quote Form",
    "form_fields": [
      {"_id": "f1", "field_type": "text", "field_label": "Name", "placeholder": "John", "required": "true", "width": "50", "width_mobile": "100"},
      {"_id": "f2", "field_type": "email", "field_label": "Email", "placeholder": "john@example.com", "required": "true", "width": "50", "width_mobile": "100"},
      {"_id": "f3", "field_type": "textarea", "field_label": "Message", "placeholder": "...", "width": "100", "rows": "4"}
    ],
    "button_text": "Submit",
    "button_width": "100",
    "button_background_color": "#E63946",
    "button_hover_background_color": "#D32F3F",
    "button_text_color": "#FFFFFF",
    "button_border_radius": {"unit": "px", "top": "50", "right": "50", "bottom": "50", "left": "50", "isLinked": true},
    "button_typography_typography": "custom",
    "button_typography_font_size": {"unit": "rem", "size": 1.05},
    "button_typography_font_weight": "700",
    "label_color": "#1B2A4A",
    "label_typography_typography": "custom",
    "label_typography_font_size": {"unit": "rem", "size": 0.875},
    "label_typography_font_weight": "700",
    "field_background_color": "#FFFFFF",
    "field_border_color": "#E5E7EB",
    "field_border_radius": {"unit": "px", "top": "8", "right": "8", "bottom": "8", "left": "8", "isLinked": true},
    "field_text_color": "#374151",
    "field_placeholder_color": "#9CA3AF",
    "column_gap": {"unit": "px", "size": 15},
    "row_gap": {"unit": "px", "size": 15},
    "mark_required_color": "#E63946"
  },
  "elements": [],
  "isInner": false
}
```

Form widget is **Pro-only**. On a free Elementor install, the import succeeds but the widget renders as "missing widget" until Pro is activated.

`width` on each field is a percentage as a string: `"50"` = half-width, `"100"` = full row. `width_mobile` lets fields stack on phones.

## nested-accordion (Elementor Pro)

The modern container-based accordion. Each item has a title in `items` array AND a corresponding locked container in `elements` for the body content.

```json
{
  "id": "acc001",
  "elType": "widget",
  "widgetType": "nested-accordion",
  "settings": {
    "items": [
      {"item_title": "First Item Title", "_id": "item1"},
      {"item_title": "Second Item Title", "_id": "item2"}
    ],
    "accordion_item_title_icon": {"value": "fas fa-chevron-down", "library": "fa-solid"},
    "accordion_item_title_icon_active": {"value": "fas fa-chevron-up", "library": "fa-solid"},
    "accordion_item_title_position_horizontal": "space-between",
    "title_tag": "h3",
    "accordion_item_title_color": "#0F172A",
    "accordion_item_title_typography_typography": "custom",
    "accordion_item_title_typography_font_size": {"unit": "rem", "size": 1},
    "accordion_item_title_typography_font_weight": "600",
    "icon_color": "#64748B",
    "icon_active_color": "#64748B",
    "icon_size": {"unit": "px", "size": 14},
    "accordion_item_title_padding": {"unit": "px", "top": "20", "right": "24", "bottom": "20", "left": "24", "isLinked": false},
    "accordion_border_border": "solid",
    "accordion_border_width": {"unit": "px", "top": "1", "right": "1", "bottom": "1", "left": "1", "isLinked": true},
    "accordion_border_color": "#E2E8F0",
    "accordion_border_radius": {"unit": "px", "top": "12", "right": "12", "bottom": "12", "left": "12", "isLinked": true},
    "accordions_gap": {"unit": "px", "size": 12},
    "accordion_background": "#FFFFFF"
  },
  "elements": [
    {
      "id": "item1cont",
      "elType": "container",
      "settings": {"_title": "First Item Title", "content_width": "full"},
      "elements": [
        {
          "id": "item1text",
          "elType": "widget",
          "widgetType": "text-editor",
          "settings": {
            "editor": "<p>Content for first item</p>",
            "text_color": "#64748B",
            "typography_typography": "custom",
            "typography_font_size": {"unit": "rem", "size": 0.9375},
            "typography_line_height": {"unit": "em", "size": 1.7}
          },
          "elements": [],
          "isInner": false
        }
      ],
      "isInner": true,
      "isLocked": true
    },
    {
      "id": "item2cont",
      "elType": "container",
      "settings": {"_title": "Second Item Title", "content_width": "full"},
      "elements": [
        {
          "id": "item2text",
          "elType": "widget",
          "widgetType": "text-editor",
          "settings": {
            "editor": "<p>Content for second item</p>",
            "text_color": "#64748B",
            "typography_typography": "custom",
            "typography_font_size": {"unit": "rem", "size": 0.9375},
            "typography_line_height": {"unit": "em", "size": 1.7}
          },
          "elements": [],
          "isInner": false
        }
      ],
      "isInner": true,
      "isLocked": true
    }
  ],
  "isInner": false
}
```

**Iron rule:** `items[]` length must equal the count of `isLocked: true` containers in `elements[]`, in the same order. The `_id` on each item should match the order of locked containers (they're paired by index, not by ID, but consistent IDs make debugging easier).

`isLocked: true` is what tells Elementor this container is the body for the corresponding accordion item — without it, the container shows up as a sibling section, not inside the accordion.
