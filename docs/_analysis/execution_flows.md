# Execution Flows

Illustrative control flows for key user actions in TrakEM2.

## Project Creation/Open
1. User triggers `New_Project`/`Open_Project`/`Open_DB_Project` which delegate to `ini.trakem2.Project` constructors.
2. `Project` initializes loaders (`FSLoader` or `DBLoader`) and GUI scaffolding (trees via `ProjectTree`, `LayerTree`; `ControlWindow`).
3. Templates are loaded (`TemplateTree`, `TemplateThing`) to define domain object schemas.
4. Initial `LayerSet` is created and shown via `Display`, wiring `Displayable` subclasses to canvas tools and toolbar state.
5. Plugins are discovered (JAR scan in `Project` static initializer) and menu actions registered.

## Display Interaction
1. `Display` manages the visible `LayerSet` and delegates drawing to `display.graphics` painters.
2. User actions (selection, paint, transform) manipulate `Displayable` subclasses; updates propagate to `Layer`/`LayerSet` and persist through `Loader`.
3. Z-navigation updates the active `Layer`; `Display` coordinates double-buffered rendering and 3D viewer synchronization when enabled.

## Alignment / Montage (mpicbg.trakem2.align)
1. Alignment commands (e.g., `ElasticMontage`, `Align`) collect `Patch` or tile descriptors from the active `LayerSet`.
2. Block matching via `BlockMatchPairCallable` computes correspondences, feeding `SimilarityTile2D`/`TranslationTile2D` structures.
3. Optimizers apply affine/elastic models (`RegularizedAffineLayerAlignment`) to generate transforms stored back into patch metadata.
4. Results are persisted through `Loader` and reflected in display/rendering; optional inspection via `display.inspect` tools.

## Scripting Automation
1. Script entry points in `ini.trakem2.scripting` expose convenience wrappers around `Project`, `LayerSet`, and alignment classes.
2. Scripts execute operations headless or semi-interactive, reusing loaders and persistence; can invoke alignment, export, or measurement utilities.

## Persistence
1. Domain objects extend `DBObject` and rely on `Loader` to resolve IDs and serialized XML state.
2. `FSLoader` handles filesystem-based projects; `DBLoader` connects to relational storage. Both manage caches and background IO via `Bureaucrat` workers.
