# Pro dynamic widgets

These widgets render content from a query (posts, custom post types, taxonomies) using a referenced inner-card template. They each require:
1. A separate "loop item" template you build first (in Elementor → Templates → Saved Templates)
2. The template's ID set as `template_id` (or equivalent) on the widget
3. Query parameters (`posts_post_type`, `posts_number`, taxonomy filters)

They will not render any content until both the inner template exists and the query returns results.

## widgets

### loop-grid

When to use: Dynamic grid of WordPress posts/CPT items, each rendered by a separately-built loop-item template. The Pro equivalent of `posts` but with full custom layout control instead of fixed skins.

Pro/free: Pro

```json
{
  "id": "lg001",
  "elType": "widget",
  "widgetType": "loop-grid",
  "settings": {
    "_skin": "post",
    "template_id": "1234",
    "columns": "3",
    "columns_tablet": "2",
    "columns_mobile": "1",
    "posts_per_page": 6,
    "edit_handle_selector": "[data-elementor-type=\"loop-item\"]",
    "post_query_post_type": "post",
    "post_query_orderby": "post_date",
    "post_query_order": "desc",
    "post_query_ignore_sticky_posts": "yes",
    "pagination_load_type": "page_reload",
    "pagination_page_limit": "5",
    "pagination_prev_label": "Previous",
    "pagination_next_label": "Next",
    "pagination_align": "center",
    "text": "Load More",
    "load_more_button_align": "center",
    "load_more_no_posts_custom_message": "No more posts to show",
    "nothing_found_message_text": "It seems we can't find what you're looking for.",
    "nothing_found_message_html_tag": "div"
  },
  "elements": [],
  "isInner": false
}
```

Notes: **Requires a separately-built loop-item template.** `template_id` is the WP post ID of that template — without it the grid renders empty. `pagination_load_type`: `"page_reload"`, `"load_more_on_click"`, `"load_more_infinite_scroll"`, or `"none"`. `_skin` is typically `"post"`; `"taxonomy"` skin queries terms instead of posts. `alternate_templates[]` (optional repeater) lets you swap a different template at certain positions — leave out unless you actually need it.

### loop-carousel

When to use: Dynamic carousel that loops over WordPress posts/CPT items, rendering each using a separately-built loop-item template. Combines a query with a carousel layout.

Pro/free: Pro

```json
{
  "id": "lc001",
  "elType": "widget",
  "widgetType": "loop-carousel",
  "settings": {
    "_skin": "post",
    "template_id": "1234",
    "posts_per_page": 6,
    "slides_to_show": "3",
    "slides_to_scroll": "1",
    "equal_height": "yes",
    "post_query_post_type": "post",
    "post_query_orderby": "post_date",
    "post_query_order": "desc",
    "post_query_ignore_sticky_posts": "yes",
    "autoplay": "yes",
    "autoplay_speed": 5000,
    "pause_on_hover": "yes",
    "infinite": "yes",
    "speed": 500,
    "arrows": "yes",
    "pagination": "bullets"
  },
  "elements": [],
  "isInner": false
}
```

Notes: **Requires a separately-built loop-item template.** `template_id` references that template's WP post ID — you cannot inline the item layout here. Workflow: (1) create a "Loop Item" template in Elementor → Templates, (2) note its ID, (3) paste it as `template_id`. `post_query_post_type`: `"post"`, `"page"`, or any registered CPT slug. Without a valid `template_id`, the widget renders empty.

### posts

When to use: Grid/list of WordPress posts (blog index, recent articles section). Has multiple skin presets — `classic`, `cards`, `full_content` — each with its own typography/layout settings.

Pro/free: Pro

```json
{
  "id": "psts001",
  "elType": "widget",
  "widgetType": "posts",
  "settings": {
    "_skin": "classic",
    "classic_columns": "3",
    "classic_posts_per_page": 6,
    "classic_thumbnail": "top",
    "classic_thumbnail_size_size": "medium",
    "classic_item_ratio": {"unit": "px", "size": 0.66},
    "classic_show_title": "yes",
    "classic_title_tag": "h3",
    "classic_show_excerpt": "yes",
    "classic_excerpt_length": 25,
    "classic_meta_data": ["date", "comments"],
    "classic_meta_separator": " / ",
    "classic_show_read_more": "yes",
    "classic_read_more_text": "Read More »",
    "posts_post_type": "post",
    "posts_orderby": "post_date",
    "posts_order": "desc",
    "posts_ignore_sticky_posts": "yes",
    "classic_column_gap": {"unit": "px", "size": 30},
    "classic_row_gap": {"unit": "px", "size": 35}
  },
  "elements": [],
  "isInner": false
}
```

Notes: `_skin` chooses the layout: `"classic"`, `"cards"`, or `"full_content"`. **Each skin namespaces its own settings** — `classic_*` keys only apply when `_skin: "classic"`; switch to cards and you'd use `cards_columns`, `cards_posts_per_page`, etc. Don't mix prefixes. Unlike `loop-carousel`, this widget uses built-in skins, not a custom loop-item template. For full layout control, use `loop-grid` instead.

### portfolio

When to use: Filterable masonry/grid of posts (or CPT items) with category filters at top — built for showcasing project work, case studies, gallery items.

Pro/free: Pro

```json
{
  "id": "pf001",
  "elType": "widget",
  "widgetType": "portfolio",
  "settings": {
    "columns": "3",
    "posts_per_page": 6,
    "thumbnail_size_size": "medium",
    "item_ratio": {"unit": "px", "size": 0.66},
    "show_title": "yes",
    "title_tag": "h3",
    "posts_post_type": "post",
    "posts_orderby": "post_date",
    "posts_order": "desc",
    "posts_ignore_sticky_posts": "yes",
    "filter_item_spacing": {"unit": "px", "size": 10},
    "filter_spacing": {"unit": "px", "size": 10}
  },
  "elements": [],
  "isInner": false
}
```

Notes: Filter tabs (built from the queried post-type's taxonomy terms) auto-render above the grid. `posts_post_type` can be `"post"` or any CPT slug; the filter taxonomy is inferred from the post type. Unlike `posts` and `loop-carousel`, this widget has a fixed card layout — no skin choice and no separately-built loop-item template.

### taxonomy-filter

When to use: Standalone filter-button row that filters a sibling `loop-grid` or `loop-carousel` on the same page by taxonomy terms — for "Filter by Category" UI above a portfolio/blog loop.

Pro/free: Pro

```json
{
  "id": "txf001",
  "elType": "widget",
  "widgetType": "taxonomy-filter",
  "settings": {
    "taxonomy": "category",
    "direction": "horizontal",
    "multiple_selection": "no",
    "logical_combination": "AND",
    "show_empty_items": "no",
    "show_child_taxonomy": "no",
    "child_taxonomy_depth": "1",
    "show_first_item": "yes",
    "first_item_title": "All",
    "horizontal_scroll": "disable"
  },
  "elements": [],
  "isInner": false
}
```

Notes: **PAIRED widget** — it filters a sibling `loop-grid` or `loop-carousel` placed on the same page (no explicit target setting; the link is by-page). Set `taxonomy` to the slug of a registered taxonomy (`"category"`, `"post_tag"`, or any custom-taxonomy slug — e.g. `"product_cat"`, `"portfolio_category"`). `show_first_item: "yes"` adds the "All" reset button (label set via `first_item_title`). `direction`: `"horizontal"` or `"vertical"`. `multiple_selection: "yes"` lets users pick several terms at once (combined with `logical_combination` = `"AND"` or `"OR"`). The target loop widget must be on the same page — if no loop-grid/loop-carousel exists alongside, the filter renders but has nothing to control.
