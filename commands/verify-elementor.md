---
description: Verify the latest Elementor JSON in cwd (or a section by name) for widget-first compliance via a fresh subagent. Use after generating an Elementor template to catch hallucinated HTML or excessive custom_css + design fidelity if matching .html reference exists.
argument-hint: [section-name]
---

You have been invoked as `/verify-elementor`. The argument (may be empty) is: **$ARGUMENTS**

You must verify an Elementor JSON template for widget-first compliance. **You must NOT analyze or fix the file yourself.** All audit and all fixes happen inside a single fresh subagent — that is the whole point of this command (a fresh context breaks any hallucination chain carried over from whichever conversation generated the JSON). The user has explicitly required strict subagent-only fixing; respect that even if a fix looks trivial.

## Step 1 — Resolve the target JSON

Find the target Elementor JSON in the user's current working directory using the Bash tool. The naive `ls -t *.json` will match `package.json`, `tsconfig.json`, `composer.json` etc. which are not Elementor templates, so filter to files containing Elementor markers (`"widgetType"` or `"elType"`).

The argument may be empty or a section-name substring. Build the bash pipeline accordingly:

- **No argument** — iterate `*.json` newest-first, pick the first one containing `"widgetType"` or `"elType"`.
- **With argument** — same, but additionally filter the filename list with a case-insensitive substring match on the argument.

A working one-liner (adapt for whichever case applies, dropping the `grep -i` filter when no argument):

```bash
ls -t *.json 2>/dev/null \
  | grep -i -- '$ARGUMENTS' \
  | while read f; do
      grep -q -E '"widgetType"|"elType"' "$f" && { echo "$(pwd)/$f"; break; }
    done
```

When `$ARGUMENTS` is empty, omit the `grep -i` stage entirely (an empty pattern would match every line, which is fine, but cleaner to skip it).

Interpret the result:

- **Empty result, no argument** — Tell the user: "No Elementor `.json` files found in `$(pwd)`. Run `/verify-elementor` from the directory that contains your Elementor template, or check that the JSON has Elementor structure (`widgetType` / `elType` keys)." Then STOP. Do not dispatch.
- **Empty result, with argument** — Tell the user: "No Elementor `.json` files matching `$ARGUMENTS` found in `$(pwd)`. Try `/verify-elementor` with no argument, or check the section name." Then STOP.
- **One path returned** — That is the target. Continue to HTML reference resolution below.

### Resolve the HTML reference (optional)

After the target JSON is resolved, attempt to find a matching `.html` reference file in cwd so the subagent can perform the optional design-fidelity audit (color drift, padding drift, missing decorative CSS, missing hover effects). The HTML reference is auto-detected — never passed as an argument.

Resolution strategy:

1. Take the JSON filename without its extension (e.g. `pricing-section.json` → `pricing-section`).
2. Look for an `.html` file matching that exact stem (`pricing-section.html`). If found, use it.
3. If not found, look for any `.html` file in cwd whose name shares a prefix / substring (case-insensitive) with the JSON stem. Prefer the newest by mtime.
4. If nothing is found, no HTML reference is available — design-fidelity audit will be skipped by the subagent; widget-first + universal `isLinked` audit still runs.

A working bash pattern:

```bash
JSON_STEM="$(basename "$TARGET_JSON" .json)"
# Try exact stem match first
HTML_REF="$(ls -t "${JSON_STEM}.html" 2>/dev/null | head -1)"
# Fallback: substring match
if [ -z "$HTML_REF" ]; then
  HTML_REF="$(ls -t *.html 2>/dev/null | grep -i "$JSON_STEM" | head -1)"
fi
# Absolute path if found
[ -n "$HTML_REF" ] && HTML_REF="$(pwd)/$HTML_REF"
```

Briefly tell the user:

- If an HTML reference was found: "Verifying `<absolute json path>` against `<absolute html path>` via a fresh subagent."
- If no HTML reference: "Verifying `<absolute json path>` (no HTML reference found — design-fidelity audit will be skipped, widget-first audit still runs)."

## Step 2 — Dispatch a single fresh subagent

Use the Agent tool with `subagent_type: "general-purpose"`. The subagent has no memory of this conversation — its prompt must be fully self-contained. Use this template (substitute `<TARGET_JSON>` with the absolute path you resolved, and `<HTML_REF>` with the absolute HTML reference path if found — otherwise the literal string `(none — skip design-fidelity audit)`):

> You are auditing an Elementor JSON template for widget-first compliance AND design fidelity. Read the `elementor-verify` skill at `/Users/ismailmasroor/.claude/skills/elementor-verify/SKILL.md` in full — it contains the complete decision trees, the universal padding `isLinked` check, the optional design-fidelity audit, the high-frequency-mistakes scan list, the fix-in-place workflow, and the required output format.
>
> Target JSON file: `<TARGET_JSON>`
> Source HTML reference: `<HTML_REF or "(none — skip design-fidelity audit)">`
>
> Read both files (if HTML provided), audit per the skill's three categories: (1) widget-first compliance, (2) universal `isLinked: false` on all responsive paddings/margins, (3) design fidelity if HTML provided. Fix violations in-place using the Edit tool on the JSON. After fixes, re-read the file and confirm remaining issues are zero (or fully justified). Return the report in exactly the format the skill specifies — no preamble, no postscript.
>
> Do not ask the calling agent any clarifying questions. Make judgment calls strictly per the skill's decision trees.

Description on the Agent tool call: "Verify Elementor JSON widget-first compliance".

Do not pass any context from this conversation that isn't in the template above. The whole reason for dispatching fresh is to break carry-over context.

## Step 3 — Present the verdict to the user

After the subagent returns, parse the `VERDICT:` line.

- **VERDICT: PASS** — Show the user the **full report verbatim** (not a summary — the user should see exactly what was found, fixed, and justified). Then confirm based on which audits ran:
  - If an HTML reference was found and passed to the subagent: "Widget-first compliance + design fidelity verified. The file is ready to import into Elementor."
  - If no HTML reference was found: "Widget-first compliance verified (no HTML reference found, so design-fidelity audit was skipped). If you want a design-fidelity audit, place the source `.html` next to the `.json` and re-run `/verify-elementor`."
- **VERDICT: FAIL** — Show the user the **full report verbatim**. Then tell them: "Verification failed. Run `/verify-elementor` again to re-dispatch a fresh subagent, or open the file and address the unresolved items manually. I will not attempt fixes myself — your requirement is strict subagent-only fixing." Then STOP.

You may not bypass the subagent for a "simple" or "obvious" case. You may not propose fixes yourself in the FAIL path. The user's hard requirement is that all verification and all fixing happens via a fresh subagent context.
