# Widget catalog

Common (free Elementor) widgets. For Pro-only widgets (form, nested-accordion) see `pro-widgets.md`.

Every widget settings object accepts responsive suffixes (`_tablet`, `_mobile`) and the advanced `_padding` / `_margin` settings — see `containers.md`. Every widget has `"elements": []` and `"isInner": false`.

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

`editor` takes raw HTML — what TinyMCE would output: `<p>`, `<strong>`, `<em>`, `<a>`, `<ul>`.

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

Button uses `text_padding` (not `padding`) for inner padding. Hover background is `button_background_hover_color`. Drop the `selected_icon` block entirely if there's no icon — leaving it with empty `value` shows a missing-icon placeholder.

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

## icon

The single-icon widget. Common for feature cards, stat blocks, contact info.

```json
{
  "id": "icon001",
  "elType": "widget",
  "widgetType": "icon",
  "settings": {
    "selected_icon": {"value": "fas fa-hands-helping", "library": "fa-solid"},
    "view": "stacked",
    "shape": "circle",
    "primary_color": "#4591d5",
    "secondary_color": "#FFFFFF",
    "icon_color": "#FFFFFF",
    "icon_size": {"unit": "px", "size": 36},
    "icon_size_mobile": {"unit": "px", "size": 30},
    "icon_padding": {"unit": "px", "top": "22", "right": "22", "bottom": "22", "left": "22", "isLinked": true},
    "background_background": "classic",
    "background_color": "#4591d5",
    "border_radius": {"unit": "%", "top": "50", "right": "50", "bottom": "50", "left": "50", "isLinked": true},
    "align": "center"
  },
  "elements": [],
  "isInner": false
}
```

`view`: `"default"` (icon only), `"stacked"` (icon on a colored background), or `"framed"` (icon inside a border frame).

`shape`: `"circle"` or `"square"` — only meaningful when `view` is `stacked` or `framed`.

For stacked/framed views, `primary_color` is the background (or frame) and `secondary_color` is the icon itself. For `default`, just use `icon_color`.

## divider

```json
{
  "id": "div001",
  "elType": "widget",
  "widgetType": "divider",
  "settings": {
    "width": {"unit": "px", "size": 40},
    "weight": {"unit": "px", "size": 3},
    "color": "#3B82F6",
    "gap": {"unit": "px", "size": 0},
    "_margin": {"unit": "px", "top": "12", "right": "0", "bottom": "15", "left": "0", "isLinked": false},
    "_css_classes": "card-line"
  },
  "elements": [],
  "isInner": false
}
```

`width` is the line length. `weight` is its thickness. `gap` adds vertical space above + below the line. Add `"style": "dashed"` (or `"dotted"`, `"double"`) for non-solid lines.

## icon-list

Bulleted list with custom icon per item. Used heavily for feature lists.

```json
{
  "id": "list001",
  "elType": "widget",
  "widgetType": "icon-list",
  "settings": {
    "icon_list": [
      {"_id": "ben001", "text": "Same-Day Service", "selected_icon": {"value": "fas fa-check", "library": "fa-solid"}},
      {"_id": "ben002", "text": "Free Consultation", "selected_icon": {"value": "fas fa-check", "library": "fa-solid"}},
      {"_id": "ben003", "text": "100% Satisfaction", "selected_icon": {"value": "fas fa-check", "library": "fa-solid"}}
    ],
    "view": "inline",
    "space_between": {"unit": "px", "size": 35},
    "space_between_mobile": {"unit": "px", "size": 15},
    "icon_color": "#FFFFFF",
    "icon_color_hover": "#FFFFFF",
    "icon_size": {"unit": "px", "size": 12},
    "icon_self_align": "center",
    "text_color": "#FFFFFF",
    "text_color_hover": "#FFFFFF",
    "text_indent": {"unit": "px", "size": 10},
    "typography_typography": "custom",
    "typography_font_size": {"unit": "rem", "size": 0.95},
    "typography_font_weight": "600"
  },
  "elements": [],
  "isInner": false
}
```

`view`: `"traditional"` (vertical list) or `"inline"` (horizontal row). `space_between` is the gap between items in either direction.

Each `icon_list[]` item can also accept a `link: {"url": "..."}` to make that one row a link.

## star-rating

```json
{
  "id": "rate001",
  "elType": "widget",
  "widgetType": "star-rating",
  "settings": {
    "title": "",
    "rating_scale": 5,
    "rating": 5,
    "star_style": "font_awesome",
    "unmarked_star_style": "solid",
    "star_size": {"unit": "px", "size": 15},
    "star_color": "#f5a623",
    "star_unmarked_color": "#e0e0e0",
    "align": "left",
    "_margin": {"unit": "px", "top": "0", "right": "0", "bottom": "16", "left": "0", "isLinked": false}
  },
  "elements": [],
  "isInner": false
}
```

`rating` is a number 0–`rating_scale` (decimals work — `4.5` shows half a star). Leave `title: ""` for testimonial use; set it to label the rating.

## gallery

Image gallery with grid layout, lightbox, hover overlays.

```json
{
  "id": "gall001",
  "elType": "widget",
  "widgetType": "gallery",
  "settings": {
    "gallery_type": "single",
    "gallery": [
      {"_id": "gi1", "id": "", "url": "https://placehold.co/600x450"},
      {"_id": "gi2", "id": "", "url": "https://placehold.co/600x450"},
      {"_id": "gi3", "id": "", "url": "https://placehold.co/600x450"}
    ],
    "gallery_layout": "grid",
    "columns": "3",
    "columns_tablet": "2",
    "columns_mobile": "1",
    "aspect_ratio": "0.75",
    "gap": {"unit": "px", "size": 24},
    "gap_mobile": {"unit": "px", "size": 16},
    "link_to": "none",
    "image_border_radius": {"unit": "px", "top": "12", "right": "12", "bottom": "12", "left": "12", "isLinked": true},
    "overlay_hover_animation": "fade",
    "show_title": "yes",
    "show_description": "yes",
    "title_tag": "h4"
  },
  "elements": [],
  "isInner": false
}
```

`gallery_type`: `"single"` (one gallery) or `"multiple"` (multiple tagged galleries with a filter bar — adds a `galleries[]` array of tag definitions).

`link_to`: `"none"`, `"file"` (lightbox), or `"custom"`.

`aspect_ratio`: as a string fraction — `"0.75"` = 4:3, `"0.5625"` = 16:9, `"1"` = square.

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

`aspect_ratio` here uses digits without the colon: `"169"` = 16:9, `"219"` = 21:9, `"43"` = 4:3 (different format from `gallery` — Elementor inconsistency).

For Vimeo, use `video_type: "vimeo"` and `vimeo_url`. Self-hosted: `video_type: "hosted"` and `hosted_url`.

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

Use only when the design genuinely can't be expressed with native widgets. The `html` widget is opaque to the Elementor UI — values inside aren't editable through controls and don't respond to the responsive breakpoints unless you write your own media queries inside a `<style>` block.
