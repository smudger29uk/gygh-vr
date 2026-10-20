# Gasholder No. 172, Great Yarmouth: 3D guide frame model

`gasholder-172-3d.html` is one self-contained file. It loads Three.js r128 and OrbitControls from jsDelivr (pinned) and has no build step. It runs on the desktop as before and, in a browser that supports WebXR, adds a VR mode for the Meta Quest 3.

## Deploying to GitHub Pages (HTTPS) and opening it on the Quest

1. Put `gasholder-172-3d.html` in the root of the repository that serves GitHub Pages. If Pages is set to serve the `/docs` folder, put it in `docs/` instead. Commit and push. Optionally also save a copy as `index.html` so the bare site URL opens it.
2. The URL is `https://<github-user>.github.io/<repo>/gasholder-172-3d.html`. WebXR needs HTTPS, which Pages provides.
3. On the Quest, open the Meta Quest Browser and go to that URL. The model opens as a normal 2D page.
4. Press **Enter VR** in the header. The button only appears when the browser reports that immersive VR is supported. Accept the browser prompt.
5. To leave VR, use **Exit VR** on the Move tab of the wrist panel, or press the Meta button.

Optional address parameters: `?xrscale=0.8` renders at 80 per cent of the headset's recommended resolution (range 0.5 to 1.5, default 1), and `?ffr=1` asks for the strongest fixed foveation where the browser allows it.

## In VR

You start on the south (lorry yard, minus X) side, 27 m from the centre of the holder and about 5 m inside the wall line, facing the holder. Eye height comes from the headset.

| Control | Action |
| --- | --- |
| Right ray (arc) and trigger | Teleport to the green marker on release. Red means blocked. |
| Right ray at a scaffold deck | A straight ray that meets a visible deck teleports you onto that lift. Needs **Decks** on, and only lifts that are erected at the current stage. |
| Right stick forward | Teleport to the current marker |
| Right stick left or right | Snap turn, 30 degrees a step, never continuous |
| X (left) or Move tab | X cycles teleport, smooth walk, fly, then back to teleport. The Move tab has a button for each. |
| Left stick | Smooth mode: walk at about 1.5 m/s, relative to where you face. Fly mode: move level at 3 m/s, with no collision, inside the site boundary. Optional vignette while moving in either. |
| Right stick up or down (fly mode) | Raise or lower the floor at 2 m/s, up to 45 m, with a "Floor height" toast. Only counts when the stick is clearly vertical, so a snap turn never also climbs. |
| Height buttons (Move tab) | Ground, Tier 1, Tier 2, Crown and Above (4 m over the top) switch to fly mode and set the floor to that height where you stand. |
| Right ray at a marker | Hover shows its title. Trigger opens the full text in a reader card with Previous, Next and Close. |
| Left controller panel | Hold your left hand up. Tabs: Layers (structure, access scaffold, decks, site context, markers, notes, condition colours, column numbers), Scaffold (parts and the erection stage slider) and Move. |

The panel and cards use the same checkboxes, sliders and finding data as the desktop page, so nothing is duplicated and the settings carry over when you exit VR. The caveat "Model is indicative, not to scale, not for construction measurement. Briefing and planning use only." is drawn on the panel and on every card.

You cannot teleport or walk to a point that is inside a column, a scaffold standard, a beam or the plan bracing, a hoist mast, an AT-PAC cube, a site building or a lorry, or past the edge of a deck. You also cannot stand where your head would meet the roof trusses.

## Performance (draw calls and triangles)

Measured with three.js `renderer.info`, main pass only, default state (decks off). The updated rows were re-measured after the later model edits (catch fans, demolished holder and redundant ladders removed, lift numbers and bridging pans added).

| Case | Draw calls | Triangles | Shadow pass |
| --- | --- | --- | --- |
| Original page, desktop | 129 | 468,602 | 2048 px map, 72 casters |
| Updated page, desktop | 104 | 465,986 | 2048 px map, 43 casters |
| Updated page, VR, per eye | 132 | 364,738 | none |
| Updated page, VR, per eye, Decks on | 137 | 400,402 | none |

What changed: static site meshes that share a material are batched (29 fewer draws; the triangle count is unchanged, the small rise on desktop is because a batched mesh is no longer culled piecemeal). In VR the shadow pass is skipped, scaffold tubes use open-ended cylinders (about 100,000 fewer triangles per eye), and the render scale defaults to the headset's recommended size. The VR draw calls are higher than desktop because of the 23 in-world markers and the head and hand items. No model geometry, drawing data or `[VERIFY]` note was changed.

## What was and was not tested

Tested in headless Chromium (software rendering) against a purpose-written mock of the WebXR API, not the Immersive Web Emulator and not a polyfill: no console errors on desktop load, with and without `navigator.xr`; desktop render matches the original; the session starts with `local-floor`; start pose; both controllers and the panel appear; teleport, blocked targets, deck teleport, stage clipping, snap turn, smooth walking with collision, vignette, panel buttons, slider, reader card, marker hover and select; exit restores the camera, OrbitControls, shadows and pins; entering and leaving twice.

Not tested, because it needs the headset:

- Frame rate, comfort and whether 72 or 90 fps holds. Try `?xrscale=0.8` first if it does not.
- Where the panel sits on the left controller and whether it faces you when you raise your hand. The offsets are the `PANEL` constants near the top of the VR block (x, y, z, tilt).
- Real Touch button and stick mapping (right stick turn, forward and fly climb, X to cycle mode).
- Text legibility of the panel, cards and markers at real distances.
- Hand tracking without controllers (not designed for).

## Known limitations

- r128 ships `VRButton` and `XRControllerModelFactory` only as ES modules, so the entry button is written inline and the controllers are simple generic shapes, not Touch models. Three.js was not upgraded.
- The pointer, teleport and card controls are on the right hand and the panel is on the left. There is no left-handed swap.
- Shadows are off in VR, so there is less depth cue on the ground.
- Teleport and walking use simplified clearances (a 0.4 m radius round each standard, 0.95 m round each column, a straight beam and bracing line). They are for orientation, not a clearance check.
- Leaving fly mode (by X or the Teleport and Smooth buttons) puts you on the highest visible, unblocked deck at or below your height, else on the ground. With Decks off there are no decks to find, so you always land on the ground, and if the ground spot is inside steel or outside the site you return to the start point.
- Decks are off by default, as on desktop, so lifts are not walkable until you switch them on.
- Dark sky and ground fade are plain colours. Site context is approximate, as on desktop.
- The reader card is placed once in front of you and stays there; walk back to it or open another.
- The DOM panel, pins and toast are not shown in VR (the Quest browser has no DOM overlay), only the in-world equivalents.
