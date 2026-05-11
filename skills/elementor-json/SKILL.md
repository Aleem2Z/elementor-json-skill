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

## Workflow

1. **Build HTML/CSS first.** Match the design pixel-perfect in plain HTML before touching JSON. Catches layout bugs cheaply, before they're buried under verbose settings objects.
2. **Compare screenshots side-by-side.** Iterate the HTML until it matches.
3. **Translate to JSON.** Map each section to a container, each text/button/etc. to a widget. Maximize native widgets; minimize HTML widgets. Run the decision tree above on every block of content.
4. **Conditional auto-verify via fresh subagent — only if you used an `html` widget.** You will hallucinate — every generator does. The high-risk situation is when you reached for an `html` widget out of habit (when a native widget combination would have worked). To save tokens, this auto-verify pass fires **only when triggered**, not for every page.

   **Trigger check.** After writing the JSON, before reporting done, grep the file you just wrote:

   ```bash
   grep -c '"widgetType": "html"' <path-to-your-json>
   ```

   - **Count is `0` → SKIP the auto-verify pass.** Tell the user the file is ready; they can still run `/verify-elementor` manually if they want a second opinion, but you should not burn tokens on a verify when there's nothing for the verifier to catch. (If you also wrote any unusually large `custom_css` block — say, 800+ characters with non-pseudo-element / non-animation rules — verify that block too; otherwise skip.)
   - **Count is `≥ 1` → MANDATORY auto-verify.** Dispatch a single fresh subagent (via the Agent tool, `subagent_type: "general-purpose"`) with a self-contained prompt telling it to:
     - Read the `elementor-verify` skill at `/Users/ismailmasroor/.claude/skills/elementor-verify/SKILL.md` in full
     - Audit the JSON file you just wrote (pass the absolute path)
     - Fix violations in-place using the Edit tool
     - Return the report in the format the skill specifies

     Treat the subagent's report as part of your output. If `VERDICT: PASS`, show the user the **full report verbatim** (not a paraphrase — the user wants to see exactly what was found, fixed, and justified) and only then say the file is ready. If `VERDICT: FAIL`, do NOT call the file done — show the user the full report verbatim and tell them to run `/verify-elementor` again to re-dispatch a fresh subagent.

   **Why fire automatically before the user even runs `/verify-elementor`:** the fresh subagent lacks your writer's context, so it catches things you can't see. The user has explicitly required this auto-pass because ~99% of `html` widgets in generated Elementor JSON turn out to be convertible to native widgets + `custom_css`. If you reached for `html`, prove it was justified by sending a fresh agent to check.

   **Do not skip the auto-verify when html widgets are present** by reasoning "I'm confident this html widget is justified" — that's exactly the writer's-context blind spot the fresh subagent exists to break. The cost of a single subagent call is tiny compared to the cost of shipping a template where the client can't edit a heading.
5. **Validate by importing.** WordPress → Elementor → Tools → Import / Export → Import Templates. Upload the JSON and drop the template into a page. It's correct only if (a) the layout matches, AND (b) every value the client might edit is reachable through the Elementor UI controls — not just visible.
6. **Test responsive at 1024px (tablet) and 760px / 767px (mobile)** in Elementor's preview. Don't ship without checking both.

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
