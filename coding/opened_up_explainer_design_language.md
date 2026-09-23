# "Opened Up" explainers: design language and architecture

This document records the approach used by [The Plane of Focus](https://lens.lab.sael.net/) (a camera lens, opened up) and applied here to `index.html` (an ST3215 servo, opened up). Use it as a template for any "show me how this machine works inside" page.

---

## 1. The idea in one line

**One real mechanism, opened up in 3D, with one invisible quantity made visible, and one slider that changes it.**

| | Lens | Servo |
|---|---|---|
| Mechanism | Camera lens on an optical bench | ST3215 servo on a pedestal |
| Invisible quantity, made visible | The plane of focus: a glowing sheet sweeping through a diorama | The position error: an amber arc from present to goal, plus flows of light for command, drive and feedback |
| Main control | Focus distance (ring and slider) | Goal position (arm and slider) |
| Second variable | Aperture (f/2, f/5.6, f/16) | Load (none, 1, 3, 6 kg) and tuning (Soft, Stock, Hot) |
| Structural toggle | Lens assembled / exploded | Case assembled / exploded |
| Consequence you watch | Circles of confusion grow and shrink | Droop, overshoot, hunting, stall |

A third application, `so100.html` (an SO-100 arm, "The Weight of Reach"), shows how the pattern stretches to a system of parts:

| | SO-100 arm |
|---|---|
| Invisible quantity | The torque at each joint: weight × horizontal distance. It is shown as lever lines to the centre of mass beyond each joint, and rings that fill with the share of stall torque |
| Main control | Reach (fingertip distance), using the beats Tuck / Reach / Up, a slider, or dragging the arm (inverse kinematics in the arm's plane) |
| Second variable | Payload (none to 500 g) and supply voltage (7.4 V or 12 V, which sets the stall torque) |
| Structural toggle | A **layer** toggle (Torque or Bus) replaces the exploded view. When the object is a system, switch which story is drawn over it |
| Instrument | A torque-budget bar chart: what each servo delivers, the torque needed to hold, and the stall line |

Pages in a family link to each other with a quiet pill in the bottom-right corner ("Inside one servo →" / "Six of them in an arm →"), the way the lens page's "also check out" deck does.

Rules that follow from it:

1. **The object is the interface.** You can grab the thing itself (focus ring, servo arm). The HTML controls are a second, accessible route to the same state.
2. **Every number has a place in the scene.** Stats in the corner repeat numbers that are also shown next to the part responsible (the tags).
3. **One sentence of why.** A single live paragraph explains the current state in plain words, with the key numbers in bold. It changes when the situation changes, not every frame.
4. **Depth on demand.** Everything beyond that sentence goes in a modal "How it works" dialog, one short section per concept, with the keyboard shortcuts and the assumptions listed at the end.

---

## 2. Page architecture

### 2.1 One file, no build

- A single `index.html`: inline `<style>`, one inline `<script type="module">`.
- Dependencies come from a CDN through an **import map** (`three` and `three/addons/` from jsDelivr, pinned to one version). There is no bundler and no `package.json`.
- Fonts come from Google Fonts: **Outfit** for UI text, **DM Mono** for numbers and data.
- It works when opened from `file://` because only inline modules and CDN modules are used. A relative `import './x.js'` would break this, so don't add one.

### 2.2 DOM skeleton (fixed order)

```
#app (position:fixed; inset:0)
 ├─ main#world            ← the WebGL canvas (z-index −3), aria-label describes the whole scene
 ├─ .vignette             ← gradient that darkens the left edge and corners so the UI stays readable
 ├─ #tags                 ← 3D-anchored HTML labels (pointer-events:none)
 ├─ aside.side            ← top-left: brand, stat chips, secondary readout, the "why" sentence
 ├─ section.card.ctl      ← top-right: the control card (rows of segmented buttons, slider, tool icons)
 ├─ section.card.*        ← bottom corners: optional secondary instrument (the servo's scope)
 ├─ #ui-hint              ← top-centre pill for transient feedback ("Load: 3 kg")
 └─ dialog#info           ← "How it works"
```

### 2.3 Script sections (fixed order, with banner comments)

Each section starts with a full-width banner comment of the form `// ===… <what, in plain words>`, so the file reads like a table of contents:

1. **imports and tiny helpers** (`$`, `clamp`, `lerp`, `smooth`, `ease`)
2. **the model**: pure functions for the physics (optics or motor), with **no three.js and no DOM**, between `// ---- SIM BEGIN` and `// ---- SIM END` markers so a Node script can extract and test it
3. **renderer**: scene, camera, tone mapping, environment, lights, post-processing
4. **build helpers**: material factories (`M` matte, `MET` metal, `GLOW` unlit >1.0 for bloom), `rbox`, `label()` canvas textures, geometry generators
5. **the room**: floor, grid, props that set the scale
6. **the hero object**: built part by part; each part is a `Group` with a rest position and an explode offset
7. **the explanation layer**: rays, flows, arcs, ghost targets and tags, drawn on top (`depthTest:false`, high `renderOrder`)
8. **state**: controls set a *target*; values ease toward it (`x += (target − x)·(1 − e^(−k·dt))`)
9. **hands**: camera, direct manipulation (raycast onto a plane), the cinematic director
10. **the card**: button wiring, slider, `whyText()`, `syncDom()` (throttled to 10 Hz)
11. **frame**: `resize()` and `tick()`, which runs model → state → scene → overlays → render → DOM
12. **boot**: initial state (don't name the frame function `tick`, which is also the model's usual name for converting an angle to encoder ticks), remove the loading line, start the loop, expose `window.__sim` for tests

---

## 3. Visual tokens

```css
:root{
  font-family:'Outfit',Arial,sans-serif; color:#eef2fa; background:#0b0f1a;
  --glass:linear-gradient(150deg,#141a2ae8,#0d1220e3);  /* card fill */
  --line:#ffffff1c;                                      /* 1px borders */
  --mute:#9ea8c2;                                        /* labels */
  --cyan:#7fe3ff;   /* truth: the measured, the sharp, the target, the signal */
  --amber:#ffb454;  /* effort: error, blur, drive, heat */
  --mono:'DM Mono',ui-monospace,Menlo,monospace;
}
```

### Colour semantics (the most important rule)

- **Background** is navy-black (`#0b0f1a`, scene `#0d121c`), never pure black. Fog is a lighter navy, so distant objects fade into the room instead of going dark.
- **Cyan means "what is true or wanted"**: the plane of focus, the goal, the encoder reading, command packets, feedback.
- **Amber means "what it costs"**: blur discs, position error, PWM duty, current, the drive path.
- White is for values; `--mute` and `#8d97b3` are for labels. Nothing else is saturated. The hero object's own materials (brass, steel, anodised blue) supply the rest of the colour.
- Glow comes from unlit materials with colour × 1.4–3 so that they pass the bloom threshold (~0.9). Nothing else blooms. If the hero object is white or light-coloured, raise the threshold to ~1.05 and tone the material down to about `#bdb7ab`; otherwise lit plastic blooms too.

### Type scale

| Use | Style |
|---|---|
| Title line 1 | Outfit 600, 19px, uppercase, `#dfe6f5` |
| Title line 2 (the noun) | Outfit 800, 37px, uppercase, cyan, line-height .92 |
| Lede | Outfit 400, 12.5px/1.5, `#c7cee0`, max 28em |
| Group label | Outfit 600, 10px, letter-spacing 1.2px, uppercase, `--mute`; the keyboard hint sits right-aligned in DM Mono 10px |
| Stat label / value | DM Mono 500 9px uppercase +.12em / DM Mono 500 15px, `tabular-nums` |
| Why sentence | Outfit 12px/1.5, `#c3cadc`, key numbers `<b>` white, `.c` cyan, `.a` amber |
| Tags | DM Mono 500 10.5px, with an Outfit 600 9.5px uppercase name |
| Dialog | h2 Outfit 700 23px; h3 11px uppercase `#8f98b3`; body 13.5px/1.65 |

### Surfaces

- **Card**: `--glass` fill, 1px `--line` border, radius 16, `backdrop-filter:blur(14px)`, shadow `0 18px 50px -20px #000a`.
- **Chip** (stat, why box, readouts): `#0d1220c4`, radius 10–12, blur 8px.
- **Segmented control**: track `#ffffff0c` radius 11 with padding 2; button height 28, radius 9; the selected button is **inverted** (`#eef4ff` fill, `#101626` text).
- **Icon tool button**: 32×32, radius 10, `#ffffff12`; when on, it turns solid white.
- **Slider**: 2px track `#ffffff1f`; the fill is a cyan gradient with a glow; the knob is a 16px metal-look disc with a 3px cyan halo; tick marks sit under the track at meaningful stops; a **zone** (`.dz`) shows a range (depth of field, or the error between present and goal).

---

## 4. Layout

- **Desktop:** brand and readouts on the left (310px), controls on the right, the object in the middle. `camera.setViewOffset` nudges the render so the object sits clear of the left column.
- **Short screens** (`max-height: 760–820px`): hide the lede and the why box, so the stage keeps its height.
- **Phones** (`max-width: 900px`): the title goes onto one line, the stats become compact, the why box and secondary instruments are hidden, the control card docks to the bottom at full width with buttons that stretch (`flex:1`), the slider takes a full row, and the view offset shifts the object up. Tags stay, but lose their names.
- A `/` key toggles `#app.ui-off`, which hides everything except the world, for clean screenshots.

---

## 5. Interaction model

### 5.1 Three ways to change the one thing

1. **Beats**: a segmented control with 3–4 named presets (Foreground / Middle / Background; Down / Level / Up), bound to keys `1 2 3`.
2. **Continuous**: a slider on the natural scale (logarithmic for focus distance, linear 0–4095 ticks for a servo), with arrow-key steps and `[` `]` nudges.
3. **Direct**: drag the physical part in 3D. Raycast on `pointerdown` in the **capture phase on the canvas's parent** so it runs before OrbitControls; if it hits the part, stop propagation and intersect the ray with the part's plane of motion.

The second and third variables get one segmented control each (aperture; load and tuning), with a single-letter key that cycles them. Each cycle flashes the hint pill.

### 5.2 Targets and easing

Controls never set the displayed value directly. They set a **target**, and the frame loop eases toward it with frame-rate-independent exponential smoothing. The easing is faster while dragging and slower for button jumps. The lens eases `1/distance` rather than distance, which keeps the motion even across the scale. In the servo, the physics sim *is* the easing: the goal jumps and the arm follows at the rate the motor allows.

### 5.3 The cinematic director (guided tour)

- `SHOTS = [{t:[target], yaw, pitch, dist, hold, beats:[[time, fn], …]}, …]`.
- While the tour is on, the camera eases toward each shot and drifts slowly in yaw. **Beats** change the model (focus somewhere, add a load, switch tuning), so the tour demonstrates the ideas instead of only showing the object.
- **Any touch hands over control**: every control handler calls `touched()`, which switches the tour off. The camera icon (`C`) turns it back on. It starts on unless `prefers-reduced-motion` is set.
- `R` flies back to the hero shot and releases the camera.

### 5.4 Keyboard map (keep it consistent across projects)

`1 2 3` beats · `[ ]` nudge · one letter per variable (`F` aperture, `L` load, `T` tuning) · `X` assembled/exploded · `C` cinematic · `R` reset view · `/` hide UI · `?` help · drag to orbit, right-drag to pan, scroll to zoom.

---

## 6. The scene grammar

- **A showroom at dusk**: dark floor, faint grid, a warm key light casting shadows, a cool cyan rim light from behind-left, a hemisphere fill, and a PMREM room environment so metals have something to reflect. ACES filmic tone mapping.
- **Hero object on a stand or bench**, at a scale that props make obvious (a diorama, a USB adapter, a kilogram weight).
- **Cutaway or translucent housing**: the case is ~13% opacity with faint cyan edge lines (`EdgesGeometry`), so the parts inside are always visible.
- **Exploded view** by default: every part is a group with `y0 + ey·smooth(explode)`. Offsets run along one axis and preserve the stacking order. Pick a hero camera angle from which that axis reads as depth *and* separation (about 50° off the axis).
- **Correct kinematics, even when nobody checks.** Gear bodies turn at exact tooth ratios with phased teeth (a tooth meets a gap). Lens elements travel the right way. Weights hang plumb. When the motion is exact, people trust the picture.
- **The explanation layer** is drawn over the object (`depthTest:false`, `renderOrder ≥ 9`):
  - lines at about 30% opacity showing the path;
  - **moving dots** for anything that flows (light rays, bytes, PWM energy, sensor readings), where speed and brightness follow the magnitude and direction follows the sign;
  - **ghosts** for targets (a cyan translucent arm at the goal) and **arcs or zones** for differences (amber from present to goal).
- **Tags**: small HTML labels projected from 3D anchors every frame, each showing the one live number that part is responsible for. They declutter first-come in priority order, and hide when off-screen or behind the camera.

---

## 7. State and data flow

```
input (button / slider / key / drag / tour beat)
   └─► setX(target)                        ← the only writers of state; also send side effects (e.g. a packet)
tick(dt):
   model.step(state, dt)                   ← pure, testable
   applyState(dt)                          ← writes transforms and uniforms; eases structural targets
   applyCam(dt)                            ← tour / fly-to / OrbitControls
   instruments (scope history @30 Hz)
   overlays (flows, dots)  → composer.render()
   updateTags()            → DOM transforms every frame
   syncDom()               → text and classes at 10 Hz; innerHTML only when the string changes
```

- `whyText()` is an ordered list of situations, with the most specific first (stalled → hunting → moving → holding under load → on target → settling). Each returns one sentence with the numbers filled in. Getting this order right is most of the teaching.
- Keep `dt` clamped to `[0, 0.05]`. Negative `dt` does happen on the first frame and breaks anything that takes a modulo.

---

## 8. Writing style

- Plain, concrete, present tense: "The motor spins at **15,382 rpm**; the gears divide that by 345, and the horn turns at 45 rpm."
- Put the effect first and the cause after it. Name the part that is responsible.
- Bold the numbers that change; colour-code them with the cyan/amber semantics.
- Dialog: one `h3` per concept, each 2–4 sentences, and a closing section on the counter-intuitive case ("Upside down?", "Why it sags, why it buzzes"). End with **Keys**, then a small grey footnote stating the reference numbers and what is simplified.
- The title is "The ___ of ___" or "Inside the ___": two lines, with the noun in cyan. The subtitle is one sentence of surprise ("Everyone knows when a photo is blurry. Almost nobody has seen…").

---

## 9. Accessibility and robustness

- `main#world` gets an `aria-label` that describes the whole scene in one sentence.
- Segmented controls are `role="group"` with an `aria-label`. The slider is `role="slider"` with `aria-valuemin/max/now` (and `valuetext` when the units matter) and handles arrow keys.
- The why box has `aria-live="polite"`. Every button gets a `:focus-visible` cyan outline.
- `prefers-reduced-motion` means the tour does not autoplay (the lens also stops its pulse animations).
- If WebGL fails, replace the loading line with a sentence that says what is needed.
- Cap the pixel ratio at 1.5. The lens also adapts to a pixel budget and steps quality down (pixel ratio → reflections → MSAA → AO) when the frame rate stays low.

---

## 10. Testing

- **Model:** the pure physics between `SIM BEGIN/END` markers is extracted and checked by `node check.mjs` (and `node check-so100.mjs` for the arm), which asserts the behaviours the page claims (stock settles cleanly, hot overshoots, soft droops more, overload stalls). Run it after any change to constants.
- **Visual:** use headless Chromium with SwiftShader, e.g. `chromium --headless=new --enable-unsafe-swiftshader --use-angle=swiftshader --window-size=1440,900 --virtual-time-budget=6000 --screenshot=out.png file://…/index.html`. Check desktop and 390×844. On snap Chromium the output must go under `~/snap/chromium/common/`.
- `window.__sim` exposes the setters, so a test page can inject a state before the screenshot.

---

## 11. What the lens page has that this servo page leaves out

This page keeps the design language but drops the lens page's heavier machinery. Add these when a project needs them:

- A custom post chain (MSAA → half-res GTAO → bloom → a gather depth of field whose blur is driven by the model), and a `⌘'` **render tuning panel** that edits every rendering constant live and exports JSON.
- Custom yaw/pitch/dist camera code with WASD and QE, in place of OrbitControls.
- Diegetic screens built into the 3D console (film strip, iris), and a second camera rendering "what the lens sees".
- Analytics, a live viewer count, a share button, and an "also check out" deck of looping thumbnails.

---

## 12. Starting a new "opened up" page: checklist

1. Choose **the one invisible quantity** and decide how it glows (a sheet, an arc, flows, a zone).
2. List **3 beats** for the main variable, plus 1–2 secondary variables with 3–4 settings each.
3. Write the **model** first, as pure functions with a Node check.
4. Block out the hero object as **parts with explode offsets**. Get the kinematics exact.
5. Add the **explanation layer**: paths, dots, ghost and arc, tags.
6. Build the **card** from the tokens above and wire every input through `setX()` plus `touched()`.
7. Write `whyText()` situations from most to least specific, then the dialog.
8. Script **4–5 tour shots** whose beats demonstrate each idea in turn.
9. Screenshot desktop and phone. Check contrast, clutter and the first frame.
