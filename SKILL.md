---
name: design-system-docs
description: Generates designer-facing documentation for design system components from a link (Figma file, page, frame, or node URL). Studies each component's variants, properties, states, and styling, then writes a document covering usage guidelines, when to use each variation, available properties, dos and don'ts, and accessibility considerations. Use whenever the user shares a link to a group of components and asks to document, describe, write guidelines for, or audit a component library or design system, even if they don't say "documentation" (e.g. "write up these buttons", "what's in this component set", "create usage guidelines for this file"). Never pushes anything to Figma until the user explicitly approves the draft.
---

# Design System Docs

Turn a link to a group of design system components into documentation a designer can act on. The audience is designers, so write about choices, behaviour, and properties as they appear in the design tool, not about code.

## Hard rule: no pushing to Figma before approval

Documentation is drafted and reviewed in the conversation first. Do not write, publish, or modify anything in Figma until the user has clearly said they are happy with the documentation and asked for it to go to Figma. Reasons: Figma writes are hard to undo cleanly, and designers' files are shared with teams. "Looks good" about one section is not approval of the whole document; confirm before pushing.

## Workflow

### 1. Intake
- Accept the link. If none was given, ask for it.
- Parse the Figma URL: `fileKey` is the segment after `/design/` or `/file/`; `node-id` in the URL uses hyphens (`1-13069`) but the API expects colons (`1:13069`).
- If the link isn't Figma (Storybook, docs site), fetch it with `web_fetch` and adapt the same workflow.

### 2. Study the components
Load Figma tools if needed (`tool_search` for "figma"), then call `get_figma_data` with the `fileKey` and `nodeId`. For large nodes, fetch the top level first to list the components, then fetch each component or component set individually so nothing is truncated.

For every component or component set, record:
- **Name and purpose** (infer from name, description, and structure; flag as inferred)
- **Variants and variant properties** (e.g. Type, Size, State) with all values
- **Component properties**: boolean, text, instance-swap, with defaults
- **States** present (default, hover, pressed, focus, disabled, error, loading) and notably **states missing**
- **Anatomy**: layers, slots, icons, text styles
- **Styling**: color tokens/styles, typography, spacing, radius, elevation, sizes (note raw values when no token exists)
- **Layout behaviour**: auto layout, constraints, resizing, min/max sizes
- **Relationships**: components that nest inside or commonly pair with this one

Never invent properties or values. If something can't be determined from the data, write "Not specified in the file" and list it under open questions. Fabricated specs are worse than gaps because designers will trust them.

### 3. Draft the document
Follow `references/doc-template.md` for structure. Cover, per component:
1. Overview and when to use / when not to use
2. Variations: what each variant is for and how to choose between them
3. Properties table: name, type, values, default, effect
4. Usage guidelines: placement, sizing, content, spacing, composition
5. Dos and don'ts: concrete, tied to this component's actual variants
6. Accessibility: use `references/accessibility.md`. Separate what is **observed** in the file (e.g. a focus state exists) from what is **recommended** (e.g. minimum target size), because the designer needs to know which is fact and which is advice.

Start with a short group-level overview (what the set covers, shared conventions, token usage) and end with an **Open questions** section.

Output format: if Claude Docs tools are available, create a Claude Doc; otherwise produce a Markdown file and present it. Keep it structured and free of filler, and give the rationale behind each guideline in a clause ("Use sentence case, because mixed case reduced scan speed in dense toolbars") only when the rationale is real, not decorative.

### 4. Review loop
Present the draft and ask the user what to change. Iterate section by section or whole-document, whichever they prefer. Edit the existing document rather than regenerating it, so approved parts stay stable. Track what the user has approved.

### 5. Push to Figma (only on explicit approval)
When the user says they're happy and wants it in Figma:
1. Re-confirm in one line what will be pushed and where (new page, frames beside the components, component descriptions).
2. Check which tools can write to Figma. Figma read tools (like Framelink) cannot write. If no write-capable tool is available, say so plainly and offer alternatives: export the doc (Markdown/PDF/Word), paste component descriptions into Figma, or provide the content structured for a Figma documentation plugin.
3. Prefer additive changes (new page or frames) over editing existing components.

## Quality bar
- Every claim traces to the file or is labelled inferred or recommended.
- Guidelines are specific to these components, not generic design-system advice.
- Missing states, token gaps, and inconsistencies are surfaced, not smoothed over.
