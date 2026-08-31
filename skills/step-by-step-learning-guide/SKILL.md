---
name: step-by-step-learning-guide
description: Create attractive, self-contained, step-by-step HTML learning guides for worked problems and derivations (maths, physics, engineering, circuits, control systems, statistics), designed for readers who need material broken into small chunks — including ADHD-friendly study material. Use this skill whenever the user asks for a "learning guide", "study guide", "revision guide", "walkthrough", or "worked example with every step shown", asks to expand or unpack a worked solution or tutorial, asks to make maths teaching material "attractive", "visual", "colourful", or "ADHD friendly", or uploads tutorial/problem-set PDFs and wants them turned into learnable material. Also use it when extending or restyling a guide previously made with this skill.
---

# Step-by-step learning guide

Produce a single self-contained HTML file that walks a reader through one worked problem in small, tickable steps, with rendered maths (KaTeX), colour-coded symbols, and comprehension checkpoints. The format was designed for readers with ADHD but works for anyone: the core moves are chunking, visible progress, and never making the reader hold two ideas in their head at once.

## Workflow

1. **Solve and verify the problem numerically first.** Before writing a single line of HTML, reproduce every number in the solution with Python (`bash_tool`). Compute intermediate values (coefficients, roots, residues, times) to more precision than you will display, and check them against any provided answer sheet. A beautiful guide with a wrong number is worse than no guide. Also compute the *verification* numbers the guide will use (e.g. the size of a neglected term, a limit check) — the guide should show those checks, so you must know they pass.
2. **Break the solution into a route of 6–12 steps.** Each step makes exactly one conceptual move (e.g. "write the loop equations", "find the poles"). If a step needs two moves, split it. Estimate 2–5 minutes per step and say so.
3. **Assign one colour per recurring entity** (a circuit component, a variable, a matrix, a force). That colour follows the entity from the diagram through every line of algebra, so a lost reader can trace any term back to its origin. Four to five colours maximum; more becomes noise.
4. **Copy `assets/template.html`** into the working directory and fill it in. It contains the full CSS, the KaTeX setup with working colour macros, and one skeleton step showing every element in place.
5. **Draw diagrams as inline SVG**, using the same CSS colour variables as the algebra. For circuits and geometry, a hand-built SVG at ~640×240 viewBox is enough; label parts in their colours. When the analysis introduces new structure (mesh currents, free-body forces, auxiliary lines), draw a second version of the diagram with that structure added rather than overloading the first.
6. **Save to `/mnt/user-data/outputs/` and present the file.**

## Anatomy of the guide

Top matter, in order:
- Title and one-line subtitle.
- A short "How to use this guide" tip box: one step at a time, tick the box, breaks are fine.
- Colour legend mapping each colour to its entity, with values.
- The problem diagram (SVG) with caption.
- **The mission in one sentence**, highlighted, followed immediately by the final answer ("Answer to aim for: …"). Stating the destination up front reduces anxiety and gives every step a purpose; this is deliberate, not a spoiler.
- A "what you need coming in" box listing the (few) assumed facts.
- The route: an anchored list of all steps with per-step time estimates.

Each step section contains, in order:
- A numbered header with a "Done" checkbox (the template dims completed steps via CSS).
- A one-line **goal** ("Goal: eliminate I₁, leaving one equation in I₂").
- The working, with **every algebraic move on its own display line** — expansions shown term-by-term (a small table works well for products), substitutions written out before simplifying, discriminants computed on their own line. Never write "which simplifies to". If a course's own solution skips steps, this guide is where they get un-skipped.
- Zero or more callout boxes: `why` (blue — motivation, meaning, where a term came from), `watch` (orange — a common slip, placed at the exact spot students actually err), `tip` (green — a pause-and-notice or a reusable trick).
- A collapsible **checkpoint**: one question testing understanding (not recall), with the answer hidden in a `<details>` element. Good checkpoints ask "why", "what does this mean physically", or "predict before computing".

End matter:
- **Recap card**: the whole route compressed to one numbered list, one line per step.
- **Sanity checks**: 3–5 ten-second checks (limits, initial/final values, root sum-and-product, order-of-magnitude) the reader can run on their own future work.
- **Where to go next**: a pointer to related problems, mapped to which steps of this route they exercise.

## Writing rules

- **Self-contained by default.** Introduce every concept (KVL, transfer function, cover-up method, whatever the domain needs) in a short box at the point of first use. Do not reference "the lecture", "the worked example", or other documents unless the user asks for a companion piece.
- Short sentences, one idea each. Prose between equations narrates ("Move the I₁ term across:", "Factor it out:") — never a wall of unexplained displays.
- Where an approximation is made, dedicate a `why` box to justifying it and **verify it numerically at the end** ("plug t back in: the dropped term is 0.2 mV against a 2.5 V target").
- Give physical readings of mathematical facts wherever possible (a zero at s = 0 means no DC current; residues sum to zero because the capacitor starts empty).
- The big final answer gets its own large highlighted line.
- Match the reader's regional spelling throughout (e.g. Australian English: colour, behaviour, analyse).
- No emoji or decorative icons; the callout colours and shapes carry the visual hierarchy.
- If the guide accompanies a problem the reader should attempt themselves, frame it attempt-first: "try each step on paper, then open the working to check", and keep answers inside the collapsibles.

## Technical notes (learned the hard way)

- **KaTeX colour macros: never put a hex colour inside a macro definition.** `"\\ind": "\\textcolor{#4338CA}{#1}"` silently breaks — the `#4` is parsed as a macro parameter and the maths fails to render. Instead use `\htmlClass` with `trust: true` (already set up in the template) and colour the classes in CSS. This also keeps colours in one place (the CSS variables).
- KaTeX loads from cdnjs (`katex.min.js` + `auto-render.min.js`); delimiters are `$$ … $$` for display and `\( … \)` for inline. Set `throwOnError: false`.
- Give `.katex-display` horizontal overflow scrolling so long equations survive on mobile.
- Checkbox "Done" state and `<details>` are pure HTML/CSS — no JavaScript state, and **no localStorage** (it fails in artifact iframes). State lasting only while the page is open is acceptable and should be mentioned in the how-to box.
- Respect `prefers-reduced-motion`; the only motion should be user-triggered (opening a checkpoint, anchor scrolling).
- Superscripts/subscripts inside SVG `<text>` use `<tspan dy>` shifts, or just use Unicode subscripts (I₁, R₂) at this size.

## Quality bar before presenting

- Every displayed number re-derivable from your verification script; residues/limits/answer cross-checked.
- No dangling references to material outside the file.
- Each step has a goal line and a checkpoint; each `watch` box sits at a genuine error site, not generic advice.
- Open-and-skim test: a reader who reads only the goal lines, the highlighted mission, and the recap card should still come away with the route.
