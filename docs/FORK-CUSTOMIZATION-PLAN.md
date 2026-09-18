# Fork Customization Plan

This fork keeps the existing God's Eye View interface and interaction model as the baseline while creating a clean path toward an independently branded, reusable command-center platform.

## Objective

Preserve the high-value UI/UX foundation while progressively replacing third-party assets and data dependencies with sources that are easier to reuse, redistribute, and commercialize.

## Non-goals

- Do not rewrite the UI simply to make it different.
- Do not remove working features before replacements exist.
- Do not delete attribution required by existing assets.
- Do not couple the project to a single data provider.

## Target architecture

```text
Existing God's Eye View UI shell
        |
        +-- HUD / panels / telemetry
        +-- globe + camera controls
        +-- layer manager
        +-- selection / tracking interactions
        |
        v
Fork-owned presentation layer
        |
        +-- theme tokens / branding
        +-- reusable panel primitives
        +-- reusable map/globe overlays
        +-- modular data adapters
        +-- asset registry
        |
        v
Replaceable providers
        +-- public/open datasets
        +-- local data
        +-- self-hosted APIs
        +-- optional external APIs
```

## UI strategy

The current UI may remain visually very close to upstream. The fork should first separate **presentation**, **data**, and **assets** so later customization does not require a rewrite.

Priority components to preserve and modularize:

1. Full-screen globe composition
2. Floating HUD panels
3. Layer toggles
4. Target selection / tracking
5. Telemetry presentation
6. Camera modes and cinematic transitions
7. Search / filtering
8. Status indicators and event overlays

## Asset sovereignty strategy

Prefer assets in this order:

1. **Procedural Three.js geometry** — best default for markers, radar, simple aircraft, ships, towers, cameras, rings, paths, and tactical overlays.
2. **CC0 assets** — Poly Haven and Kenney are preferred libraries.
3. **Government/public-use assets** — NASA resources when appropriate, subject to NASA media/branding rules.
4. **Attribution-required assets** — retain only when they add meaningful value and their license is tracked.

### Why procedural-first

Procedural assets give the fork:

- no external asset dependency
- tiny bundle sizes
- consistent visual language
- easy recoloring and animation
- predictable orientation/scale
- fewer attribution requirements
- easier LOD/instancing optimization

## Verified external asset sources

### Poly Haven

- Assets: HDRIs, textures, 3D models
- License: CC0
- Commercial use: allowed
- Attribution: not required
- Source: https://polyhaven.com/license

### Kenney

- Assets: 2D/3D game assets, UI, icons, effects
- License: CC0 for game assets on asset pages
- Commercial use: allowed
- Attribution: not required
- Source: https://kenney.nl/support

### NASA 3D Resources

- Assets: spacecraft, satellites, mission hardware, textures and related models
- Usage: free to download/use, but NASA media and branding guidelines apply
- Avoid implying NASA endorsement; inspect individual assets for third-party material
- Sources:
  - https://www.nasa.gov/3d-resources/
  - https://www.nasa.gov/nasa-brand-center/images-and-media/

## Replacement matrix

| Current role | Preferred replacement |
|---|---|
| Generic aircraft | procedural Three.js low-poly aircraft |
| Generic ship | procedural Three.js hull/superstructure |
| Cameras | procedural boxes/cylinders or Kenney CC0 |
| Radar/towers | procedural Three.js |
| Tactical markers | custom SVG / Three.js sprites |
| Environment textures/HDRIs | Poly Haven CC0 |
| Generic props | Kenney CC0 |
| Satellites / spacecraft | procedural first; NASA resources where useful |

## Data-provider rule

Every external data source should be wrapped behind a small adapter with a stable internal shape.

```ts
interface ObservationProvider<T> {
  id: string;
  fetch(bounds: GeoBounds, signal?: AbortSignal): Promise<T[]>;
  normalize(raw: unknown): T[];
}
```

UI components should consume normalized domain objects rather than provider-specific payloads.

This makes it possible to swap ADS-B, maritime, weather, camera, satellite, infrastructure, or local-data providers without rebuilding the UI.

## Migration phases

### Phase 0 — preserve working baseline

- keep upstream behavior intact
- retain required MIT and asset attribution
- document all fork-specific decisions

### Phase 1 — asset inventory

- enumerate every model, texture, icon, dataset and external API
- record source, license, runtime usage and replacement priority

### Phase 2 — procedural tactical primitives

Build fork-owned primitives for:

- aircraft
- ship
- camera
- tower
- target marker
- radar ring
- waypoint
- route/path
- uncertainty radius
- alert pulse

### Phase 3 — replace third-party models

Replace attribution-heavy models only after visual and runtime parity is demonstrated.

Acceptance criteria per replacement:

- same semantic role
- correct orientation
- similar or lower draw cost
- no regression in picking/tracking
- documented source/license
- visually coherent with the HUD

### Phase 4 — fork branding/theme layer

Centralize:

- colors
- typography
- borders
- glow intensity
- spacing
- animation timing
- panel opacity
- status colors

Do not scatter fork-specific styling through unrelated components.

### Phase 5 — reusable command-center shell

Extract the UI foundation so the same frontend can support multiple products or deployments, such as:

- regional public-data watch
- infrastructure monitor
- network/tailnet visualization
- logistics tracker
- federal/public spending map
- incident command center

## Definition of done for this customization track

The fork is considered independent enough for long-term development when:

- the original MIT notice remains intact
- every bundled third-party asset has an explicit license record
- generic visual objects can be rendered using fork-owned procedural primitives
- data providers are replaceable without UI rewrites
- branding/theme values are centralized
- the existing globe/HUD experience remains intact
- no source is described as public-domain/CC0 without verification

## Rule for future AI-assisted development

Before adding an external asset or dataset, record:

```text
name
source URL
license / terms
commercial-use status
attribution requirement
local file(s)
runtime consumer
replacement difficulty
```

If the license cannot be verified, treat the asset as **unapproved** until resolved.
