# Pro-only widgets

These require Elementor Pro to be installed and activated. On a free Elementor install, the import succeeds but the widgets render as "missing widget" placeholders until Pro activates.

**Companion files:** query-driven widgets (loop-grid, loop-carousel, posts, portfolio) live in `pro-widgets-dynamic.md`. Navigation/header widgets (nav-menu, mega-menu, off-canvas, table-of-contents, search, login, countdown) live in `pro-widgets-navigation.md`.

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

## Extra Pro widgets (extracted from real-world export)

Minimal reference templates pulled from a production Elementor export. Each shows only the settings keys that distinguish the widget from its default state — empty strings, default-zero filters, motion_fx noise, mask/sticky boilerplate, and unused responsive overrides have been pruned. Add a real `id` (8-char hex) and adjust copy/colors per design.

### call-to-action

When to use: Marketing CTA card with a background image, overlay graphic, heading, description, and a button bundled into one widget — replaces a manually-built hero stack.

Pro/free: Pro

```json
{
  "id": "cta001",
  "elType": "widget",
  "widgetType": "call-to-action",
  "settings": {
    "skin": "classic",
    "bg_image": {"url": "https://example.com/wp-content/uploads/cta-bg.jpg"},
    "bg_image_size": "large",
    "graphic_element": "icon",
    "selected_icon": {"value": "fas fa-star", "library": "fa-solid"},
    "icon_view": "default",
    "icon_shape": "circle",
    "title": "Ready to get started?",
    "title_tag": "h2",
    "description": "Book a mobile notary in under 60 seconds.",
    "description_tag": "div",
    "button": "Book Now",
    "link_click": "button",
    "button_size": "sm",
    "alignment": "center",
    "content_animation": "grow",
    "transformation": "zoom-in"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `skin` options: `"classic"`, `"cover"`. `graphic_element`: `"image"`, `"icon"`, or `"none"`. `link_click`: `"button"` makes only the button clickable, `"box"` makes the whole card a link.

### image-box

When to use: Icon-box analogue but with an image instead of an icon — image on top/left/right, then title and description stacked or aligned next to it.

Pro/free: free

```json
{
  "id": "imgbx001",
  "elType": "widget",
  "widgetType": "image-box",
  "settings": {
    "image": {"url": "https://example.com/wp-content/uploads/feature.jpg"},
    "thumbnail_size": "full",
    "title_text": "Mobile Notary Service",
    "description_text": "Available 24/7 across the Space Coast region.",
    "title_size": "h3",
    "position": "top",
    "content_vertical_alignment": "top",
    "image_space": {"unit": "px", "size": 15},
    "image_size": {"unit": "%", "size": 30},
    "image_object_position": "center center"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `position`: `"top"`, `"left"`, or `"right"` — controls where the image sits relative to the text. `image_size` is a percentage of the widget width when `position` is `left`/`right`.

### animated-headline

When to use: Heading where part of the text rotates between multiple words (e.g., "We make it Better / Bigger / Faster") or one word gets a stylized highlight (circle marker, underline, etc.).

Pro/free: Pro

```json
{
  "id": "anim001",
  "elType": "widget",
  "widgetType": "animated-headline",
  "settings": {
    "headline_style": "highlight",
    "animation_type": "typing",
    "marker": "circle",
    "before_text": "This service is",
    "highlighted_text": "Amazing",
    "rotating_text": "Better\nBigger\nFaster",
    "loop": "yes",
    "highlight_animation_duration": 1200,
    "highlight_iteration_delay": 8000,
    "rotate_iteration_delay": 2500,
    "tag": "h3",
    "alignment": "center"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `headline_style`: `"highlight"` (one styled word) or `"rotate"` (cycling words). `marker` (highlight mode): `"circle"`, `"curly"`, `"underline"`, `"double"`, `"double_underline"`, `"underline_zigzag"`, `"strikethrough"`, `"x"`. `animation_type` (rotate mode): `"typing"`, `"clip"`, `"flip"`, `"swirl"`, `"blinds"`, `"drop-in"`, `"wave"`, `"slide"`. Newline-separated `rotating_text` becomes the rotating words list.

### price-table

When to use: Pricing card with currency, price, period, sub-heading, a list of feature rows with checkmarks, a CTA button, optional ribbon ("Popular"), and footer note — replaces a manually-built feature-list card.

Pro/free: Pro

```json
{
  "id": "pt001",
  "elType": "widget",
  "widgetType": "price-table",
  "settings": {
    "heading": "Standard Plan",
    "sub_heading": "For small businesses",
    "heading_tag": "h3",
    "currency_symbol": "dollar",
    "currency_position": "before",
    "currency_vertical_position": "top",
    "price": "39.99",
    "original_price": "59",
    "original_price_vertical_position": "bottom",
    "fractional_part_vertical_position": "top",
    "period": "Monthly",
    "period_position": "below",
    "features_list": [
      {"_id": "f1", "item_text": "Up to 10 notarizations", "selected_item_icon": {"value": "far fa-check-circle", "library": "fa-regular"}},
      {"_id": "f2", "item_text": "Email support", "selected_item_icon": {"value": "far fa-check-circle", "library": "fa-regular"}},
      {"_id": "f3", "item_text": "Same-day appointments", "selected_item_icon": {"value": "far fa-check-circle", "library": "fa-regular"}}
    ],
    "list_divider": "yes",
    "divider_style": "solid",
    "divider_color": "#ddd",
    "divider_weight": {"unit": "px", "size": 2},
    "divider_gap": {"unit": "px", "size": 15},
    "button_text": "Choose Plan",
    "link": {"url": "https://example.com/signup"},
    "button_size": "md",
    "footer_additional_info": "Cancel anytime",
    "show_ribbon": "yes",
    "ribbon_title": "Popular",
    "ribbon_text_color": "#ffffff"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `currency_symbol` accepts named values: `"dollar"`, `"euro"`, `"pound"`, `"yen"`, etc., or `"custom"` (then set `currency_symbol_custom`). `features_list[]._id` is an 8-char-ish hex string. Strikethrough on `original_price` is automatic. `show_ribbon: "yes"` displays the corner ribbon.

### price-list

When to use: Menu-style list of items with title, description, price, and optional dotted leader separator — restaurant menu, services pricing, etc.

Pro/free: Pro

```json
{
  "id": "pl001",
  "elType": "widget",
  "widgetType": "price-list",
  "settings": {
    "price_list": [
      {"_id": "i1", "title": "Single signature", "item_description": "One notarized signature on a document", "price": "$20", "link": {"url": "#"}},
      {"_id": "i2", "title": "Loan signing", "item_description": "Full loan-document package", "price": "$150", "link": {"url": "#"}},
      {"_id": "i3", "title": "Travel surcharge", "item_description": "Per 10 miles beyond service area", "price": "$5", "link": {"url": "#"}}
    ],
    "title_tag": "span",
    "description_tag": "p",
    "image_size_size": "thumbnail",
    "vertical_align": "top",
    "row_gap": {"unit": "px", "size": 20},
    "image_spacing": {"unit": "px", "size": 20},
    "separator_style": "dotted",
    "separator_weight": {"unit": "px", "size": 2}
  },
  "elements": [],
  "isInner": false
}
```

Notes: Each item can also carry an optional `image` field. `separator_style`: `"solid"`, `"dotted"`, `"dashed"`, `"double"`, `"none"`.

### flip-box

When to use: Two-sided card that flips on hover/click to reveal back content — front shows icon/image + title, back shows description + button.

Pro/free: Pro

```json
{
  "id": "fb001",
  "elType": "widget",
  "widgetType": "flip-box",
  "settings": {
    "graphic_element": "icon",
    "selected_icon": {"value": "fas fa-star", "library": "fa-solid"},
    "icon_view": "default",
    "icon_shape": "circle",
    "title_text_a": "Reliable Service",
    "description_text_a": "Hover to learn more",
    "title_text_b": "Reliable Service",
    "description_text_b": "Available 24/7, mobile to your location, fully insured and bonded.",
    "button_text": "Book Now",
    "link_click": "button",
    "title_tag": "h3",
    "description_tag": "div",
    "flip_effect": "flip",
    "flip_direction": "up",
    "alignment_a": "center",
    "alignment_b": "center",
    "button_size": "sm"
  },
  "elements": [],
  "isInner": false
}
```

Notes: All `*_a` keys configure the FRONT face, all `*_b` keys configure the BACK face — these are paired groups, not separate sections. `flip_effect`: `"flip"`, `"slide"`, `"push"`, `"zoom-in"`, `"zoom-out"`, `"fade"`. `flip_direction`: `"up"`, `"down"`, `"left"`, `"right"`. `graphic_element`: `"icon"`, `"image"`, or `"none"`.

### slides

When to use: Full-bleed hero slider — each slide has a background image/color, overlay, heading, description, and CTA button. Use for marketing hero rotators.

Pro/free: Pro

```json
{
  "id": "sld001",
  "elType": "widget",
  "widgetType": "slides",
  "settings": {
    "slides_name": "Slides",
    "slides": [
      {
        "_id": "s1",
        "heading": "Slide 1 Heading",
        "description": "Lorem ipsum dolor sit amet consectetur adipiscing elit.",
        "button_text": "Learn More",
        "background_color": "#833ca3",
        "background_size": "cover",
        "background_overlay_color": "rgba(0,0,0,0.5)",
        "zoom_direction": "in",
        "link_click": "slide"
      }
    ],
    "slides_height": {"unit": "px", "size": 400},
    "slides_title_tag": "div",
    "slides_description_tag": "div",
    "navigation": "both",
    "autoplay": "yes",
    "autoplay_speed": 5000,
    "pause_on_hover": "yes",
    "pause_on_interaction": "yes",
    "infinite": "yes",
    "transition": "slide",
    "transition_speed": 500,
    "content_animation": "fadeInUp",
    "content_max_width": {"unit": "%", "size": 66},
    "slides_horizontal_position": "center",
    "slides_vertical_position": "middle",
    "slides_text_align": "center",
    "button_size": "sm"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `slides[]` is a repeater — keep one representative slide and duplicate as needed (each slide needs a unique `_id`). Each slide carries its own background, copy, and button. `transition`: `"slide"` or `"fade"`. `navigation`: `"arrows"`, `"dots"`, `"both"`, or `"none"`. `link_click`: `"slide"` makes the whole slide clickable, `"button"` only the button.

### media-carousel

When to use: Image (or image + video) carousel with thumbnail navigation, lightbox icon, captions, autoplay — a richer alternative to `image-carousel`.

Pro/free: Pro

```json
{
  "id": "mc001",
  "elType": "widget",
  "widgetType": "media-carousel",
  "settings": {
    "skin": "carousel",
    "slides_name": "Slides",
    "slides": [
      {"_id": "m1", "type": "image", "image": {"url": "https://example.com/wp-content/uploads/photo-1.jpg"}}
    ],
    "effect": "slide",
    "thumbs_ratio": "219",
    "show_arrows": "yes",
    "pagination": "bullets",
    "speed": 500,
    "autoplay": "yes",
    "autoplay_speed": 5000,
    "loop": "yes",
    "pause_on_hover": "yes",
    "pause_on_interaction": "yes",
    "caption": "title",
    "icon": "search-plus",
    "overlay_animation": "fade",
    "image_size_size": "full",
    "space_between": {"unit": "px", "size": 10},
    "arrows_size": {"unit": "px", "size": 20},
    "pagination_position": "outside"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `skin`: `"carousel"` (one item) or `"coverflow"` (3D stack). Each slide's `type` is `"image"` or `"video"`. `caption`: `"title"`, `"caption"`, `"description"`, or `"none"` — read from the WordPress media library. `effect`: `"slide"`, `"fade"`, `"cube"`, `"coverflow"`, `"flip"`.

### lottie

When to use: SVG/JSON animation player (e.g., from LottieFiles) — used for animated hero illustrations, scroll-driven icons, hover micro-interactions.

Pro/free: Pro

```json
{
  "id": "lot001",
  "elType": "widget",
  "widgetType": "lottie",
  "settings": {
    "source": "media_file",
    "source_json": {"url": "https://example.com/wp-content/uploads/animation.json"},
    "align": "center",
    "trigger": "arriving_to_viewport",
    "viewport": {"unit": "%", "sizes": {"start": 0, "end": 100}},
    "effects_relative_to": "viewport",
    "on_hover_out": "default",
    "hover_area": "animation",
    "play_speed": {"unit": "px", "size": 1},
    "start_point": {"unit": "%", "size": 0},
    "end_point": {"unit": "%", "size": 100},
    "renderer": "svg",
    "caption_source": "none",
    "link_to": "none"
  },
  "elements": [],
  "isInner": false
}
```

Notes: Requires a valid Lottie/Bodymovin JSON animation file. `source`: `"media_file"` (upload to WP media library) or `"external_url"` (then use `source_external_url`). `trigger`: `"arriving_to_viewport"`, `"on_hover"`, `"on_click"`, `"bind_to_scroll"`. `renderer`: `"svg"` (sharper, slower) or `"canvas"` (faster, blurrier).

### google_maps

When to use: Embedded Google Map showing a location — by street address or by coordinates.

Pro/free: free

```json
{
  "id": "gm001",
  "elType": "widget",
  "widgetType": "google_maps",
  "settings": {
    "address": "London Eye, London, United Kingdom",
    "zoom": {"unit": "px", "size": 10}
  },
  "elements": [],
  "isInner": false
}
```

Notes: The free version uses Google's no-key iframe embed with `address` text — no API key needed, but no custom styling, no markers, no lat/lng. For coordinates or markers, swap to a Pro maps widget or a third-party plugin. `zoom.size` is the Google Maps zoom level (1 = world, 20 = building).

### nested-tabs

When to use: The modern container-based tabs widget — each tab title in `tabs[]` is paired by index with an `isLocked: true` inner container in `elements[]` that holds the tab's body content.

Pro/free: free (since Elementor 3.x — replaces the legacy `tabs` widget)

```json
{
  "id": "ntab001",
  "elType": "widget",
  "widgetType": "nested-tabs",
  "settings": {
    "tabs": [
      {"_id": "tab1", "tab_title": "Overview"},
      {"_id": "tab2", "tab_title": "Pricing"},
      {"_id": "tab3", "tab_title": "FAQ"}
    ],
    "horizontal_scroll": "disable",
    "breakpoint_selector": "mobile"
  },
  "elements": [
    {
      "id": "tab1body",
      "elType": "container",
      "settings": {"_title": "Overview", "content_width": "full"},
      "elements": [
        {"id": "tab1txt", "elType": "widget", "widgetType": "text-editor", "settings": {"editor": "<p>Overview content.</p>"}, "elements": [], "isInner": false}
      ],
      "isInner": true,
      "isLocked": true
    }
  ],
  "isInner": false
}
```

Notes: **Same paired-array rule as nested-accordion** — `tabs[]` length must equal the count of `isLocked: true` containers in `elements[]`, in the same order. Index-paired, not ID-paired. Forgetting `isLocked: true` causes the body container to render as a sibling instead of the tab's content. `breakpoint_selector` controls when tabs collapse to a stacked accordion-like layout (`"mobile"`, `"tablet"`, `"none"`).

### nested-carousel

When to use: Carousel where each slide is a full container you build with any widgets inside — testimonial cards, feature blocks, etc. Differs from `media-carousel` (images only) in that slides hold arbitrary content.

Pro/free: free (3.x containers feature)

```json
{
  "id": "ncar001",
  "elType": "widget",
  "widgetType": "nested-carousel",
  "settings": {
    "carousel_name": "Carousel",
    "carousel_items": [
      {"_id": "ci1", "slide_title": "Slide #1"},
      {"_id": "ci2", "slide_title": "Slide #2"},
      {"_id": "ci3", "slide_title": "Slide #3"}
    ],
    "equal_height": "yes",
    "autoplay": "yes",
    "autoplay_speed": 5000,
    "pause_on_hover": "yes",
    "pause_on_interaction": "yes",
    "infinite": "yes",
    "speed": 500,
    "direction": "ltr",
    "offset_sides": "none",
    "arrows": "yes",
    "navigation_previous_icon": {"value": "eicon-chevron-left", "library": "eicons"},
    "navigation_next_icon": {"value": "eicon-chevron-right", "library": "eicons"},
    "pagination": "bullets",
    "image_spacing_custom": {"unit": "px", "size": 10}
  },
  "elements": [
    {
      "id": "ci1cont",
      "elType": "container",
      "settings": {"_title": "Slide #1", "content_width": "full"},
      "elements": [
        {"id": "ci1h", "elType": "widget", "widgetType": "heading", "settings": {"title": "Card one"}, "elements": [], "isInner": false}
      ],
      "isInner": true,
      "isLocked": true
    }
  ],
  "isInner": false
}
```

Notes: **Same paired-array rule as nested-tabs and nested-accordion** — `carousel_items[]` length must equal the count of `isLocked: true` containers in `elements[]`. Each `isLocked` container holds the actual slide content. `direction`: `"ltr"` or `"rtl"`. `offset_sides`: `"none"`, `"start"`, `"end"`, `"both"` — shows a peek of adjacent slides.

### progress-tracker

When to use: Scroll-progress indicator at the top or side of the page (NOT a progress bar widget) — shows reading progress through a page or section.

Pro/free: Pro

```json
{
  "id": "pt001",
  "elType": "widget",
  "widgetType": "progress-tracker",
  "settings": {
    "type": "horizontal",
    "relative_to": "entire_page",
    "percentage": "no",
    "horizontal_border_style": "none",
    "horizontal_tracker_border_style": "none"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `type`: `"horizontal"`, `"vertical"`, or `"circle"`. `relative_to`: `"entire_page"` or `"section_or_column"` (anchor it to a specific section instead of the whole page). `percentage: "yes"` displays a numeric percentage label. For a static "progress-bar" style widget (filled bar at fixed percent for skills/stats), use the separate `progress` widget — this one is scroll-driven.

### social-icons

When to use: Row of branded social-network icons linking out to profiles (Facebook, X, Instagram, YouTube, LinkedIn, etc.).

Pro/free: free

```json
{
  "id": "soc001",
  "elType": "widget",
  "widgetType": "social-icons",
  "settings": {
    "social_icon_list": [
      {"_id": "s1", "social_icon": {"value": "fab fa-facebook", "library": "fa-brands"}, "link": {"url": "https://facebook.com/yourpage", "is_external": "true"}, "item_icon_color": "default"},
      {"_id": "s2", "social_icon": {"value": "fab fa-x-twitter", "library": "fa-brands"}, "link": {"url": "https://x.com/yourhandle", "is_external": "true"}, "item_icon_color": "default"},
      {"_id": "s3", "social_icon": {"value": "fab fa-youtube", "library": "fa-brands"}, "link": {"url": "https://youtube.com/@yourchannel", "is_external": "true"}, "item_icon_color": "default"}
    ],
    "shape": "rounded",
    "columns": "0",
    "align": "center",
    "icon_color": "default",
    "icon_spacing": {"unit": "px", "size": 5},
    "row_gap": {"unit": "px", "size": 0}
  },
  "elements": [],
  "isInner": false
}
```

Notes: `item_icon_color: "default"` uses each brand's official color (Facebook blue, YouTube red, etc.); change to `"custom"` per item plus `item_icon_primary_color` for a unified tint. `shape`: `"rounded"`, `"square"`, `"circle"`. `columns: "0"` means auto-wrap; otherwise number of columns. Use `fa-brands` library for brand glyphs.

### hotspot

When to use: Interactive image with clickable/hoverable hotspot markers — each marker reveals a tooltip with extra info. Used for annotated product shots, floor plans, infographics.

Pro/free: Pro

```json
{
  "id": "hs001",
  "elType": "widget",
  "widgetType": "hotspot",
  "settings": {
    "image": {"url": "https://example.com/wp-content/uploads/base.jpg"},
    "image_size": "large",
    "object-position": "center center",
    "hotspot": [
      {
        "_id": "h1",
        "hotspot_label": "Detail A",
        "hotspot_icon": {"value": "fas fa-plus", "library": "fa-solid"},
        "hotspot_icon_position": "start",
        "hotspot_tooltip_content": "More info about this point.",
        "hotspot_horizontal": "left",
        "hotspot_offset_x": {"unit": "%", "size": 30},
        "hotspot_vertical": "top",
        "hotspot_offset_y": {"unit": "%", "size": 40}
      }
    ],
    "hotspot_animation": "e-hotspot--expand",
    "hotspot_sequenced_animation": "no",
    "tooltip_position": "top",
    "tooltip_trigger": "click",
    "tooltip_animation": "e-hotspot--fade-in-out"
  },
  "elements": [],
  "isInner": false
}
```

Notes: Each `hotspot[]` item is one marker — position with `hotspot_horizontal` (`"left"` or `"right"`) + `hotspot_offset_x` plus `hotspot_vertical` (`"top"` or `"bottom"`) + `hotspot_offset_y`, both as percentages of the image. `tooltip_trigger`: `"hover"` or `"click"`. `tooltip_position`: `"top"`, `"bottom"`, `"left"`, `"right"`. `hotspot_animation`: pulse/expand effect on the marker itself. Each marker can be a label OR an icon — set whichever; the other stays empty.

### image-carousel

When to use: Simple image-only carousel — a row of images with arrows/dots, autoplay, optional lightbox. Lighter than `media-carousel` (no video, no thumbnails strip, no per-image caption variants).

Pro/free: free

```json
{
  "id": "ic001",
  "elType": "widget",
  "widgetType": "image-carousel",
  "settings": {
    "carousel_name": "Image Carousel",
    "carousel": [
      {"_id": "i1", "id": 0, "url": "https://example.com/wp-content/uploads/slide-1.jpg"},
      {"_id": "i2", "id": 0, "url": "https://example.com/wp-content/uploads/slide-2.jpg"},
      {"_id": "i3", "id": 0, "url": "https://example.com/wp-content/uploads/slide-3.jpg"}
    ],
    "thumbnail_size": "thumbnail",
    "image_stretch": "no",
    "navigation": "both",
    "link_to": "none",
    "open_lightbox": "default",
    "autoplay": "yes",
    "pause_on_hover": "yes",
    "pause_on_interaction": "yes",
    "autoplay_speed": 5000,
    "infinite": "yes",
    "effect": "slide",
    "speed": 500,
    "direction": "ltr",
    "arrows_position": "inside",
    "dots_position": "outside",
    "image_spacing_custom": {"unit": "px", "size": 20},
    "caption_align": "center"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `carousel[]` is the image repeater — each item points at a WP-uploaded image. `navigation`: `"arrows"`, `"dots"`, `"both"`, `"none"`. `effect`: `"slide"` or `"fade"`. `link_to`: `"none"`, `"file"` (opens lightbox), or `"custom"` (then per-image link). `image_stretch: "yes"` forces all images to the widget's full width regardless of native size. Distinct from `media-carousel` (Pro — adds video, thumbnails, and skin variants) and from `nested-carousel` (build-your-own slide containers holding arbitrary widgets, not just images).

### reviews

When to use: Carousel of review/testimonial cards with avatar, name, handle, content, star rating, and a social network badge. Pre-styled for social-proof rows.

Pro/free: Pro

```json
{
  "id": "rv001",
  "elType": "widget",
  "widgetType": "reviews",
  "settings": {
    "slides_name": "Slides",
    "slides": [
      {
        "_id": "r1",
        "name": "Jane Doe",
        "title": "@janedoe",
        "content": "Excellent service — fast, friendly, and reliable.",
        "image": {"url": "https://example.com/wp-content/uploads/avatar.jpg"},
        "rating": "5",
        "selected_social_icon": {"value": "fab fa-twitter", "library": "fa-brands"},
        "link": {"url": "https://twitter.com/janedoe"}
      }
    ],
    "show_arrows": "yes",
    "pagination": "bullets",
    "speed": 500,
    "autoplay": "yes",
    "autoplay_speed": 5000,
    "loop": "yes",
    "pause_on_hover": "yes",
    "pause_on_interaction": "yes",
    "image_size_size": "full",
    "space_between": {"unit": "px", "size": 10},
    "show_separator": "has-separator",
    "icon_color": "default",
    "star_style": "star_fontawesome",
    "unmarked_star_style": "solid"
  },
  "elements": [],
  "isInner": false
}
```

Notes: **Data source is the inline `slides[]` repeater you author in the widget settings** — it does NOT pull from WooCommerce product reviews, the WP comments table, or any external API. Each `slides[]` item is one review card — name/title/content plus an optional avatar `image`, a `rating` (string, e.g. `"5"`), and a `selected_social_icon` brand badge. `icon_color: "default"` uses the brand's color (twitter blue, etc.); set `"custom"` + `icon_primary_color` for a unified tint. `star_style`: `"star_fontawesome"` or `"star_unicode"`. Distinct from `testimonial` (single free card with no carousel) and `nested-carousel` (general-purpose container slider you'd hand-build to match this look).

### e-paragraph

When to use: Elementor's newer atomic paragraph widget, gradually replacing `text-editor` for short prose blocks. Stores content in the v3 typed-value format.

Pro/free: free (Elementor 3.x atomic widgets)

```json
{
  "id": "ep001",
  "elType": "widget",
  "widgetType": "e-paragraph",
  "settings": {
    "paragraph": {
      "$$type": "html-v3",
      "value": {
        "content": {"$$type": "string", "value": "Your paragraph text here."}
      }
    },
    "tag": {"$$type": "string", "value": "p"}
  },
  "elements": [],
  "isInner": false
}
```

Notes: **This is the new atomic widget format replacing `text-editor`** — uses the `$$type` typed-value envelope instead of raw strings. `tag` supports `"p"`, `"span"`, `"div"`. For most page content the legacy `text-editor` widget (with HTML `editor` string) is still the safer bet — `e-paragraph` is still rolling out and styling controls are leaner. Migrate when you need very tight per-element styling that text-editor's WYSIWYG can't express cleanly.

### code-highlight

When to use: Code snippet block with syntax highlighting, line numbers, and a copy-to-clipboard button — for technical/developer docs pages.

Pro/free: Pro

```json
{
  "id": "ch001",
  "elType": "widget",
  "widgetType": "code-highlight",
  "settings": {
    "language": "javascript",
    "code": "console.log('Code is Poetry');",
    "line_numbers": "line-numbers",
    "copy_to_clipboard": "copy-to-clipboard",
    "theme": "default"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `code` holds the raw snippet as a string — escape backslashes and embedded quotes correctly for JSON. `language` controls Prism highlighting (`"javascript"`, `"typescript"`, `"php"`, `"css"`, `"html"`, `"python"`, `"bash"`, `"json"`, `"sql"`, etc.). `line_numbers` and `copy_to_clipboard` are toggle-style strings: the slug enables, empty string disables. `theme`: `"default"`, `"okaidia"`, `"twilight"`, `"coy"`, `"dark"`, `"funky"`, `"solarizedlight"`, `"tomorrow"`.

### blockquote

When to use: Styled pull-quote with optional attribution and a tweet button — for marketing pages or article callouts that want more presence than a plain `<blockquote>`.

Pro/free: Pro

```json
{
  "id": "bqt001",
  "elType": "widget",
  "widgetType": "blockquote",
  "settings": {
    "blockquote_skin": "border",
    "blockquote_content": "Lorem ipsum dolor sit amet, consectetur adipiscing elit.",
    "author_name": "John Doe",
    "author_image": {"url": "https://example.com/wp-content/uploads/jdoe.jpg"},
    "tweet_button": "yes",
    "tweet_button_view": "icon-text",
    "tweet_button_skin": "classic",
    "tweet_button_label": "Tweet",
    "url_type": "current_page"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `blockquote_skin`: `"border"`, `"quotation"`, `"box"`, `"clean"`. Set `tweet_button: "yes"` to render the share button, then `tweet_button_view` controls icon-only vs `"icon-text"`, `tweet_button_skin` (`"classic"` / `"bubble"` / `"link"`) controls its style, and `tweet_button_label` is the visible text. `author_name` and `author_image` provide attribution; `url_type: "current_page"` makes the tweet share the page URL (use `"custom"` + `url` to override).

### share-buttons

When to use: Row of social-share buttons (Facebook, Twitter/X, LinkedIn, WhatsApp, Pinterest, etc.) for blog posts and landing pages.

Pro/free: Pro

```json
{
  "id": "shr001",
  "elType": "widget",
  "widgetType": "share-buttons",
  "settings": {
    "share_buttons": [
      {"button": "facebook", "_id": "b49dfd0", "text": ""},
      {"button": "x-twitter", "_id": "00568af", "text": ""},
      {"button": "linkedin", "_id": "31e5103", "text": ""}
    ],
    "view": "icon-text",
    "show_label": "yes",
    "skin": "gradient",
    "shape": "square",
    "share_url_type": "current_page",
    "color_source": "official",
    "column_gap": {"unit": "px", "size": 10},
    "row_gap": {"unit": "px", "size": 10}
  },
  "elements": [],
  "isInner": false
}
```

Notes: `share_buttons[]` is an array of platforms — common `button` values: `"facebook"`, `"x-twitter"` (formerly `"twitter"`), `"linkedin"`, `"pinterest"`, `"whatsapp"`, `"telegram"`, `"reddit"`, `"email"`, `"pocket"`, `"tumblr"`. Each entry needs a stable `_id`. `view` controls icon-only (`"icon"`) vs `"icon-text"`. `share_url_type`: `"current_page"` shares the current URL; `"custom"` lets you set `share_url`. `skin`: `"flat"`, `"gradient"`, `"framed"`, `"boxed"`, `"minimal"`. `color_source: "official"` uses brand colors; `"custom"` lets you pick.

### template

When to use: Embed a saved Elementor template (a designed section/block stored in WP) inline at this spot — acts like an "include" so the same designed footer-CTA, pricing block, or testimonial row can be reused across many pages.

Pro/free: Pro

```json
{
  "id": "tpl001",
  "elType": "widget",
  "widgetType": "template",
  "settings": {
    "template_id": "1234"
  },
  "elements": [],
  "isInner": false
}
```

Notes: Embeds a saved Elementor template by ID — set `template_id` to the WP post ID of the saved template (Templates → Saved Templates in WP admin). This is how you reuse a designed section across multiple pages without copy-pasting JSON; edits to the source template propagate everywhere it's embedded. **Distinct from `loop-grid`'s `template_id`**, which points at a per-item loop-item template — this widget's `template_id` embeds the WHOLE template inline, once.

