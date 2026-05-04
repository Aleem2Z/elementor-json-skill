# Pro-only widgets

These require Elementor Pro to be installed and activated. On a free Elementor install, the import succeeds but the widgets render as "missing widget" placeholders until Pro activates.

## form

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

`width` on each field is a percentage as a string: `"50"` = half-width, `"100"` = full row. `width_mobile` lets fields stack on phones.

`field_type` options: `"text"`, `"email"`, `"tel"`, `"url"`, `"number"`, `"textarea"`, `"select"`, `"checkbox"`, `"radio"`, `"acceptance"`, `"date"`, `"time"`, `"upload"`, `"hidden"`, `"html"`, `"step"`.

## nested-accordion

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
          "settings": {"editor": "<p>Content for second item</p>"},
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

**Iron rule:** `items[]` length must equal the count of `isLocked: true` containers in `elements[]`, in the same order. They're paired by index, not by ID — but consistent IDs make debugging easier.

`isLocked: true` is what tells Elementor this container is the body for the corresponding accordion item. Without it, the container shows up as a sibling section, not inside the accordion.
