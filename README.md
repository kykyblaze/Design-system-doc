# Design System Docs

An AI agent skill that turns a link to a group of design system components into documentation designers can act on.

Share a: 
Figma file
Storybook link
Page
Frame
Node URL. 

The agent studies each component's variants, properties, states, and styling, then drafts usage guidelines, a properties table, dos and don'ts, and accessibility notes. It **never writes to Figma until you explicitly approve the draft**.

---

## Features

- **Reads the real file.** Pulls component data straight from Figma, so the specs come from the file and aren't made up.
- **Written for designers.** Covers choices, behaviour, and properties as they appear in the design tool, not code.
- **Consistent structure.** Every component gets the same sections, defined in [`references/doc-template.md`](references/doc-template.md).
- **Honest about gaps.** Missing states, token gaps, and inconsistencies are called out. Anything that can't be determined is marked *"Not specified in the file"* and added to an **Open questions** section.
- **Accessibility built in.** Uses a WCAG 2.2-based checklist ([`references/accessibility.md`](references/accessibility.md)) and labels each point **Observed** (verified in the file) or **Recommended** (guidance).
- **Review before publishing.** You iterate on the draft in the conversation. Nothing goes to Figma until you say so.

## What the output covers

For the component group as a whole:
- Overview of the set, shared conventions, and token usage
- Gaps or inconsistencies across components
- Open questions

For each component:

| Section | Contents |
|---|---|
| Overview | What it is, when to use it, when not to (and what to use instead) |
| Anatomy | Numbered list of parts |
| Variations | Each variant, its purpose, and when to choose it |
| Properties | Name, type, values, default, effect |
| States | States present, plus expected states that are missing |
| Usage guidelines | Placement, sizing, spacing, content, composition |
| Dos and don'ts | Concrete rules tied to the component's actual variants |
| Accessibility | Observed vs. recommended: contrast, focus, target size, labels, roles |

```

## Requirements

- An AI agent that supports the `SKILL.md` skill format (e.g. Claude, Antigravity, or similar agents that load skills from a folder).
- **A Figma MCP server** so the agent can read Figma files.
- *(Optional)* A **write-capable** Figma tool if you want the approved docs pushed back into Figma. If no write tool is available, the agent will offer to export the doc instead (Markdown/PDF/Word, component descriptions to paste, or content for a documentation plugin).

> **Non-Figma sources:** if you share a Storybook or documentation site link, the agent fetches the page and follows the same workflow.

## Installation

Clone the repo into your agent's skills directory:

```

Common locations:

| Agent | Global skills | Project skills |
|---|---|---|
| Claude Code | `~/.claude/skills/design-system-docs/` | `.claude/skills/design-system-docs/` |
| Antigravity | `~/.gemini/config/skills/design-system-docs/` | `.agents/skills/design-system-docs/` |

For Claude.ai, zip the folder and upload it under **Settings → customise → Skills**.

Make sure the folder contains `SKILL.md` at its root.

## Usage

Share a link and ask for documentation. You don't need to say "documentation". The skill triggers on requests like:

- "Document the components in this file: `https://www.figma.com/design/AbC123/My-DS?node-id=1-13069`"
- "Write up these buttons"
- "What's in this component set?"
- "Create usage guidelines for this file"
- "Audit this component library"

### Workflow

1. **Intake.** The agent parses the URL. `fileKey` is the segment after `/design/` or `/file/`, and `node-id` `1-13069` becomes `1:13069` for the API.
2. **Study.** It fetches the node. For large files it lists the components first, then fetches each one on its own so nothing gets truncated.
3. **Draft.** It writes the document using the template, with a group overview at the top and open questions at the end.
4. **Review.** You ask for changes section by section or for the whole document. The agent edits the existing draft instead of regenerating it, so sections you've approved stay as they are.
5. **Push (optional).** Only after you explicitly approve, the agent confirms what will be pushed and where, then makes changes that only add content (a new page or frames) and doesn't edit your existing components.

> **Why the approval gate?** Figma writes are hard to undo cleanly, and design files are shared across teams. "Looks good" about one section doesn't count as approval of the whole document.

## Quality bar

- Every claim traces back to the file, or is labelled *inferred* or *recommended*.
- Guidelines are specific to the components in the file, not generic design system advice.
- Missing states, token gaps, and inconsistencies are surfaced, not smoothed over.
- The skill never claims WCAG conformance. It states which criteria were checked and what the result was.

## Customizing

- **Change the output structure:** edit [`references/doc-template.md`](references/doc-template.md).
- **Adjust accessibility criteria:** edit [`references/accessibility.md`](references/accessibility.md), for example to add platform-specific target sizes or your organisation's contrast rules.
- **Change triggers or behaviour:** edit the `description` frontmatter and workflow in [`SKILL.md`](SKILL.md).