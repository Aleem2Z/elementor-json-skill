# Widget catalog

Free Elementor widgets. For Pro widgets see `pro-widgets.md` (core Pro catalog), `pro-widgets-dynamic.md` (query-driven widgets like loop-grid and taxonomy-filter), and `pro-widgets-navigation.md` (header/footer template-part widgets like nav-menu, mega-menu, off-canvas).

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

## icon-box

A combined icon + title + description widget. **Use this for "trust pills", "feature cards", "step cards", or anything that's [small icon] + [bold label] + [optional subtitle]** — instead of building three widgets stacked or, worse, an HTML widget.

```json
{
  "id": "ib001",
  "elType": "widget",
  "widgetType": "icon-box",
  "settings": {
    "selected_icon": {"value": "fas fa-clock", "library": "fa-solid"},
    "view": "default",
    "title_text": "60-Second Quotes",
    "description_text": "Locked-in before we leave",
    "title_size": "h4",
    "position": "left",
    "icon_space": {"unit": "px", "size": 14},
    "icon_size": {"unit": "px", "size": 20},
    "icon_padding": {"unit": "px", "size": 11},
    "primary_color": "#E63946",
    "hover_primary_color": "#FFFFFF",
    "icon_secondary_color": "#FFFFFF",
    "hover_secondary_color": "#E63946",
    "title_color": "#0B2C4D",
    "description_color": "#6B7280",
    "title_typography_typography": "custom",
    "title_typography_font_family": "Poppins",
    "title_typography_font_size": {"unit": "px", "size": 13},
    "title_typography_font_weight": "800",
    "title_typography_letter_spacing": {"unit": "px", "size": -0.05},
    "description_typography_typography": "custom",
    "description_typography_font_family": "Inter",
    "description_typography_font_size": {"unit": "px", "size": 11.5},
    "description_typography_line_height": {"unit": "em", "size": 1.4},
    "icon_border_radius": {"unit": "px", "top": "10", "right": "10", "bottom": "10", "left": "10", "isLinked": true},
    "custom_css": "selector .elementor-icon{background:rgba(230,57,70,.08) !important;transition:background .15s,color .15s}selector:hover .elementor-icon{background:#E63946 !important;color:#fff !important}"
  },
  "elements": [],
  "isInner": false
}
```

`position`: `"left"`, `"right"`, or `"top"` for legacy alignment, or `"inline-start"` / `"block-start"` for CSS-logical-property alignment that respects RTL. Use the logical versions when possible; pair `position` (desktop) with `position_mobile: "block-start"` to flip icon-above-text on phones.

`view`: `"default"` (icon only), `"stacked"` (icon on a colored background — set `primary_color` as the background), or `"framed"` (icon inside a circular/square bordered ring — set `primary_color` as the ring color).

For a hover-color flip (icon background goes from light to solid), set `primary_color` (base) and `hover_primary_color` (hover) and add a small `transition` rule in `custom_css`.

### icon-box variant: framed circle (for trust strips on dark backgrounds)

When the design shows a thin-ring circular icon next to a stack of two short caps lines (e.g. `AFTER-HOURS / AVAILABLE`, `LOAN SIGNING / AGENT`) on a navy background, use the framed-circle variant. This is exactly what the trust strip on a navy CTA bar looks like:

```json
{
  "id": "ib002",
  "elType": "widget",
  "widgetType": "icon-box",
  "settings": {
    "selected_icon": {"value": "far fa-moon", "library": "fa-regular"},
    "view": "framed",
    "shape": "circle",
    "title_text": "AFTER-HOURS",
    "description_text": "AVAILABLE",
    "title_size": "h3",
    "position": "inline-start",
    "position_mobile": "block-start",
    "content_vertical_alignment": "middle",
    "icon_space": {"unit": "px", "size": 10},
    "icon_space_mobile": {"unit": "px", "size": 5},
    "title_bottom_space": {"unit": "px", "size": -2},
    "primary_color": "#FFFFFF",
    "hover_primary_color": "#FFFFFF",
    "icon_size": {"unit": "px", "size": 30},
    "icon_size_mobile": {"unit": "px", "size": 25},
    "icon_padding": {"unit": "px", "size": 12},
    "icon_padding_mobile": {"unit": "px", "size": 10},
    "border_width": {"unit": "px", "top": "2", "right": "2", "bottom": "2", "left": "2", "isLinked": true},
    "border_radius": {"unit": "px", "top": "60", "right": "60", "bottom": "60", "left": "60", "isLinked": true},
    "title_typography_typography": "custom",
    "title_typography_font_family": "Roboto",
    "title_typography_font_size": {"unit": "px", "size": 18},
    "title_typography_font_weight": "600",
    "title_color": "#FFFFFF",
    "hover_title_color": "#FFFFFF",
    "description_typography_typography": "custom",
    "description_typography_font_family": "Roboto",
    "description_typography_font_size": {"unit": "px", "size": 15},
    "description_typography_font_weight": "400",
    "description_color": "#E1E1E1"
  },
  "elements": [],
  "isInner": false
}
```

Key things this template demonstrates that aren't obvious from the prose above:
- `view: "framed"` + `shape: "circle"` together produce the thin-ring circular icon look — the icon sits inside a circular border drawn at the icon's edge.
- `border_width: 2` and `border_radius: 60` give the ring its weight and roundness. The 60 isn't a magic number — it's any value larger than half the icon container, which forces the circle.
- `position: "inline-start"` + `position_mobile: "block-start"` is the canonical responsive pair: icon-beside-text on desktop, icon-above-text on mobile.
- `title_bottom_space: -2` pulls the description tight against the title so they read as a two-line block.
- Empty `secondary_color`/`hover_secondary_color` is fine — the framed variant uses `primary_color` for both the ring and the icon.

This is the variant the client may have seen in their existing site's navy CTA strip. Reach for it whenever the design shows a labeled icon on a dark background — don't build this as HTML.

## accordion

The classic (free Elementor) accordion. Simpler and more reliable than `nested-accordion` for FAQs. **Strongly prefer this for FAQ sections.**

```json
{
  "id": "acc001",
  "elType": "widget",
  "widgetType": "accordion",
  "settings": {
    "tabs": [
      {"_id": "tab1", "tab_title": "Are there any hidden fees?", "tab_content": "<p>No. Every charge is itemized on the quote we send before dispatch.</p>"},
      {"_id": "tab2", "tab_title": "When am I billed for travel?", "tab_content": "<p>Standard appointments include up to 15 miles round-trip.</p>"}
    ],
    "title_html_tag": "h4",
    "icon_align": "right",
    "selected_icon": {"value": "fas fa-plus", "library": "fa-solid"},
    "selected_active_icon": {"value": "fas fa-minus", "library": "fa-solid"},
    "border_border": "solid",
    "border_width": {"unit": "px", "top": "1", "right": "1", "bottom": "1", "left": "1", "isLinked": true},
    "border_color": "#E5E9F0",
    "border_radius": {"unit": "px", "top": "10", "right": "10", "bottom": "10", "left": "10", "isLinked": true},
    "title_background": "#F5F7FA",
    "title_color": "#0B2C4D",
    "tab_active_color": "#0B2C4D",
    "tab_content_background_color": "#F5F7FA",
    "tab_content_color": "#6B7280",
    "icon_color": "#E63946",
    "icon_active_color": "#E63946",
    "title_typography_typography": "custom",
    "title_typography_font_family": "Inter",
    "title_typography_font_size": {"unit": "px", "size": 15},
    "title_typography_font_weight": "700",
    "title_typography_text_decoration": "none",
    "content_typography_typography": "custom",
    "content_typography_font_family": "Inter",
    "content_typography_font_size": {"unit": "px", "size": 14},
    "content_typography_line_height": {"unit": "em", "size": 1.7},
    "title_padding": {"unit": "px", "top": "18", "right": "22", "bottom": "18", "left": "22", "isLinked": false},
    "content_padding": {"unit": "px", "top": "0", "right": "22", "bottom": "20", "left": "22", "isLinked": false},
    "custom_css": "selector .elementor-accordion-item{margin-bottom:10px;overflow:hidden;border-radius:10px}selector .elementor-accordion .elementor-tab-title{cursor:pointer}selector .elementor-accordion-title,selector .elementor-tab-title,selector .elementor-tab-title a,selector .elementor-tab-title *,selector .elementor-tab-title:hover,selector .elementor-tab-title:hover *{text-decoration:none !important;border-bottom:0 !important}"
  },
  "elements": [],
  "isInner": false
}
```

The `accordion` widget is content-editable through `tabs[].tab_title` and `tabs[].tab_content` — the client can edit every Q and A in Elementor's panel. By contrast, FAQs built as HTML would be uneditable.

**Underline bug:** Many WordPress themes add `text-decoration: underline` on accordion titles via `.elementor-tab-title:hover`. The `custom_css` above kills it with `!important` on the title and all descendants. Always include this rule for accordion widgets unless you've confirmed the theme doesn't add the underline.

## counter

Animated number counter — counts up from `starting_number` to `ending_number` when scrolled into view. Use for stat strips like "1,000+ Signings", "24/7 Availability", "$100K E&O", "4.9★ Rating".

```json
{
  "id": "ctr001",
  "elType": "widget",
  "widgetType": "counter",
  "settings": {
    "starting_number": 0,
    "ending_number": 1000,
    "prefix": "",
    "suffix": "+",
    "duration": 2000,
    "thousand_separator": "yes",
    "thousand_separator_char": ",",
    "title": "Signings Completed",
    "title_tag": "div",
    "number_color": "#FFFFFF",
    "number_alignment": "center",
    "typography_number_typography": "custom",
    "typography_number_font_family": "Poppins",
    "typography_number_font_size": {"unit": "px", "size": 36},
    "typography_number_font_size_mobile": {"unit": "px", "size": 28},
    "typography_number_font_weight": "800",
    "typography_number_letter_spacing": {"unit": "px", "size": -0.72},
    "typography_number_line_height": {"unit": "em", "size": 1},
    "title_color": "rgba(255,255,255,0.7)",
    "title_horizontal_alignment": "center",
    "typography_title_typography": "custom",
    "typography_title_font_family": "Inter",
    "typography_title_font_size": {"unit": "px", "size": 11},
    "typography_title_font_weight": "600",
    "typography_title_letter_spacing": {"unit": "px", "size": 1.32},
    "typography_title_text_transform": "uppercase",
    "title_gap": {"unit": "px", "size": 8},
    "custom_css": "selector .elementor-counter-number-suffix{color:#E63946;font-size:24px;margin-left:2px;font-weight:800}selector .elementor-counter-number-prefix{color:#FFFFFF}selector .elementor-counter-number{display:inline-flex;align-items:baseline;justify-content:center}"
  },
  "elements": [],
  "isInner": false
}
```

`suffix` and `prefix` are rendered in their own spans (`.elementor-counter-number-suffix` / `-prefix`) — you can color them independently via `custom_css`. A red `+` after a navy number is a common stat-strip styling and is achieved entirely through native widget + `custom_css`, never through an HTML widget.

For values that aren't truly numeric (like "24/7", "4.9★", "$100K"), use a `heading` widget instead of a counter — the counter animates from 0 and only handles real integers/decimals. A heading with `custom_css` on inner `<small>` or `<span>` spans can produce the same visual.

**Editor note:** In Elementor's editor preview the counter shows `0` (it only animates when scrolled into view on the front-end). This is normal — verify on the published page, not in the editor.

## html (escape hatch — use as last resort)

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

Use only when the design genuinely can't be expressed with native widgets — and re-read SKILL.md's "widgets first" decision tree before deciding it can't. The `html` widget is opaque to the Elementor UI: values inside aren't editable through controls, the responsive breakpoint switcher doesn't apply, and the client must learn HTML to change a word.

**Legitimate uses include:** CSS keyframe animations (marquees, pulses), decorative SVG overlays, complex absolute-positioned floating chips on a portrait, stylized receipt/invoice mockups with `::after` notches, milestone timeline tracks where a connecting line runs through multiple cards. **Even then**, push editable content (headings, prices, button labels) out as native widgets and use HTML only for the decorative shell that wraps them.

**Wrong uses:** Trust pills (use `icon-box`), feature lists (use `icon-list`), FAQ Qs (use `accordion`), stat numbers (use `counter`), card titles + subtitles (use `heading` + `text-editor`), eyebrow chips (use `heading` with `custom_css` styling).

## Additional free widgets

### progress

When to use: Static progress bar (skills, stats, capacity meters) — a labeled horizontal bar filled to a fixed percentage. **Distinct from the Pro `progress-tracker`**, which is a scroll-position indicator pinned to the page edge.

Pro/free: free

```json
{
  "id": "pg001",
  "elType": "widget",
  "widgetType": "progress",
  "settings": {
    "title": "Web Design",
    "title_tag": "span",
    "title_display": "yes",
    "percent": {"unit": "%", "size": 75},
    "display_percentage": "show",
    "inner_text": "Web Designer"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `percent.size` is the fill amount (0–100). `display_percentage`: `"show"` (renders the number on the bar) or `"hide"`. `inner_text` is an optional caption rendered inside the bar. **Not** scroll-driven — the fill is static. For a scroll-based indicator use the Pro `progress-tracker` widget.

### testimonial

When to use: Single testimonial card with quote, name, job title, and an optional headshot. **Distinct from a testimonial carousel** — for a slider of testimonials, build a `nested-carousel` whose inner containers each hold testimonial widgets (or use `reviews` for the pre-styled Pro variant).

Pro/free: free

```json
{
  "id": "tst001",
  "elType": "widget",
  "widgetType": "testimonial",
  "settings": {
    "testimonial_content": "Excellent service — fast, friendly, and exactly what we needed.",
    "testimonial_image": {"url": "https://example.com/wp-content/uploads/avatar.jpg"},
    "testimonial_image_size": "full",
    "testimonial_name": "Jane Doe",
    "testimonial_job": "Operations Manager",
    "testimonial_image_position": "aside",
    "testimonial_alignment": "center"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `testimonial_image_position`: `"aside"` (image next to name/title block), `"top"`, or `"bottom"`. `testimonial_alignment`: `"left"`, `"center"`, `"right"`. Renders one card only — duplicate the widget (or wrap in a flex container / `nested-carousel`) for multiple testimonials.

### spacer

When to use: Hard pixel-spacing gap between widgets when container `_padding`/`_margin` won't do.

Pro/free: free

```json
{
  "id": "spc001",
  "elType": "widget",
  "widgetType": "spacer",
  "settings": {
    "space": {"unit": "px", "size": 50}
  },
  "elements": [],
  "isInner": false
}
```

Notes: For hard pixel-spacing only. Most spacing should use container `_margin`/`_padding` instead — only reach for `spacer` when you genuinely need an empty gap that varies independently of its surrounding container's padding (e.g., visually-tuned vertical rhythm in a hero). Has responsive variants `space_tablet` and `space_mobile`.

### menu-anchor

When to use: Invisible anchor target so on-page links (`#slug`) can scroll to a precise spot inside a section.

Pro/free: free

```json
{
  "id": "anc001",
  "elType": "widget",
  "widgetType": "menu-anchor",
  "settings": {
    "anchor": "pricing"
  },
  "elements": [],
  "isInner": false
}
```

Notes: Renders an invisible anchor — set `anchor` to the slug (no `#`), then link to `#that-slug` from buttons/menus elsewhere on the page. Distinct from `_element_id` on a section, which also works as a jump target but is less explicit; `menu-anchor` exists specifically to be referenced and is friendlier for `nav-menu` integration.

### alert

When to use: Colored notification box (info, success, warning, danger) with title, description, and optional dismiss button.

Pro/free: free

```json
{
  "id": "alr001",
  "elType": "widget",
  "widgetType": "alert",
  "settings": {
    "alert_type": "info",
    "alert_title": "This is an Alert",
    "alert_description": "I am a description. Click the edit button to change this text.",
    "show_dismiss": "show"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `alert_type` accepted values: `"info"`, `"success"`, `"warning"`, `"danger"` — each renders a matching default icon and background tint unless overridden via `dismiss_icon` / color styling. `show_dismiss`: `"show"` or `"hide"` controls the close button.

### read-more

When to use: Insert a `<!--more-->` excerpt cutoff marker into a single blog post so archive/listing pages truncate at that point.

Pro/free: free

```json
{
  "id": "rdm001",
  "elType": "widget",
  "widgetType": "read-more",
  "settings": {
    "link_text": "Continue reading"
  },
  "elements": [],
  "isInner": false
}
```

Notes: This is the `<!--more-->` excerpt-cutoff marker for blog posts, **not** a generic "read more" button. It's invisible on the actual post page; it only affects how the archive/excerpt is truncated and what the "continue" link reads. For a "read more" button widget on a page, use a regular `button` instead.
