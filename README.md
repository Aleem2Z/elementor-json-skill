# elementor-json

Claude Code skill for converting designs into Elementor template JSON for WordPress.

## What it does

When you ask Claude to turn a screenshot, Figma file, or HTML mockup into an Elementor template, this skill loads the schema and conventions Claude needs to produce JSON that imports cleanly via **Elementor → Tools → Import / Export**.

It covers:

- **The critical rule**: use Elementor JSON settings (not `custom_css`) for layout, spacing, typography, colors — otherwise the controls panel and responsive breakpoints break.
- **Workflow**: build HTML first, validate visually, then translate to JSON, import-test in WordPress.
- **Container reference**: full settings, responsive variants (`_tablet`/`_mobile`), hierarchy rules, common section patterns (hero, card grid, two-column).
- **Widget catalog**: copy-paste templates for heading, text-editor, button, image, video, html, form (Pro), nested-accordion (Pro).
- **`custom_css` guide**: when it's justified (pseudo-elements, SVG backgrounds, `backdrop-filter`), the `selector` keyword, JSON escaping, common patterns.

## Install

```
/plugin marketplace add aleem2z/elementor-json-skill
/plugin install elementor-json@aleem2z-skills
```

Then use Claude normally — the skill auto-triggers when your request looks like Elementor template work.

## Manual install (no marketplace)

Clone into your skills directory:

```bash
git clone https://github.com/aleem2z/elementor-json-skill.git ~/.claude/skills/elementor-json-skill
ln -s ~/.claude/skills/elementor-json-skill/skills/elementor-json ~/.claude/skills/elementor-json
```

## Layout

```
skills/elementor-json/
├── SKILL.md                  # Core rules, workflow, top-level shape
└── references/
    ├── containers.md         # Container settings, responsive, hierarchy, patterns
    ├── widgets.md            # heading, text-editor, button, image, video, html, form, nested-accordion
    └── custom-css.md         # When it's justified, `selector` keyword, escaping, examples
```

`SKILL.md` stays lean (always loaded). Reference files are only pulled in when Claude is actually building that thing.

## Contributing

Found a widget setting that's documented wrong? A breakpoint quirk? Open an issue or PR. Ground-truth comes from actual `.json` files exported from Elementor — when in doubt, export a template you've configured in the editor and check what keys/values Elementor itself writes.

## License

MIT
