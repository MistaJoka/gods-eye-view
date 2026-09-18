# FOSS / Public-Use 3D Replacement Plan

This directory currently contains third-party GLB assets whose individual licenses are documented in `README.md`. Keep those attributions intact until each asset is actually replaced.

## Goal

Move generic scene objects toward assets we can own procedurally or source under very permissive terms, without breaking runtime behavior or visual parity.

## Priority order

1. Procedural Three.js geometry
2. CC0 asset libraries
3. NASA/public-use mission assets where appropriate
4. Attribution-required assets only when materially better

## Current inventory and replacement intent

| Current file | Current license | Replacement target | Priority |
|---|---|---|---|
| `airplane.glb` | CC BY 4.0 | procedural generic airliner | high |
| `jet.glb` | CC BY 4.0 | procedural business jet | medium |
| `ship.glb` | CC BY 4.0 | procedural cargo vessel | high |
| `bell206.glb` | CC BY 4.0 | procedural generic helicopter | medium |
| `c172.glb` | CC BY 4.0 | procedural light aircraft | medium |
| `citation2.glb` | CC BY 4.0 | procedural business jet variant | low |
| `mq9.glb` | CC BY 4.0 | procedural unmanned-aircraft silhouette | medium |
| `b789.glb` | CC BY 4.0 | procedural widebody variant | low |
| `atr72.glb` | CC BY 4.0 | procedural turboprop variant | low |

## Procedural model contract

Every generated model should follow the same runtime conventions already used by the project:

- glTF/Three.js Y-up world
- predictable forward direction
- centered origin
- dimensions expressed consistently
- geometry suitable for instancing
- minimal material count
- no external texture requirement unless justified

Before replacing a GLB, inspect its current consumer and preserve orientation/scale expectations.

## Recommended primitives

### Aircraft

Use:

- fuselage: tapered cylinder or capsule-like mesh
- wings: thin boxes/custom triangle geometry
- stabilizers: thin boxes
- tail: triangle/custom buffer geometry
- engines: cylinders when needed

Create a small parameter set rather than separate hand-authored models:

```ts
type AircraftProfile = {
  fuselageLength: number;
  fuselageRadius: number;
  wingspan: number;
  wingChord: number;
  tailHeight: number;
  engineCount: number;
};
```

### Ship

Use:

- tapered hull
- deck box
- bridge/superstructure boxes
- optional container blocks

Favor silhouette readability over realism at globe-scale zoom levels.

### Helicopter

Use:

- capsule/box fuselage
- tail boom
- main rotor lines/boxes
- tail rotor

### Tactical objects

Radar rings, towers, cameras, markers and alert objects should be generated entirely in code wherever possible.

## CC0 sources

### Poly Haven

https://polyhaven.com/license

Use primarily for:

- HDRIs
- textures
- environment assets
- occasional generic 3D props

Poly Haven states its downloadable assets are CC0 and may be used commercially without attribution.

### Kenney

https://kenney.nl/support

Use primarily for:

- generic low-poly props
- UI/icon resources
- simple environment/game assets

Kenney states game assets on its asset pages are CC0 and may be used commercially without attribution.

## NASA resources

https://www.nasa.gov/3d-resources/

Use selectively for actual spacecraft/satellite representations where a mission-specific model provides real value.

NASA media/branding rules still apply:

https://www.nasa.gov/nasa-brand-center/images-and-media/

Do not treat NASA logos/identifiers as CC0, do not imply endorsement, and inspect individual resources for any third-party notices.

## Replacement workflow

For each model:

1. Locate every code reference to the GLB.
2. Record current orientation, scale and selection behavior.
3. Build the procedural/CC0 replacement alongside the original.
4. Add a temporary switch for A/B validation if necessary.
5. Compare silhouette and interaction at real operating zoom levels.
6. Check draw calls / geometry cost.
7. Verify picking, tracking and camera-follow behavior.
8. Switch the default to the replacement.
9. Remove the old asset only after parity is confirmed.
10. Update `README.md` attribution inventory.

## Acceptance gate

Do not remove an existing model until the replacement passes all of the following:

- loads without console errors
- renders in correct orientation
- visually communicates the same object class
- selection/picking still works
- tracking/follow-camera still works
- no material performance regression
- source/license is documented
- attribution is removed only when no longer required

## License discipline

`FOSS`, `free`, `public-domain`, and `commercially usable` are not interchangeable terms.

For every new non-procedural asset, capture:

```text
asset:
source:
license:
commercial use:
attribution required:
modifications:
verified date:
```

Unknown license = do not merge the asset.
