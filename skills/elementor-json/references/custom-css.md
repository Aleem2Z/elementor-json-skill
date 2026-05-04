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

## The `selector` keyword

Inside `custom_css`, the literal string `selector` is replaced at render time with a CSS selector matching THIS element specifically. So `selector::before` targets the pseudo-element of this container only — no need to know its generated `.elementor-element-xxxx` class.

- `selector` → this element
- `selector::before` / `selector::after` → its pseudo-elements
- `selector .child-class` → descendant elements
- `selector:hover` → hover state
- `selector h3` → child h3 (useful for text-editor styling)

## When it's justified

- **Pseudo-elements** (`::before`, `::after`) — Elementor has no native UI for these.
- **Inline SVG backgrounds** — Elementor's background image control wants a Media Library upload; for inline `data:image/svg+xml` URIs, use `custom_css`.
- **Hover transitions on properties Elementor can't animate** — e.g. `transition: box-shadow .3s ease` so the hover box-shadow animates rather than snapping.
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
  "custom_css": "selector::before {\n  content: '';\n  position: absolute;\n  top: -80px;\n  left: 50%;\n  transform: translateX(-50%);\n  width: 100%;\n  max-width: 1200px;\n  height: 100px;\n  background: url('data:image/svg+xml,%3Csvg viewBox=\\'0 0 1200 100\\'...') no-repeat center;\n  background-size: 100% 100%;\n}"
}
```

**Hover box-shadow transition (paired with hover settings in JSON):**
```json
{
  "custom_css": "selector {\n  transition: box-shadow 0.3s ease;\n}\nselector:hover {\n  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.08);\n}"
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
  "custom_css": "selector {\n  position: relative;\n}\nselector::before {\n  content: '';\n  position: absolute;\n  inset: 0;\n  background-image: url('data:image/svg+xml,%3Csvg xmlns=\\'http://www.w3.org/2000/svg\\' viewBox=\\'0 0 1200 500\\'%3E%3Cpath d=\\'M0,350 Q150,320 300,350 T600,340 T1200,340 L1200,500 L0,500 Z\\' fill=\\'rgba(255,255,255,0.03)\\'/%3E%3C/svg%3E');\n  background-size: cover;\n  pointer-events: none;\n  z-index: 0;\n}\nselector > .e-con-inner {\n  position: relative;\n  z-index: 1;\n}"
}
```

`pointer-events: none` on the decorative pseudo-element prevents it from blocking clicks on real content.

`selector > .e-con-inner` targets Elementor's internal flex wrapper — useful when you need to lift real content above a `::before` overlay.
