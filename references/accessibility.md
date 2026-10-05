# Accessibility Checklist for Designer Docs

For each component, cover what applies. Label each point **Observed** (verifiable in the file) or **Recommended** (guidance).

## Visual
- **Color contrast**: text vs background at least 4.5:1 (3:1 for large text 18pt+/14pt bold); UI boundaries and icons at least 3:1. Check every variant and state, especially disabled-looking and tinted variants. Compute from actual fill values when available.
- **Not color alone**: errors, selection, and status need a second cue (icon, text, shape).
- **Focus indicator**: a visible focus state with 3:1 contrast against adjacent colors. Flag if the file has none.
- **Text sizing**: note smallest text size; text must survive 200% zoom without clipping, so check fixed-height containers.

## Interaction
- **Target size**: at least 24x24 CSS px minimum (WCAG 2.2 AA); 44x44 recommended for touch. Report actual sizes per Size variant.
- **States**: hover, focus, pressed, disabled, error all distinguishable.
- **Motion**: note animations; recommend reduced-motion alternatives.

## Content and semantics (what designers influence)
- **Labels**: every interactive element has a visible or programmatic label; icon-only components need an accessible name (recommend specifying it in the doc).
- **Roles and order**: state the intended role (button, link, tab, etc.) and logical reading/focus order for composite components.
- **Error and helper text**: tied to the field, not only placed near it.
- **Touch and pointer**: avoid hover-only information.

Do not claim WCAG conformance for a component; state which criteria were checked and the result.
