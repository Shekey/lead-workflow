# Design guide (Figma)

This guide assumes a Figma connector that can read files (design context, metadata, screenshots, variables, library search) and, for write work, change them. Tool names differ between connectors; use whichever tool does the job.

If the connector ships its own skills (for example a skill you must load before writing to the canvas), load and follow them. They take precedence over this guide on how to call the tools.

## Orient

- Identify the file, page and frames the task is about. If the user gave no link or node, ask for one.
- Find the design system: connected libraries, components, variables (color, spacing, radius, typography) and text styles.
- Look at 1-2 existing screens that are close to the task. New work matches their structure, spacing and naming.

## Trivial examples

Changing copy, swapping an icon or image, switching a component variant, adjusting one property on a single frame. Nothing that touches shared components, variables or library styles.

## Constraints for briefs

- Use library components and variables. No hard-coded colors, spacing, radii or font sizes where a variable or style exists.
- No detaching instances unless the brief explicitly allows it.
- Auto layout for anything that should resize. Constraints set sensibly for anything that doesn't.
- Name layers meaningfully; no "Frame 123" in delivered work.
- Change only the frames or components you own.
- Never publish a library, and never edit main components or variables, unless the brief says so.
- When the connector supports branches, do Standard and Large write work on a branch.

## Checks

1. Every color, spacing, radius and type value is bound to a variable or style, or there's a stated reason it isn't.
2. No unintended detached instances.
3. Text contrast meets WCAG AA: 4.5:1 for body text, 3:1 for large text and UI elements.
4. Auto layout behaves: resize the frame and check that nothing breaks.
5. Required states and variants exist (hover, focus, disabled, error, empty, loading) where the component calls for them.
6. Layer names are clean.

## Review criteria

The reviewer gets the brief, a screenshot of each changed frame, and the node structure.

- Does it meet the acceptance criteria and solve the user's actual problem?
- Consistent with the existing screens and the design system.
- Visual hierarchy, alignment and spacing rhythm.
- Accessibility beyond contrast: target sizes, focus order, readable type sizes.
- Edge cases: long text, missing images, empty and error states.
- Ready for handoff: a developer could build it without guessing.

## Risky (always add an `opus` reviewer)

Changes to main components, variables or styles used across the file, library publishing, deleting or restructuring pages, anything touching a shared library that other files consume.

## Finish

Share links to the changed frames. Merge a branch or publish a library only when the user asks.
