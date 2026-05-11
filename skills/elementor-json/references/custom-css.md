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

## Restyling a widget's inner DOM (instead of reaching for HTML widget)

Elementor widgets render specific DOM that you can target with `selector .inner-class` from `custom_css`. **This is the lever that lets you keep content editable while applying any visual style.** Most "I'd need an HTML widget for this" situations are actually solvable here.

**Icon-list with red-circle checkmarks** (replaces hand-coded `<ul>` with SVGs):

```json
{
  "custom_css": "selector .elementor-icon-list-items{display:grid;grid-template-columns:1fr 1fr;gap:12px 22px;list-style:none;padding:0;margin:0}@media(max-width:760px){selector .elementor-icon-list-items{grid-template-columns:1fr;gap:10px}}selector .elementor-icon-list-item{align-items:center !important;display:flex !important}selector .elementor-icon-list-item>a,selector .elementor-icon-list-item>span{display:flex !important;align-items:center !important;width:100%;gap:12px}selector .elementor-icon-list-icon{box-sizing:border-box !important;background:#E63946 !important;border-radius:50% !important;width:24px !important;height:24px !important;min-width:24px !important;padding:0 !important;display:inline-flex !important;align-items:center !important;justify-content:center !important;flex-shrink:0 !important;margin:0 !important;box-shadow:0 4px 10px rgba(230,57,70,.3);line-height:1}selector .elementor-icon-list-icon svg{width:13px !important;height:13px !important;display:block !important;margin:0 !important}selector .elementor-icon-list-icon svg path{fill:#fff !important}selector .elementor-icon-list-icon i{color:#fff !important;font-size:11px !important}selector .elementor-icon-list-text{padding-left:12px !important;margin-left:0 !important}"
}
```

The `!important`s are unfortunately necessary — Elementor's default styles target `.elementor-icon-list-icon` with high specificity, and without `!important` your background/size won't apply. The 12px `padding-left` on `.elementor-icon-list-text` is critical: without it, the text sits flush against the checkmark circle and looks crammed.

**Counter with red suffix:**

```json
{
  "custom_css": "selector .elementor-counter-number-suffix{color:#E63946;font-size:24px;margin-left:2px;font-weight:800}selector .elementor-counter-number-prefix{color:#FFFFFF}selector .elementor-counter-number{display:inline-flex;align-items:baseline;justify-content:center}"
}
```

**Accordion no-underline (FAQ underline fix):**

```json
{
  "custom_css": "selector .elementor-accordion-item{margin-bottom:10px;overflow:hidden;border-radius:10px}selector .elementor-accordion .elementor-tab-title{cursor:pointer}selector .elementor-accordion-title,selector .elementor-tab-title,selector .elementor-tab-title a,selector .elementor-tab-title *,selector .elementor-tab-title:hover,selector .elementor-tab-title:hover *{text-decoration:none !important;border-bottom:0 !important}"
}
```

Many themes add `text-decoration: underline` to accordion titles on hover. The hover override needs to include child selectors (`:hover *`) because the title text is wrapped in an inner span.

## Button hover-text override

Elementor's `hover_color` (text color on hover) is unreliable on outline-style buttons that swap background — the text often stays its base color and becomes invisible against the new background. Force it with `custom_css`:

```json
{
  "custom_css": "selector .elementor-button{text-decoration:none !important}selector .elementor-button:hover{background:#FFFFFF !important;color:#0B2C4D !important;border-color:#FFFFFF !important}selector .elementor-button:hover .elementor-button-text{color:#0B2C4D !important}"
}
```

Note the explicit `.elementor-button-text` override — Elementor wraps the button label in an inner span, and the wrapping span's color rule wins over the outer button's color. Override both.

The `text-decoration: none !important` on the base `.elementor-button` also kills the theme's default link underline that some themes add to all `a` tags.

## Pulse animation for status dots / "live" badges

```json
{
  "custom_css": "selector .dot{width:7px;height:7px;border-radius:50%;background:#E63946;box-shadow:0 0 0 0 rgba(230,57,70,.6);animation:p-pulse 1.8s infinite}@keyframes p-pulse{0%{box-shadow:0 0 0 0 rgba(230,57,70,.6)}70%{box-shadow:0 0 0 10px rgba(230,57,70,0)}100%{box-shadow:0 0 0 0 rgba(230,57,70,0)}}"
}
```

Prefix your `@keyframes` name (e.g., `p-pulse`, `a2-pulse`) so it doesn't collide with theme or other-template animations on the same page.

## Brute-force flex / grid layouts (when Elementor's container defaults fight you)

Elementor containers sometimes refuse to honor `flex-direction: row` on inner content because the default `.e-con-inner` wrapper applies its own column flex. When `flex_direction: "row"` in JSON settings produces a stacked layout anyway, force both the outer container AND its `.e-con-inner` wrapper:

```json
{
  "custom_css": "selector,selector>.e-con-inner{display:flex !important;flex-direction:row !important;flex-wrap:nowrap !important;align-items:center !important;justify-content:space-between !important;gap:24px !important;width:100%}selector>.e-con,selector>.e-con-inner>.e-con{flex:1 1 0 !important;min-width:0 !important;max-width:25% !important;width:25% !important}@media(max-width:760px){selector,selector>.e-con-inner{flex-wrap:wrap !important;gap:28px 16px !important}selector>.e-con,selector>.e-con-inner>.e-con{flex:0 0 calc(50% - 8px) !important;max-width:calc(50% - 8px) !important;width:calc(50% - 8px) !important}}"
}
```

This is the pattern for a 4-column stat strip on desktop that collapses to 2×2 on mobile. The `!important`s are needed because Elementor's generated stylesheet has higher specificity than a single class.

## Asymmetric hero with mobile reordering (grid-template-areas)

When the desktop hero is a 2-column split (e.g., text-left + image-right) and mobile needs the image *between* heading and paragraph (not after both), use `grid-template-areas`:

```json
{
  "custom_css": "selector{display:grid !important;grid-template-columns:1.05fr 1fr;grid-template-areas:\"lt right\" \"lb right\";column-gap:56px;row-gap:0;align-items:center}selector>.lt{grid-area:lt}selector>.right{grid-area:right;align-self:center}selector>.lb{grid-area:lb}@media(max-width:1024px){selector{grid-template-columns:1fr 1fr;column-gap:36px}}@media(max-width:760px){selector{display:flex !important;flex-direction:column;grid-template-areas:none;gap:24px}selector>.lt{order:1}selector>.right{order:2}selector>.lb{order:3}}"
}
```

Mark the three children with CSS classes (`lt`, `right`, `lb`) via the container's `_css_classes` field. On desktop, grid-template-areas puts the right column spanning two rows alongside the stacked `lt`/`lb` on the left. On mobile, the grid dissolves into a flex column with `order` properties controlling the sequence.

## Receipt / invoice mockup shell

For data-display "card" mockups (sample quote, receipt, invoice, ticket) where the visual is heavily designed but the values inside should be editable, build the **shell** in `custom_css` and put each editable line as a separate heading widget:

```json
{
  "custom_css": "selector{position:relative;background:#fff;border-radius:18px;padding:28px;box-shadow:0 30px 60px rgba(11,44,77,.18),0 0 0 1px rgba(11,44,77,.06);font-family:'JetBrains Mono',ui-monospace,monospace}selector::before{content:'';position:absolute;left:-18px;top:18px;right:18px;bottom:-18px;border-radius:18px;background:linear-gradient(135deg,rgba(11,44,77,.07),rgba(230,57,70,.05));z-index:-1}selector::after{content:'';position:absolute;left:14px;right:14px;bottom:-10px;height:14px;background-image:radial-gradient(circle at 7px 7px,#fff 6px,transparent 6.5px);background-size:14px 14px;background-repeat:repeat-x}"
}
```

The torn-receipt notch effect at the bottom (`::after`) and the offset gradient ring behind (`::before`) are decorative-only — the line items inside the container are still individual heading widgets the client can edit.

## Timeline / step-connector line

For a row of step circles or milestone dots with a horizontal connector line running through them, set the line as `::before` on the grid container and let the step elements sit on top:

```json
{
  "custom_css": "selector{position:relative}selector::before{content:'';position:absolute;left:16.66%;right:16.66%;top:32px;height:2px;background:linear-gradient(90deg,transparent 0%,rgba(229,233,240,1) 6%,rgba(230,57,70,.55) 30%,rgba(230,57,70,.55) 70%,rgba(229,233,240,1) 94%,transparent 100%);z-index:0;border-radius:2px}@media(max-width:1024px){selector::before{display:none}}"
}
```

The `16.66%` / `16.66%` margins correspond to the centers of the first and last columns in a 3-column grid. The gradient fades in/out at the ends and tints red in the middle so it reads as a clean track. **Crucially**, don't set a `background: #fff` on the step elements — they need to be transparent so the line is visible behind/between them. Only the number circles themselves should have a white background.
