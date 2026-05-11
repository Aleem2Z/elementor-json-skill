# Pro navigation and utility widgets

Widgets that handle navigation menus, side panels, search, and time-bound utility blocks.

For these widgets specifically, **they often render best as Header/Footer template parts** (Elementor → Templates → Theme Builder) rather than inline in a page. nav-menu, mega-menu, and off-canvas in particular almost always live in a Header template. The user can still import the template part as a regular .json and convert.

## widgets

### nav-menu

When to use: Standard horizontal/vertical site navigation tied to a WordPress menu (Appearance → Menus). Use in a Header template — not inline in pages.

Pro/free: Pro

```json
{
  "id": "nm001",
  "elType": "widget",
  "widgetType": "nav-menu",
  "settings": {
    "menu_name": "Menu",
    "layout": "horizontal",
    "pointer": "underline",
    "submenu_icon": {"value": "fas fa-caret-down", "library": "fa-solid"},
    "dropdown": "tablet",
    "text_align": "aside",
    "toggle": "burger",
    "toggle_align": "center",
    "color_menu_item_hover_pointer_bg": "#fff",
    "nav_menu_divider_style": "solid"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `menu` (string slug of a WP menu) is the live data source — if omitted, Elementor falls back to the first registered menu. `layout`: `"horizontal"`, `"vertical"`, `"dropdown"`. `pointer` (active-item indicator): `"underline"`, `"overline"`, `"double-line"`, `"framed"`, `"background"`, `"text"`, `"none"`. `dropdown` (mobile breakpoint where it collapses to a burger): `"mobile"`, `"tablet"`, `"none"`. `toggle`: `"burger"` or `"none"`.

### mega-menu

When to use: Full-width dropdown menu where each top-level item opens a designed panel (multi-column lists, images, CTAs) instead of a plain sub-menu. Header template only — does not work well inline.

Pro/free: Pro

```json
{
  "id": "mm001",
  "elType": "widget",
  "widgetType": "mega-menu",
  "settings": {
    "menu_name": "Menu",
    "menu_items": [
      {"_id": "mi1", "item_title": "Item #1", "item_dropdown_content": "no"},
      {"_id": "mi2", "item_title": "Item #2", "item_dropdown_content": "no"},
      {"_id": "mi3", "item_title": "Item #3", "item_dropdown_content": "no"}
    ],
    "content_width": "full_width",
    "content_horizontal_position": "center",
    "item_layout": "horizontal",
    "menu_item_icon": {"value": "fas fa-caret-down", "library": "fa-solid"},
    "menu_item_icon_active": {"value": "fas fa-caret-up", "library": "fa-solid"},
    "open_on": "hover",
    "open_animation_duration": {"unit": "ms", "size": 500},
    "horizontal_scroll": "disable",
    "breakpoint_selector": "tablet"
  },
  "elements": [],
  "isInner": false
}
```

Notes: Unlike `nav-menu`, items here are inline (in `menu_items[]`), not tied to a WP menu. Each item where `item_dropdown_content: "yes"` opens a **separately-built dropdown content template** referenced by `item_dropdown_template` (a saved-template ID) — the dropdown HTML is not inlined inside this widget's `elements[]`. `open_on`: `"hover"` or `"click"`. `breakpoint_selector` (where it collapses to a burger): `"mobile"`, `"tablet"`, `"none"`. Distinct from `nav-menu` (simple linear menu) and `nested-tabs` (tabbed content inside a single widget).

### off-canvas

When to use: Slide-out side panel (left, right, top, bottom) holding navigation, search, contact info — typically triggered by a burger button in the header on mobile.

Pro/free: Pro

```json
{
  "id": "oc001",
  "elType": "widget",
  "widgetType": "off-canvas",
  "settings": {
    "off_canvas_name": "Off-Canvas",
    "height": "custom",
    "offcanvas_animation_duration": {"unit": "s", "size": 1.5},
    "wrapper_html_tag": "div",
    "has_overlay": "yes",
    "overlay_background_background": "classic",
    "overlay_background_color": "rgba(0,0,0,.8)"
  },
  "elements": [
    {
      "id": "ocbody",
      "elType": "container",
      "settings": {"content_width": "full", "container_type": "flex"},
      "elements": [
        {"id": "ocnm", "elType": "widget", "widgetType": "nav-menu", "settings": {"menu_name": "Menu", "layout": "vertical"}, "elements": [], "isInner": false}
      ],
      "isInner": true,
      "isLocked": true
    }
  ],
  "isInner": false
}
```

Notes: **Nested-widget pattern** — the panel body lives in `elements[]` as an `isLocked: true` inner container; put any widgets you want inside (typically `nav-menu`, search, social-icons). The trigger button is a separate widget on the page — either an `icon` widget or a `button`, with its link set to `#elementor-off-canvas-<this-widget-id>` (or use Elementor's "Open Off-Canvas" link action). `height: "custom"` with no explicit value uses the default 100% viewport. `has_overlay: "yes"` dims the page behind the panel.

### table-of-contents

When to use: Auto-generated ToC scraping headings from the page (h2–h6) into a navigable list — used on long article/blog pages.

Pro/free: Pro

```json
{
  "id": "toc001",
  "elType": "widget",
  "widgetType": "table-of-contents",
  "settings": {
    "title": "Table of Contents",
    "html_tag": "h4",
    "headings_by_tags": ["h2", "h3", "h4", "h5", "h6"],
    "marker_view": "numbers",
    "icon": {"value": "fas fa-circle", "library": "fa-solid"},
    "no_headings_message": "No headings were found on this page.",
    "minimize_box": "yes",
    "expand_icon": {"value": "fas fa-chevron-down", "library": "fa-solid"},
    "collapse_icon": {"value": "fas fa-chevron-up", "library": "fa-solid"},
    "minimized_on": "tablet",
    "hierarchical_view": "yes",
    "header_text_align": "start",
    "toggle_button_position": "row",
    "item_text_underline_hover": "yes"
  },
  "elements": [],
  "isInner": false
}
```

Notes: Headings are scraped at render time — there's nothing to manually add. `marker_view`: `"numbers"`, `"bullets"`, or `"none"`. `headings_by_tags` is an array of tag names — drop unused levels to skip them. `minimize_box: "yes"` lets the user collapse the ToC; `minimized_on` controls the breakpoint where it starts collapsed (`"tablet"`, `"mobile"`, `"none"`). `hierarchical_view: "yes"` indents nested headings. Best used inside a sticky sidebar container.

### search

When to use: Live-results search bar (typeahead) for the site — types into an input, shows post results in a dropdown or below the field.

Pro/free: Pro

```json
{
  "id": "srch001",
  "elType": "widget",
  "widgetType": "search",
  "settings": {
    "search_input_placeholder_text": "Type to start searching...",
    "icon_clear": {"value": "fas fa-times", "library": "fa-solid"},
    "submit_trigger": "click_submit",
    "submit_button_text": "Search",
    "minimum_search_characters": 3,
    "number_of_columns": 1,
    "number_of_items": 6,
    "enable_nothing_found_message": "yes",
    "nothing_found_message_text": "It seems we can't find what you're looking for.",
    "nothing_found_message_html_tag": "div",
    "search_query_post_type": "any",
    "search_query_select_date": "anytime",
    "search_query_orderby": "post_date",
    "search_query_order": "desc",
    "search_query_ignore_sticky_posts": "yes",
    "pagination_type_options": "none",
    "page_limit_settings": 5,
    "results_is_dropdown_width": "search_field"
  },
  "elements": [],
  "isInner": false
}
```

Notes: `submit_trigger`: `"click_submit"` (search runs only after Enter/click) or `"on_type"` (live as you type). `search_query_post_type`: `"any"` (all post types) or a specific slug. `results_is_dropdown_width`: `"search_field"` (matches the input) or `"custom"`. **Inline vs popup:** the default render is an inline search input that sits where you drop the widget. For a popup/overlay search, pair this widget with a button whose link action triggers a popup template (Elementor → Templates → Popups) that hosts this search widget — there's no built-in `display_mode: "popup"` flag on the widget itself. Distinct from WordPress's classic search widget — this one supports live results without a plugin.

### login

When to use: Front-end WP login form on a page (instead of `/wp-login.php`) — for members-only sites, custom login pages, or a header login dropdown.

Pro/free: Pro

```json
{
  "id": "lgn001",
  "elType": "widget",
  "widgetType": "login",
  "settings": {
    "show_labels": "yes",
    "input_size": "sm",
    "button_text": "Log In",
    "button_size": "sm",
    "show_lost_password": "yes",
    "show_remember_me": "yes",
    "show_logged_in_message": "yes",
    "user_label": "Username or Email Address",
    "user_placeholder": "Username or Email Address",
    "password_label": "Password",
    "password_placeholder": "Password",
    "row_gap": {"unit": "px", "size": 10},
    "field_background_color": "#ffffff"
  },
  "elements": [],
  "isInner": false
}
```

Notes: **This renders Elementor's branded/styled login form on the page — it is NOT a redirect to `/wp-login.php` and does not inherit `wp-login.php`'s styling.** The form posts to WordPress's standard login handler under the hood, so existing WP user accounts work without extra config. `show_logged_in_message: "yes"` hides the form for already-logged-in users and shows a "You are already logged in" message — set `logged_in_message` to customize it. `redirect_after_login` + `redirect_url` let you bounce users to a specific page on success.

### countdown

When to use: Live countdown to a fixed due date (sale ends, event start) or per-visitor evergreen countdown (X hours from first page view).

Pro/free: Pro

```json
{
  "id": "cd001",
  "elType": "widget",
  "widgetType": "countdown",
  "settings": {
    "countdown_type": "due_date",
    "due_date": "2026-06-07 09:25",
    "evergreen_counter_hours": 47,
    "evergreen_counter_minutes": 59,
    "show_days": "yes",
    "show_hours": "yes",
    "show_minutes": "yes",
    "show_seconds": "yes",
    "show_labels": "yes",
    "label_days": "Days",
    "label_hours": "Hours",
    "label_minutes": "Minutes",
    "label_seconds": "Seconds",
    "label_display": "block",
    "container_width": {"unit": "%", "size": 100},
    "box_spacing": {"unit": "px", "size": 10}
  },
  "elements": [],
  "isInner": false
}
```

Notes: `countdown_type`: `"due_date"` (fixed datetime — same for everyone) or `"evergreen"` (per-visitor: counts down from `evergreen_counter_hours/minutes` starting at their first visit, stored in a cookie). `due_date` is a WP-local string `"YYYY-MM-DD HH:MM"`. `label_display`: `"inline"` or `"block"`. Use `actions_on_expire` (e.g., `["hide", "redirect", "message"]`) to control what happens when it hits zero.
