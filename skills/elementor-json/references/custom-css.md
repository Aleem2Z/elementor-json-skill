# custom_css

`custom_css` is a per-element CSS field on Elementor's Advanced tab (Pro only — free Elementor doesn't show it, but the JSON is still respected if Pro activates later). Use it sparingly.

## Format

Add `custom_css` to the element's `settings` object. **No underscore prefix** — this differs from `_padding`/`_margin` which DO take the prefix:

```json
{
  "id": "hero001",
  "elType": "container",
  "settings": {
    "padding": {"unit": "px", "top": "60", "right": "0", "bottom": "60", "left": "0", "isLinked": false},
    "custom_css": "selector::before {\n  content: '';\n  position: absolute;\n  inset: 0;\n  background: url('data:image/svg+xml,...') no-repeat center;\n  background-size: cover;\n}"
  }
}
```

## `custom_css` vs `_custom_css`

You will see **both** keys in real-world Elementor exports — they are separate fields, not aliases:

- **`custom_css`** is the standard "Custom CSS" control on Elementor Pro's Advanced tab. This is the one to use for portable templates.
- **`_custom_css`** is added by some themes and plugins (e.g. Shelder) as an additional advanced setting alongside Elementor's. Templates from those sources may have it in addition to (or instead of) `custom_css`.

It's possible for a single element to have both keys with different content — they coexist and both render. If you're authoring templates fresh, use `custom_css` and ignore `_custom_css` unless you're targeting a specific theme that depends on it.

If you import a template that uses `_custom_css` into a site without that theme/plugin, the styles silently won't apply. Use `custom_css` for anything you want to be portable.

## The `selector` keyword

Inside `custom_css`, the literal string `selector` is replaced at render time with a CSS selector matching THIS element specifically. So `selector::before` targets the pseudo-element of this container only — no need to know its generated `.elementor-element-xxxx` class.

- `selector` → this element
- `selector::before` / `selector::after` → its pseudo-elements
- `selector .child-class` → descendant elements
- `selector:hover` → hover state
- `selector h3` → child h3 (useful for text-editor styling)
- `selector > .e-con-inner` → Elementor's internal flex wrapper for the container's children (useful when stacking decorative `::before` overlays)

## When it's justified

- **Pseudo-elements** (`::before`, `::after`) — Elementor has no native UI for these.
- **Inline SVG backgrounds** — Elementor's background image control wants a Media Library upload; for inline `data:image/svg+xml` URIs, use `custom_css`.
- **Hover transitions on properties Elementor can't animate** — e.g. `transition: box-shadow .3s ease` so the hover shadow animates rather than snapping. (Hover values themselves go in JSON settings — see `containers.md`.)
- **`backdrop-filter`, `clip-path`, complex `z-index` stacks** — outside Elementor's control surface.

## When it's wrong

- Setting padding, margin, font-size, colors, alignment, border-radius. Use the JSON settings — the Elementor controls panel and the responsive breakpoint switcher depend on those keys.
- Anything that overrides a control the user might want to edit later. The CSS wins, the control silently does nothing, and the user can't figure out why their tweak isn't applying.

## JSON escaping

JSON requires `\n` for newlines and escaped quotes. Use single quotes for CSS string values (URLs, font families) to avoid escape gymnastics:

```json
{
  "custom_css": "selector {\n  font-family: 'Inter', sans-serif;\n  background: url('data:image/svg+xml,%3Csvg ...');\n}"
}
```

Double-quoted CSS values would need `\"` escaping inside the JSON string — readable but error-prone. Single-quote everything in CSS strings; double quotes are reserved for JSON.

## Common patterns

**Decorative pseudo-element shape:**
```json
{
  "custom_css": "selector::before {\n  content: '';\n  position: absolute;\n  top: -80px;\n  left: 50%;\n  transform: translateX(-50%);\n  width: 100%;\n  max-width: 1200px;\n  height: 100px;\n  background: url('data:image/svg+xml,...') no-repeat center;\n  background-size: 100% 100%;\n}"
}
```

**Hover transition (paired with hover values in JSON):**
```json
{
  "custom_css": "selector {\n  transition: background 300ms ease, box-shadow 300ms ease, border-color 300ms ease;\n}"
}
```

**Glassmorphism backdrop-filter:**
```json
{
  "custom_css": "selector {\n  backdrop-filter: blur(16px);\n  -webkit-backdrop-filter: blur(16px);\n}"
}
```

**Layered SVG wave background (set on the section, not a child):**
```json
{
  "custom_css": "selector {\n  position: relative;\n}\nselector::before {\n  content: '';\n  position: absolute;\n  inset: 0;\n  background-image: url('data:image/svg+xml,...');\n  background-size: cover;\n  pointer-events: none;\n  z-index: 0;\n}\nselector > .e-con-inner {\n  position: relative;\n  z-index: 1;\n}"
}
```

`pointer-events: none` on the decorative pseudo-element prevents it from blocking clicks on real content.
