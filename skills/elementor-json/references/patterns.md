# Section patterns

Concrete recipes for the section types that recur across modern marketing pages: hero variants, stat strips, card grids, FAQs, CTAs, and the small decorative widgets that tie them together (eyebrow chips, step tags, accent underlines, signature rows).

**These patterns exist so you don't reinvent them and don't fall back to HTML widgets.** Each pattern uses native widgets + `custom_css` where styling is needed. Copy the pattern, swap the content, change the colors.

## Decorative element patterns

These tiny components show up repeatedly inside heroes, intros, and section heads. Build them as **heading widgets** (not HTML), so the client can edit the text label through the Elementor controls panel.

### Eyebrow chip — small red pill with pulse dot

The little chip above a hero heading: `● FAMILY-RUN · BREVARD COUNTY` or `● OUR MOBILE NOTARY SERVICES`. Build it as a heading widget with `custom_css` providing the pill background and pulse animation:

```json
{
  "id": "eyebrow001",
  "elType": "widget",
  "widgetType": "heading",
  "settings": {
    "title": "<span class='dot'></span>Family-Run · Brevard County",
    "header_size": "div",
    "align": "left",
    "title_color": "#E63946",
    "typography_typography": "custom",
    "typography_font_family": "Inter",
    "typography_font_size": {"unit": "px", "size": 12},
    "typography_font_weight": "700",
    "typography_letter_spacing": {"unit": "px", "size": 1.68},
    "typography_text_transform": "uppercase",
    "_margin": {"unit": "px", "top": "0", "right": "0", "bottom": "20", "left": "0", "isLinked": false},
    "custom_css": "selector .elementor-heading-title{display:inline-flex;align-items:center;gap:8px;padding:6px 14px;border-radius:999px;background:rgba(230,57,70,.08);border:1px solid rgba(230,57,70,.18)}selector .dot{width:7px;height:7px;border-radius:50%;background:#E63946;box-shadow:0 0 0 0 rgba(230,57,70,.6);animation:p-pulse 1.8s infinite;display:inline-block}@keyframes p-pulse{0%{box-shadow:0 0 0 0 rgba(230,57,70,.6)}70%{box-shadow:0 0 0 10px rgba(230,57,70,0)}100%{box-shadow:0 0 0 0 rgba(230,57,70,0)}}"
  },
  "elements": [],
  "isInner": false
}
```

`header_size: "div"` keeps the eyebrow out of the document outline (it's not a real heading semantically). The pulse keyframe is namespaced `p-pulse` to avoid colliding with theme animations.

### Section step tag — bar-text-bar inline decorator

The small caps decorator with horizontal bars: `▬▬ OUR STORY` or `▬▬ HOW IT WORKS ▬▬`. Used to introduce a section above the section heading. Build as a heading widget:

```json
{
  "id": "step001",
  "elType": "widget",
  "widgetType": "heading",
  "settings": {
    "title": "Our Story",
    "header_size": "div",
    "align": "left",
    "title_color": "#E63946",
    "typography_typography": "custom",
    "typography_font_family": "Inter",
    "typography_font_size": {"unit": "px", "size": 11},
    "typography_font_weight": "800",
    "typography_letter_spacing": {"unit": "px", "size": 1.76},
    "typography_text_transform": "uppercase",
    "_margin": {"unit": "px", "top": "0", "right": "0", "bottom": "14", "left": "0", "isLinked": false},
    "custom_css": "selector .elementor-heading-title{display:inline-flex;align-items:center;gap:10px}selector .elementor-heading-title::before{content:'';width:28px;height:2px;background:#E63946;border-radius:2px;flex-shrink:0}"
  },
  "elements": [],
  "isInner": false
}
```

For the two-bar variant (centered headers with bars on both sides), add an `::after` mirroring the `::before`. For an `align: "center"` step tag, both bars usually look balanced; for left-aligned, just the leading bar is conventional.

### Accent underline — red bar under a word inside a heading

Inline emphasis where one word in a heading gets a red horizontal bar underneath it: `Local. Reliable. **On the Space Coast.**` (with the underline on the last phrase). Inside a heading widget's `title`, wrap the word in a span and style it via `custom_css`:

```json
{
  "id": "head001",
  "elType": "widget",
  "widgetType": "heading",
  "settings": {
    "title": "Local. Reliable. <span class='accent'>On the Space Coast.</span>",
    "header_size": "h1",
    "align": "left",
    "title_color": "#0B2C4D",
    "typography_typography": "custom",
    "typography_font_family": "Poppins",
    "typography_font_size": {"unit": "px", "size": 52},
    "typography_font_size_tablet": {"unit": "px", "size": 36},
    "typography_font_size_mobile": {"unit": "px", "size": 32},
    "typography_font_weight": "800",
    "typography_line_height": {"unit": "em", "size": 1.08},
    "typography_letter_spacing": {"unit": "px", "size": -0.78},
    "custom_css": "selector .accent{position:relative;display:inline;white-space:nowrap}selector .accent::after{content:'';position:absolute;left:0;right:0;bottom:-2px;height:3px;background:#E63946;border-radius:2px}"
  },
  "elements": [],
  "isInner": false
}
```

`white-space: nowrap` on the accent span is **critical** — without it, when the heading wraps onto multiple lines, the underline can render mid-line and slice through the next line's text. The nowrap forces the underlined phrase to stay on one line.

For a script-style subhead (Playfair italic in red, on its own line below), append another span:

```html
"title": "Local. Reliable. <span class='accent'>On the Space Coast.</span><span class='script'>a notary you can actually reach</span>"
```

With `custom_css`: `selector .script{display:block;font-family:'Playfair Display',Georgia,serif;font-style:italic;font-weight:700;color:#E63946;font-size:38px;margin-top:10px;line-height:1.1}`.

## Hero patterns

Three distinct hero rhythms — pick one per page and avoid cloning the same layout across multiple pages of the same site. The shared design language (colors, fonts, eyebrow chip, accent underline) keeps them visually related; the layout difference keeps each page feeling distinct.

### Hero A: centered catalog (services/landing-page style)

Text-centered. Eyebrow chip on top, big H1 with accent-underline, Playfair script subhead, two CTAs, trust meta row. Below the meta, optionally an icon-box grid showing the categories/services as preview chips. Best when the page is *a menu of things* (services, plans, locations).

### Hero B: asymmetric split with portrait card (about-page style)

Two-column. Left column holds eyebrow + heading + lead paragraph + CTAs + trust meta — all left-aligned. Right column holds a tall portrait card with floating credential chips overlapping its edges and a rotated stamp seal. Best when the page is *a person* (about, founder bio, team member spotlight).

The portrait card itself uses absolute-positioned chips, so it's one HTML widget — but the left column is entirely native widgets so the client can edit every word.

### Hero C: split with receipt/data mockup (pricing-page style)

Two-column. Left column = same structure as Hero B's left. Right column = a stylized "sample" data mockup (a receipt, an invoice, an order summary, a dashboard card). Use JetBrains Mono / monospace font for the mockup to convey "this is real data." Add a rotated red stamp ("TRANSPARENT", "VERIFIED", "APPROVED") for personality. Best when the page is *about pricing / transparency / a process*.

For mobile: when the receipt should appear between the heading and the lead paragraph (not at the bottom after both), structure the hero as three sibling containers (`lt` = eyebrow + heading, `right` = receipt, `lb` = lead + CTAs + meta) and use the grid-template-areas + flex-order trick from `custom-css.md`.

## Stat strip patterns

### Stat strip A: navy counter row (services-page style)

Full-bleed navy section, 4 columns of animated counter widgets with red `+` / `★` suffix. Each counter starts at 0 and animates up on scroll. Use this for current-state numbers (signings completed, years in business, reviews, response time).

### Stat strip B: navy milestones timeline (about-page style)

Full-bleed navy section, 4 milestone columns with **year labels** (2017, 2019, 2022, 2026) connected by a red gradient line running through dot markers. Each milestone has a year, a dot on the connector line, a small caps label, and a one-line description. Use this when the page is about *the journey over time* — about-page or "our history" sections.

The connector line is a `::before` pseudo-element on the grid container; the step elements have transparent backgrounds so the line shows through. Documented in `custom-css.md` under "Timeline / step-connector line."

### Stat strip C: light trust strip (pricing-page style)

White or light-gray section, 4 icon-box widgets in a row showing *what's always included*: "60-Second Quotes / Locked-in before we leave", "Cash or Card / Pay at the appointment", etc. Use `icon-box` with a soft icon background that flips solid red on hover. Best when the strip is meant to reassure / answer objections rather than impress with numbers.

## Card grid patterns

### Card grid with hover-lift

The repeating-card section pattern with `translateY(-4px)` lift, shadow elevation, and border-color shift on hover.

Container settings (on each card container):
```json
{
  "flex_direction": "column",
  "padding": {"unit": "px", "top": "30", "right": "26", "bottom": "30", "left": "26", "isLinked": false},
  "background_background": "classic",
  "background_color": "#F5F7FA",
  "border_border": "solid",
  "border_width": {"unit": "px", "top": "1", "right": "1", "bottom": "1", "left": "1", "isLinked": true},
  "border_color": "#E5E9F0",
  "border_radius": {"unit": "px", "top": "14", "right": "14", "bottom": "14", "left": "14", "isLinked": true},
  "width": {"unit": "%", "size": 33, "sizes": []},
  "width_tablet": {"unit": "%", "size": 100, "sizes": []},
  "width_mobile": {"unit": "%", "size": 100, "sizes": []},
  "custom_css": "selector{transition:transform .2s ease,box-shadow .2s ease,border-color .15s}selector:hover{transform:translateY(-4px);box-shadow:0 18px 40px rgba(11,44,77,.10);border-color:rgba(230,57,70,.25)}"
}
```

Place native widgets inside (Icon + Heading + Text Editor) — they remain editable, the hover effect is purely on the container.

For a top accent line ("this card is recommended"), add `selector::before{content:'';position:absolute;left:0;right:0;top:0;height:3px;background:linear-gradient(90deg,#E63946,transparent);opacity:.85}` and `position:relative;overflow:hidden` on the container.

### Featured tier card with scale + Most Popular badge

In a 3-tier pricing grid, mark the middle tier as featured: scaled up 1.03×, navy gradient background, white text, and a red "★ Most Popular" pill protruding from the top edge.

Tier card settings (the featured one):
```json
{
  "background_background": "gradient",
  "background_color": "#0B2C4D",
  "background_color_b": "#123E6B",
  "background_gradient_type": "linear",
  "background_gradient_angle": {"unit": "deg", "size": 165},
  "border_color": "#0B2C4D",
  "box_shadow_box_shadow_type": "yes",
  "box_shadow_box_shadow": {"horizontal": 0, "vertical": 24, "blur": 50, "spread": 0, "color": "rgba(11,44,77,0.22)"},
  "custom_css": "selector{transform:scale(1.03)}selector:hover{transform:scale(1.03) translateY(-6px);box-shadow:0 30px 60px rgba(11,44,77,.28) !important}@media(max-width:1024px){selector{transform:none}selector:hover{transform:translateY(-6px)}}"
}
```

The Most Popular badge is a heading widget with absolute positioning (NOT an HTML widget):
```json
{
  "widgetType": "heading",
  "settings": {
    "title": "★ Most Popular",
    "header_size": "div",
    "title_color": "#FFFFFF",
    "typography_typography": "custom",
    "typography_font_family": "Poppins",
    "typography_font_size": {"unit": "px", "size": 11},
    "typography_font_weight": "800",
    "typography_letter_spacing": {"unit": "px", "size": 1.54},
    "typography_text_transform": "uppercase",
    "custom_css": "selector{position:absolute;top:-13px;left:50%;transform:translateX(-50%);z-index:2}selector .elementor-heading-title{background:#E63946;padding:6px 14px;border-radius:999px;box-shadow:0 8px 22px rgba(230,57,70,.4);white-space:nowrap}"
  }
}
```

Mobile: undo the scale (it overflows on phones) and let the featured tier sit at normal size with just the hover-lift.

## Service-block alternating layout

For a list of services where odd entries show `[image | content]` and even entries show `[content | image]`, **keep the DOM order consistent** (always `[image, content]`) and flip the visual order with `flex-direction: row-reverse` on the alternating blocks. This way, on mobile (when columns stack), the image is *always* on top — no reordering needed.

```json
{
  "flex_direction": "row",
  "flex_direction_tablet": "column",
  "flex_direction_mobile": "column"
}
```

For the alternating "reverse" blocks:
```json
{
  "flex_direction": "row-reverse",
  "flex_direction_tablet": "column",
  "flex_direction_mobile": "column"
}
```

DOM order stays `[image-container, content-container]`. On desktop, `row-reverse` swaps the visual order. On tablet/mobile, `column` ignores the reverse and the image stacks on top of the content — which is the standard mobile reading order for a service card.

If you instead use `flex-direction: row` with the DOM order swapped on reverse blocks, you'll find mobile shows content-above-image on those blocks, which usually reads wrong.

## Image with floating overlay badge

For "Since 2017" badges floating over a portrait, or "Service 01/06" / "$50" overlay chips on a service-block image, **build the image as an Image widget** and place small **heading widgets with absolute positioning** as siblings inside the same container:

Container settings:
```json
{
  "padding": {"unit": "px", "size": 0},
  "custom_css": "selector{position:relative}"
}
```

Inside: an Image widget at normal flow + 1–3 heading widgets with `custom_css` positioning them absolutely:
```json
{
  "widgetType": "heading",
  "settings": {
    "title": "● Since 2017",
    "header_size": "div",
    "title_color": "#0B2C4D",
    "typography_typography": "custom",
    "typography_font_family": "Poppins",
    "typography_font_size": {"unit": "px", "size": 12},
    "typography_font_weight": "800",
    "typography_letter_spacing": {"unit": "px", "size": 1.2},
    "typography_text_transform": "uppercase",
    "custom_css": "selector{position:absolute;left:18px;top:18px;z-index:2}selector .elementor-heading-title{background:#fff;padding:8px 14px;border-radius:999px;box-shadow:0 8px 22px rgba(0,0,0,.18),0 0 0 1px rgba(11,44,77,.06)}"
  }
}
```

The Image widget remains editable (client swaps the image through the Media Library); each badge remains editable (client changes "Since 2017" to "Since 2018" through the Elementor panel). No HTML widget needed.

For an image with bottom-corner overlay chips (like a price badge + a number stamp on a service-block image), use the same pattern with two heading widgets, positioned `bottom: 18px; left: 18px` and `bottom: 18px; right: 18px`.

## Marquee sticky nav (auto-scroll horizontal links)

Auto-scrolling horizontal nav at the top of a section, used as in-page anchor links. This **does** need an HTML widget because the CSS keyframe animation has no Elementor control surface. Build it as a single HTML widget inside a sticky container:

Container settings:
```json
{
  "padding": {"unit": "px", "top": "14", "right": "0", "bottom": "14", "left": "0", "isLinked": false},
  "background_background": "classic",
  "background_color": "rgba(255,255,255,0.94)",
  "border_border": "solid",
  "border_width": {"unit": "px", "top": "0", "right": "0", "bottom": "1", "left": "0", "isLinked": false},
  "border_color": "#E5E9F0",
  "custom_css": "selector{position:sticky;top:68px;z-index:30;overflow:hidden;-webkit-backdrop-filter:saturate(140%) blur(8px);backdrop-filter:saturate(140%) blur(8px)}@media(max-width:760px){selector{top:64px}}"
}
```

HTML widget content (duplicate the link list inside the track for a seamless loop):
```html
<div class="mq">
  <div class="mq-track">
    <a href="#general">General Mobile Notary</a>
    <a href="#loan">Loan Signing Agent</a>
    <a href="#poa">Power of Attorney</a>
    <!-- duplicate aria-hidden copies for the loop -->
    <a href="#general" aria-hidden="true">General Mobile Notary</a>
    <a href="#loan" aria-hidden="true">Loan Signing Agent</a>
    <a href="#poa" aria-hidden="true">Power of Attorney</a>
  </div>
</div>
<style>
.mq{padding:14px 0;overflow:hidden;-webkit-mask-image:linear-gradient(90deg,transparent 0,#000 6%,#000 94%,transparent 100%);mask-image:linear-gradient(90deg,transparent 0,#000 6%,#000 94%,transparent 100%);transform:translateZ(0)}
.mq-track{display:inline-flex;gap:64px;width:max-content;align-items:center;animation:mq-scroll 45s linear infinite;will-change:transform;transform:translate3d(0,0,0);-webkit-backface-visibility:hidden;backface-visibility:hidden}
.mq:hover .mq-track{animation-play-state:paused}
@keyframes mq-scroll{0%{transform:translate3d(0,0,0)}100%{transform:translate3d(-50%,0,0)}}
.mq a{flex-shrink:0;display:inline-flex;align-items:center;gap:10px;padding:6px 4px;font-size:14px;font-weight:800;color:#0B2C4D;letter-spacing:.84px;text-transform:uppercase;font-family:'Poppins',sans-serif;text-decoration:none;white-space:nowrap}
.mq a::before{content:'';width:6px;height:6px;border-radius:50%;background:#E63946;opacity:.8}
.mq a:hover{color:#E63946}
@media(max-width:760px){.mq-track{gap:38px;animation-duration:32s}.mq a{font-size:12px}}
</style>
```

Critical for iOS Safari: use `translate3d(0,0,0)` and `-webkit-backface-visibility: hidden` to force GPU compositing. Without these the marquee may render but not animate on iPhones.

## Gradient CTA section (final-CTA on every page)

The closing-CTA strip with navy → secondary linear gradient and a red radial glow on the right. Used at the bottom of services, about, pricing, etc. Two layouts: heading + paragraph on the left, stacked CTAs on the right.

Section container settings:
```json
{
  "content_width": "full",
  "padding": {"unit": "px", "top": "56", "right": "0", "bottom": "56", "left": "0", "isLinked": false},
  "background_background": "gradient",
  "background_color": "#0B2C4D",
  "background_color_b": "#123E6B",
  "background_gradient_type": "linear",
  "background_gradient_angle": {"unit": "deg", "size": 135},
  "custom_css": "selector{position:relative;overflow:hidden}selector::before{content:'';position:absolute;inset:0;background:radial-gradient(700px 350px at 90% 50%,rgba(230,57,70,.18),transparent 60%);pointer-events:none}"
}
```

Inner boxed container (1300px), `flex_direction: "row"`, with a text column (`width: 60%`) holding heading + text-editor, and an actions column (`width: 40%`) holding two stacked buttons. On tablet/mobile, switch to column and center the buttons.

For the outline button on dark backgrounds, use the translucent-white variant (`background: rgba(255,255,255,0.10)`, `border: 1.5px solid rgba(255,255,255,0.32)`, `color: #fff`) so the text stays legible on the gradient.

## Disclaimer / callout card with red left-border accent

The legal-disclaimer or note card with a thick red left border and a circular red info icon. Used for "Florida statute §117.05" notes on pricing pages.

Container settings:
```json
{
  "flex_direction": "row",
  "flex_align_items": "flex-start",
  "flex_gap": {"unit": "px", "column": "14", "row": "12", "isLinked": false, "size": 14},
  "padding": {"unit": "px", "top": "18", "right": "22", "bottom": "18", "left": "22", "isLinked": false},
  "background_background": "gradient",
  "background_color": "rgba(11,44,77,0.04)",
  "background_color_b": "rgba(230,57,70,0.04)",
  "background_gradient_type": "linear",
  "background_gradient_angle": {"unit": "deg", "size": 135},
  "border_border": "solid",
  "border_width": {"unit": "px", "top": "1", "right": "1", "bottom": "1", "left": "3", "isLinked": false},
  "border_color": "#E5E9F0",
  "border_radius": {"unit": "px", "top": "10", "right": "10", "bottom": "10", "left": "10", "isLinked": true},
  "custom_css": "selector{border-left-color:#E63946 !important}"
}
```

The asymmetric `border_width` (3px left, 1px elsewhere) creates the accent stripe. The `border-left-color: #E63946 !important` override is needed because Elementor applies `border_color` uniformly to all four sides; the `!important` lets the left side win.

Inside: an icon widget (the info circle) + a text-editor widget with the disclaimer text. Both editable.

## Founder quote / blockquote with Playfair italic

The pull-quote in a founder bio: red left border, Playfair italic font, 22px size. Use a **text-editor widget** with a `<blockquote>` inside, styled via `custom_css`:

```json
{
  "widgetType": "text-editor",
  "settings": {
    "editor": "<blockquote>\"Every signing is somebody's stressful day. My job is to be the part that goes smoothly.\"</blockquote>",
    "text_color": "#0B2C4D",
    "typography_typography": "custom",
    "typography_font_family": "Playfair Display",
    "typography_font_style": "italic",
    "typography_font_size": {"unit": "px", "size": 24},
    "typography_font_size_mobile": {"unit": "px", "size": 18},
    "typography_font_weight": "700",
    "typography_line_height": {"unit": "em", "size": 1.45},
    "_margin": {"unit": "px", "top": "18", "right": "0", "bottom": "18", "left": "0", "isLinked": false},
    "custom_css": "selector blockquote{border-left:4px solid #E63946;padding:8px 0 8px 22px;margin:0}@media(max-width:760px){selector blockquote{padding-left:18px}}"
  }
}
```

The client edits the quote through the text-editor inline editor; the visual treatment is purely CSS.

## Signature row — circle avatar + name + title

The "signed by Sarah Mitchell, Founder" row at the bottom of a founder section. Three native widgets in a flex-row inner container:

Inner container settings:
```json
{
  "flex_direction": "row",
  "flex_align_items": "center",
  "flex_gap": {"unit": "px", "column": "14", "row": "8", "isLinked": false, "size": 14},
  "padding": {"unit": "px", "size": 0},
  "_margin": {"unit": "px", "top": "14", "right": "0", "bottom": "0", "left": "0", "isLinked": false}
}
```

Inside, three widgets:
1. **Image** widget (the avatar — small square, will be circled via custom_css) with `width: 48px`, `border_radius: 50%`, optionally a 2px red border via `border_border: "solid"` + `border_width` + `border_color`.
2. **Heading** widget (the name) with `header_size: "h4"`, Poppins 800 14px navy.
3. **Heading** widget (the title) with `header_size: "div"`, Inter 600 12px uppercase letter-spaced grey.

Alternative: combine #2 and #3 into a single text-editor with a `<strong>` for the name and a `<span>` for the title, styled via `custom_css`. Either works — the multi-widget approach is more editable through the controls panel; the single text-editor is more compact.

## Design philosophy: visually distinct pages, shared DNA

When building multiple pages for the same site, hold these two principles in tension:

**Shared design DNA (keep these consistent across every page):**
- Colors: same primary (navy), accent (red), text, light, border tokens.
- Fonts: same family choices and weight scale.
- Eyebrow chip, step tag, accent underline — same components, same styling.
- Dot-grid background pattern in hero zones.
- Final CTA section style.
- Button hover behavior.
- FAQ accordion look.

**Distinct layout rhythm (make each page feel different):**
- Hero: pick one of the three hero patterns above per page. Don't copy the centered catalog hero from services onto the about page — even with different content, the layout silhouette reads as a clone. The about page should use the asymmetric portrait split; pricing should use the receipt mockup; services uses the centered catalog.
- Stat strip: pick A (counters), B (timeline), or C (trust strip) — one per site or one per page-type, but don't reuse the same treatment back-to-back.
- Card grid: vary the visual treatment between sections — promise cards on about can use top-accent-line + light bg; tier cards on pricing use the scaled featured + navy gradient; service blocks on services use full alternating row layout.

The fastest way to detect cloning: look at the hero's text alignment (centered vs left-aligned), the right-side visual (none vs portrait vs receipt), and the trust strip below (no strip vs counters vs timeline vs trust pills). If any two pages match on those three, the layouts will feel identical despite different content.

## Breakpoint principle: iPad keeps splits, mobile stacks

Elementor's default breakpoints are 1024px (tablet) and 768px (mobile). For two-column splits (hero, story sections, founder section), the convention is:

- **Desktop (>1024px)**: two columns side-by-side at full size.
- **Tablet (768–1024px)**: two columns side-by-side at tightened sizes (smaller image card, reduced gap, smaller heading font). Don't stack.
- **Mobile (<768px)**: stack to one column.

This means most `flex_direction` settings should be:
```json
{
  "flex_direction": "row",
  "flex_direction_mobile": "column"
}
```
…without a `_tablet` override (so tablet inherits the row layout). Card grids are different — they're usually 3-up on desktop, 1-up on tablet AND mobile (so the tablet stacks too).

For asymmetric heroes specifically, do the same: keep side-by-side at iPad, stack at phone. Stacking too early on iPad makes the page feel mobile-first and wastes the wider screen.

The user reviewing on a tablet will be more annoyed by an unnecessarily-stacked layout than by a slightly-tight side-by-side. When in doubt, keep the iPad split.
