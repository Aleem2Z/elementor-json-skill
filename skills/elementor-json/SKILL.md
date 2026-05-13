---
name: elementor-json
description: Use when converting Figma/screenshot designs into Elementor template JSON for WordPress, generating .json files for Elementor's Import/Export tool, building Elementor pages from a design, or asked about Elementor widget settings, container layouts, responsive variants, or template structure. Use this skill whenever the user mentions Elementor, WordPress page templates, .json import files, widget-based page building, or asks to convert HTML to Elementor — even if they don't explicitly say "Elementor JSON."
---

# Elementor JSON

Convert designs to Elementor-compatible JSON templates that import cleanly via **Elementor → Tools → Import / Export**.

## The non-negotiable rule: widgets first

The whole point of generating an Elementor template (rather than a static HTML page) is that the client can edit it through Elementor's UI controls — change a heading, swap an image, edit a button label, tweak a color — without touching code. **An HTML widget destroys that.** Inside an HTML widget, content is opaque to Elementor's controls panel, the responsive breakpoint switcher doesn't apply, and the client has to learn HTML to change a word.

So before reaching for the `html` widget, work the decision tree:

1. **Is there a native widget that produces this output?** Heading, Text Editor, Button, Image, Icon, Icon Box, Icon List, Star Rating, Counter, Accordion, Divider, Gallery, Video — these are all editable through controls. Use them. The user's site has **Elementor + Elementor Pro** available, so Pro widgets (Form, Nested Accordion, etc.) are also fair game.
2. **Can a combination of native widgets achieve the layout?** A "trust pill" with icon + title + subtitle is an **icon-box**, not three lines of HTML. A "feature list with red checkmarks" is an **icon-list** with `custom_css` restyling the icon span, not a `<ul>` in HTML. A "step card with a number circle + heading + text" is an Icon (or Heading) widget + Heading + Text Editor inside a container, not a `<div>`.
3. **Can `custom_css` on the widget achieve the visual polish?** Pulse animation on a dot, gradient underline on a heading word, restyling the icon-list checkmark as a red circle with white check — all of these belong in the widget's `custom_css` field using the `selector` keyword. The content stays editable; only the *visual* lives in CSS.
4. **Is the layout fundamentally outside Elementor's control surface?** Absolute-positioned overlapping chips on an image, a CSS marquee animation, a stitched-together "receipt" mockup with pseudo-element notches — these genuinely need HTML. **Even then**, push the editable content (headings, prices, button labels) out as native widgets where you can, and use HTML only for the decorative shell.

If you find yourself writing `<div class="...">` with text inside, stop and ask which widget that text belongs in.

## Critical rule: JSON settings, not CSS, for layout values

Even within native widgets, Elementor stores layout in JSON settings — `padding`, `typography_font_size`, `flex_justify_content`, etc. Putting those in `custom_css` instead breaks the editor: controls won't show the values, the responsive breakpoint switcher won't work, and theme styles can override the rules.

**Always use JSON settings for:**
- Spacing (`padding`, `margin`, `_padding`, `_margin`)
- Sizing (`width`, `max_width`, `min_height`)
- Colors (`text_color`, `background_color`, `title_color`)
- Typography (`typography_font_size`, `typography_font_weight`, `typography_line_height`) — `typography_typography: "custom"` is REQUIRED to activate them
- Layout (`flex_direction`, `flex_justify_content`, `flex_align_items`, `flex_gap`, `flex_wrap`)
- Visuals (`border_*`, `box_shadow_*`, `background_*`)
- Hover states (`background_hover_*`, `border_color_hover`, `box_shadow_*_hover`)
- Mobile/tablet background images (`background_image_mobile`, `background_overlay_color_mobile`)

**Use `custom_css` for:** pseudo-elements, keyframe animations, restyling a widget's inner DOM (e.g., the icon span inside an icon-list), brute-force theme overrides with `!important`, transitions Elementor can't configure. See `references/custom-css.md`.

**Use `rem` for font sizes** — they scale with the user's root font size.

## Critical rule: `isLinked: false` on every padding / margin, every responsive variant

Elementor's padding/margin controls in the UI have a "Link values together" toggle (the little chain icon next to the four-sided input). WordPress's default behaviour **auto-enables that toggle when the user switches into tablet/iPad or mobile view in the editor**, which silently injects a fake ~2–4px padding on all four sides of the container or widget that the client never asked for. The user finds this irritating and wants full manual control over every side.

**The fix: always set `"isLinked": false` explicitly on every padding and margin object in the JSON — including every responsive variant.** Real keys this affects:

- Section/Style-tab variants: `padding`, `padding_tablet`, `padding_mobile`, `margin`, `margin_tablet`, `margin_mobile`
- Advanced-tab variants (underscore-prefixed): `_padding`, `_padding_tablet`, `_padding_mobile`, `_margin`, `_margin_tablet`, `_margin_mobile`

This applies to **ALL widgets** — containers, buttons, headings, icon-boxes, icon-lists, text-editors, images, dividers, every Pro widget — AND **ALL responsive variants** (desktop, tablet, mobile). No exceptions: if the JSON has a padding or margin object, it must have `"isLinked": false`.

Example — the same widget's desktop / tablet / mobile padding, all explicit:

```json
{
  "_padding":        {"unit": "px", "top": "40", "right": "32", "bottom": "40", "left": "32", "isLinked": false},
  "_padding_tablet": {"unit": "px", "top": "32", "right": "24", "bottom": "32", "left": "24", "isLinked": false},
  "_padding_mobile": {"unit": "px", "top": "24", "right": "16", "bottom": "24", "left": "16", "isLinked": false}
}
```

The verifier will flag any padding/margin object where `isLinked` is missing entirely, or set to `true` on a responsive variant. Don't ship a template that trips that check.

## Design fidelity: extract tokens before writing any JSON

The #1 failure mode of this generator is **silently substituting theme defaults for values that should come from the source HTML**. The fix is to extract the design tokens explicitly, in writing, before a single line of JSON is generated. Three things to extract:

1. **Color palette.** List every hex / `rgb()` / `rgba()` / `hsl()` value used anywhere in the source HTML/CSS, and note which element used it (e.g. `#1F4FFF — primary button bg`, `#0F172A — heading text`, `#16A34A — pricing-tier checkmark icon`). **Every color set in the generated JSON MUST come from this list.** If a widget needs a color and the HTML didn't specify one for that element — e.g. an icon has no explicit `color:` CSS rule — you MUST ask the user. Do NOT default to the WordPress/Elementor theme blue, do NOT default to black, do NOT guess from a "similar" element. Real example from a failed test: pricing-tier icons had no explicit color in HTML and the generator silently set them to the theme blue; the client wanted a custom green.

2. **Spacing tokens.** List every section padding, container gap, widget margin, internal flex/grid `gap`, and the inner padding of any card-like element. Pull these from the HTML's declared CSS (or the computed value if you rendered it). **Every padding/margin in the generated JSON must match a token from this list — do not invent values.** Real example: a pricing tier rendered fine visually overall, but the middle "featured" card ended up 30%+ taller than its siblings because the generator never extracted the inner card padding and picked a different value for the middle card.

3. **Decorative effects.** List every:
   - pseudo-element shape (`::before`, `::after`) — e.g. connector lines between numbered circles, decorative dots, ribbons
   - `transition` + `:hover` rule that changes `transform`, `color`, `background`, `box-shadow`, etc.
   - `@keyframes` animation and what triggers it
   - gradient underlines, gradient backgrounds, gradient text

   Each of these will become a `custom_css` block on the right widget. Real examples that were silently dropped in the failed test: a horizontal gradient line running through the "1 — 2 — 3" step circles (a `::before` on the grid container — only achievable via `custom_css`); a `:hover` effect on the "Emergency / Rush" pricing item that turns the card red and pushes it forward (a `:hover` transition — only achievable via `custom_css`). Both were visible in the source HTML and both were missed.

**If any of these tokens are ambiguous in the source — a color not explicit for a specific element, a padding not declared, a hover behavior unclear — DO NOT proceed to JSON. Batch every ambiguity into a single set of clarifying questions for the user (use the AskUserQuestion tool if available, or a numbered list otherwise), get answers, THEN write the JSON. The user explicitly does not want the generator hallucinating defaults.**

### How to read the source HTML thoroughly

Read the **entire** `.html` file with the Read tool — don't skim, don't sample, don't trust the first 80 lines. Walk every selector, every inline `style="..."`, every `:hover` / `::before` / `::after` rule, every `@keyframes` block, every media query. If a browser is available, render the HTML and screenshot it for side-by-side comparison while you extract tokens. Build the three lists above (colors, spacing, decorative effects) as plain text **before** touching JSON. Most fidelity loss in this skill happens not because the JSON schema was wrong but because the generator never explicitly wrote down what it was supposed to match.

### Output your token lists before generating

**Before writing any JSON, output your three extracted token lists to the user as plain text** — colors, spacing tokens, and decorative effects. For each token, mark its provenance:

- `EXTRACTED` — value came directly from a CSS rule or inline style in the source HTML
- `INFERRED` — value was guessed (because the HTML left it unspecified for this element)

Then, if there are any `INFERRED` items, ask the user to confirm each one via AskUserQuestion (batched into a single set of 1–4 questions) before generating. Only proceed to JSON when every token is either `EXTRACTED` or has a user-confirmed answer for the `INFERRED` case.

This output is non-negotiable. A subagent reading this skill may not skip the output step on the basis that "no ambiguity exists" — write the lists, classify each item, ask if needed. The cost is small; the benefit is that the user can spot a wrong inference before it's baked into JSON.

Example batched question set when the HTML left two values unspecified:

1. The pricing-tier icons (clock, moon, alert) have no explicit `color` in your HTML. Should they use the brand red (`#E63946`), the brand navy (`#0B2C4D`), or something else?
2. The inner padding of the `.tier-card.featured` card isn't declared. Should it match the `.tier-card` 32px, or something different for the highlighted card?

Phrase each question concretely — name the element, list 2–4 plausible options when you have them, and ask for the user's choice.

## Workflow

1. **Build HTML/CSS first.** Match the design pixel-perfect in plain HTML before touching JSON. Catches layout bugs cheaply, before they're buried under verbose settings objects.
2. **Extract design tokens and output them** (per the *Design fidelity* section above). List every color, every padding/margin/gap, every decorative pseudo-element, every hover effect, every animation. Output the three lists to the user as plain text, marking each item `EXTRACTED` or `INFERRED` per *Output your token lists before generating*. Keep a running log of every `INFERRED` value — this log is the input to the auto-verify trigger in step 6.
3. **Compare screenshots side-by-side.** Iterate the HTML until it matches.
4. **Confirm inferred tokens.** From the lists you output in step 2, ask the user to confirm every `INFERRED` value via a single batched AskUserQuestion call (see the worked example in *Output your token lists before generating*). If all tokens are `EXTRACTED`, skip this step. Do not invent defaults. Do not silently fall back to theme blue, black, or any guess.
5. **Translate to JSON.** Map each section to a container, each text/button/etc. to a widget. Maximize native widgets; minimize HTML widgets. Run the decision tree above on every block of content. Every color, padding, margin, and decorative effect must trace back to your extracted token lists.
6. **Conditional auto-verify via fresh subagent.** You will hallucinate — every generator does. Two distinct failure modes both warrant a fresh-subagent pass: (a) reaching for an `html` widget when a native widget combination would have worked; (b) **design drift** — wrong colors, wrong padding, dropped `::before` connectors, dropped `:hover` effects — even in all-native-widget JSON. The trigger below covers both.

   **Trigger check.** After writing the JSON, before reporting done:

   ```bash
   grep -c '"widgetType": "html"' <path-to-your-json>
   ```

   Also check whether an HTML reference file exists in cwd next to the JSON — using the same logic the `/verify-elementor` slash command uses: exact stem match (`<json_stem>.html`), or substring fallback to any `.html` in cwd whose name shares a prefix or substring with the JSON stem case-insensitively, newest by mtime. And recall whether your token-extraction step 2 logged any `INFERRED` items.

   Auto-verify is **MANDATORY** when ANY of the following is true:
   - (a) `grep -c '"widgetType": "html"'` ≥ 1,
   - (b) an HTML reference file is available in cwd (matching the json stem exactly OR via case-insensitive substring),
   - (c) you inferred any color/padding/effect during token extraction.

   In practice: if an HTML reference exists, **ALWAYS auto-verify** — it's a free design-fidelity audit and the user explicitly required this.

   **Skip auto-verify ONLY when**: zero html widgets AND no HTML reference in cwd AND zero inferences logged. In all other cases, auto-verify is mandatory.

   - **Skip path → file is ready.** Tell the user the file is ready; they can still run `/verify-elementor` manually if they want a second opinion.
   - **Trigger fired → MANDATORY auto-verify.** Dispatch a single fresh subagent (via the Agent tool, `subagent_type: "general-purpose"`) with a self-contained prompt telling it to:
     - Read the `elementor-verify` skill at `/Users/ismailmasroor/.claude/skills/elementor-verify/SKILL.md` in full
     - Audit the JSON file you just wrote (pass the absolute path)
     - Fix violations in-place using the Edit tool
     - Return the report in the format the skill specifies

     The dispatched prompt must also include this line so the subagent can run the design-fidelity audit:

     > Target JSON file: `<path>`. Source HTML reference: `<html path>` (or `(none)` if not found).

     Treat the subagent's report as part of your output. If `VERDICT: PASS`, show the user the **full report verbatim** (not a paraphrase — the user wants to see exactly what was found, fixed, and justified) and only then say the file is ready. If `VERDICT: FAIL`, do NOT call the file done — show the user the full report verbatim and tell them to run `/verify-elementor` again to re-dispatch a fresh subagent.

   **Why fire automatically before the user even runs `/verify-elementor`:** the fresh subagent lacks your writer's context, so it catches things you can't see — wrong color choices, drifted padding, dropped pseudo-element decorations, missing hover effects, as well as ~99% of `html` widgets that turn out to be convertible to native widgets + `custom_css`. If the trigger fires, prove your output was faithful by sending a fresh agent to check.

   **Do not skip the auto-verify when the trigger fires** by reasoning "I'm confident this is right" — that's exactly the writer's-context blind spot the fresh subagent exists to break. The cost of a single subagent call is tiny compared to the cost of shipping a template where the client can't edit a heading or where the colors and spacing drifted from the design.
7. **Validate by importing.** WordPress → Elementor → Tools → Import / Export → Import Templates. Upload the JSON and drop the template into a page. It's correct only if (a) the layout matches, AND (b) every value the client might edit is reachable through the Elementor UI controls — not just visible.
8. **Test responsive at 1024px (tablet) and 760px / 767px (mobile)** in Elementor's preview. Don't ship without checking both.

## Top-level shape

```json
{
  "content": [/* container objects */],
  "page_settings": {"hide_title": "yes"},
  "version": "0.4",
  "title": "template-name",
  "type": "container"
}
```

`type: "container"` uses the modern flex container model. Don't use legacy `section`/`column` types unless the target site is on a pre-3.16 Elementor that hasn't migrated.

## Container width convention

Many designs specify a fixed inner content width (1140, 1200, 1300, 1320 px). Set this on the **inner** container of each section, not the outer full-bleed wrapper:

```json
{
  "content_width": "boxed",
  "boxed_width": {"unit": "px", "size": 1300, "sizes": []}
}
```

The outer section container stays `content_width: "full"` so its background extends edge-to-edge; the inner container is `"boxed"` with the project's chosen width. If the user states a width like "1300px container," use that exact number across every section for consistency.

## Design philosophy: distinct layouts, shared DNA

When building multiple pages for one site, keep the **design tokens identical** (colors, fonts, eyebrow chip, accent underline, dot-grid background, button hover behavior, FAQ accordion look, final CTA section) while making the **layout silhouette of each page distinct**. The fastest cloning failure: building the about page hero using the same centered-catalog layout as the services page. Even with different content, the layouts read as twins and the site loses depth.

A safe heuristic: vary the hero's **text alignment** (centered vs left-aligned), the **right-side visual** (none vs portrait vs data mockup vs icon grid), and the **strip below the hero** (no strip vs counter row vs milestone timeline vs trust pills). If any two pages match on all three, the layouts will feel identical. See `references/patterns.md` for the three documented hero rhythms.

## Breakpoint principle: iPad keeps splits, mobile stacks

Elementor's defaults: 1024px (tablet boundary) and 768px (mobile boundary). For most two-column splits — hero text+visual, story image+content, founder image+bio — keep the side-by-side layout through iPad and only stack on phones. Set `flex_direction: "row"` + `flex_direction_mobile: "column"`, leaving `flex_direction_tablet` empty so iPad inherits the row layout. Stacking too early on iPad wastes the wider screen and makes the design feel mobile-first.

Card grids are the exception — they're usually 3-up on desktop, 1-up on tablet AND mobile (or 2-up tablet / 1-up mobile for 4-card layouts).

## Reference files

- **`references/containers.md`** — container settings, responsive variants (`_tablet`/`_mobile`), borders, hover states, background images, hierarchy rules, common section patterns.
- **`references/widgets.md`** — copy-paste templates for native widgets: heading, text-editor, button, image, icon, icon-box (default + framed-circle variants), icon-list, accordion, counter, star-rating, divider, gallery, video, html, progress, testimonial, spacer, menu-anchor, alert, read-more.
- **`references/pro-widgets.md`** — Elementor Pro–only widgets: form, nested-accordion, plus marketing-flavor extras (call-to-action, image-box, animated-headline, price-table, price-list, flip-box, slides, media-carousel, lottie, google_maps, nested-tabs, nested-carousel, progress-tracker, social-icons, hotspot, image-carousel, reviews, e-paragraph, code-highlight, blockquote, share-buttons, template).
- **`references/pro-widgets-dynamic.md`** — query-driven content widgets that render via a separate inner template + query params (loop-grid, loop-carousel, posts, portfolio, taxonomy-filter).
- **`references/pro-widgets-navigation.md`** — header/footer template-part widgets and time/utility blocks (nav-menu, mega-menu, off-canvas, table-of-contents, search, login, countdown).
- **`references/custom-css.md`** — when `custom_css` is justified, the `selector` keyword, JSON escaping, common patterns (pulse dots, gradient underlines, icon-list restyling, brute-force flex overrides, receipt/timeline shells, button hover text).
- **`references/patterns.md`** — concrete section recipes: eyebrow chip as a heading widget, section step tag, accent underline, three hero variants (centered catalog, asymmetric split, receipt mockup), three stat-strip variants (counter row, milestone timeline, light trust strip), card grids with hover-lift, featured tier with Most Popular badge, service-block alternating layout, image with floating overlay badge, marquee sticky nav, gradient final CTA, disclaimer card with red left-border, founder blockquote, signature row.
- **`references/gotchas.md`** — common mistakes, ID conventions, settings that silently no-op without their activation flag, FAQ accordion underline fix, button hover-text invisibility fix, mobile flex reordering, brute-force `!important` overrides.

**Read `patterns.md` first** when building a page from scratch — most of what the design needs is already a documented recipe. Then dip into `widgets.md` / `custom-css.md` / `gotchas.md` for the specific widget or fix you need. Don't try to remember the full schema — copy a template, then customize.
