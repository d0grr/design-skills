---
name: build-design
description: Builds UI from a Figma file or a design image so it matches the design, using your project's existing tokens and components.
---

# Build design

This skill turns a design into code that matches it. It reads the design at its source, builds with what the project already has, then compares the result against the design before calling it done.

The design decides. Values come from the design file, never from your taste or a pattern you liked elsewhere. A change the design does not show is a deviation, and a deviation is the user's call. Rounding to an existing token is the one exception, and the report lists every one.

It owns no domain rules. Where the design breaks one, name the owning skill and ask: `better-accessibility` for contrast and focus, `better-typography` for type, `better-layout` for spacing and structure, `better-colors` for palette, `better-writing` for copy. Reviewing finished work is `interface-review`, exploring alternatives is `variant`, stress testing a component is `break`.

## 1. Read the design at its source

A Figma link means the Figma MCP. Before saying it is unavailable, check the tool list, since the server is often connected under a name you did not expect. Only when no Figma tool is listed, ask the user to connect one, and do not build from memory or guesswork meanwhile. [figma.md](figma.md) holds which tools to call and what each returns.

A screenshot or exported image has no values to read. Sizes are ratios to the capture, so state the scale you assumed and treat every measurement as an estimate. Ask for the Figma link when one exists, since it replaces the whole estimate.

Scope the run to the frames named. Given a page, list its frames and confirm which ones ship before building. Frames that show the same thing at different widths are one piece at several breakpoints, not separate pieces.

This step is done when you hold, for every frame in scope, a screenshot and the exact values for spacing, size, radius, color, type and the components used.

## 2. Map the design onto the project

Read the project's tokens and component library before writing anything. Then map every design value to what exists:

| In the design | In code |
| --- | --- |
| A bound variable or style | The token with the same name or the same value |
| A raw value that equals a token | That token |
| A raw value with no token | The nearest existing token, listed in the report as rounded |
| A component instance | The project's component of that name, with the variant the design shows |
| A one-off group of layers | Plain markup in place, not a new component |

Never add a token, a component variant or an arbitrary value to hit a number. Where rounding would visibly change the design, such as more than 2px off on spacing, stop and ask which way to go.

Check what data the design needs. Wire it to what exists. Where the backend for part of it does not exist yet, build that part as UI fed by props and name it in the report as unwired.

## 3. Build only what the design shows

Implement the frames, the breakpoints the frames define and the states the file draws, such as hover, empty or error. A state the file does not draw is not yours to design. Leave it to the existing component's default and list it as missing.

Leave everything around the piece as it was. No neighbouring copy edits, no icon swaps, no "while I was here" cleanups. A diff wider than the design is the most common way this goes wrong.

## 4. Compare against the design

Render the build at each frame's width beside the design screenshot and walk it element by element. Compare the same properties step 1 collected. "Gap is 12px, design is 16px" is a finding. "Feels a bit tight" is not.

With a browser at hand, screenshot each width and compare. Without one, hand over the URL with the widths to check and say the comparison is unverified. Do not report a match you did not look at.

Fix every mismatch you caused, then compare again. This step is done when every remaining difference is a rounding or a question.

## 5. Report and stop

| Element | Design | Built | Status |
| --- | --- | --- | --- |
| Card padding | 20px | `p-5` | Matches |
| Title size | 15px | `text-sm`, 14px | Rounded to token |
| Badge | Filled pill | Text label | Deviates, asked: no badge component exists |

Then list, one line each:

- **Unwired.** What renders from props until the backend exists.
- **Missing states.** What the design does not draw.
- **Domain conflicts.** Where the design breaks a rule, with the owning skill.

A run where everything matches ends on the table. Say where it is running and at which widths you compared.

## Before you finish

| Mistake | Fix |
| --- | --- |
| "Figma isn't connected" said without checking | List the tools first; ask for a connection only when none is there |
| Layout rebuilt from the screenshot while the file was available | Read values from the file; the screenshot is for comparison |
| A new token or arbitrary value to hit a design number | Round to the nearest existing token and list it |
| A custom button where the design shows the library's | Use the project's component with the variant shown |
| A one-off element extracted into its own component | Plain markup in place |
| A loading or error state invented | Leave the default and list it as missing |
| Copy, icons or neighbours changed along the way | Revert anything the design does not show |
| A contrast or focus problem fixed silently | Build as designed, report the conflict and its owner |
| "Matches the design" with nothing rendered | Compare at each frame width, or say it is unverified |
| Placeholder data shipped as if wired | Name every unwired part in the report |
