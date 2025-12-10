# Components

Brief descriptions of major components and their responsibilities.

## Core
- **Project**: central orchestrator; initializes loaders, trees, displays, and plugin menus. Handles undo/redo and worker scheduling.
- **ControlWindow/New/Open Project commands**: UI entry points for creating or loading filesystem/DB-backed projects.

## Display Layer
- **Display**: main canvas controller; handles event routing, selection, painting, and rendering coordination.
- **Displayable hierarchy**: `LayerSet`, `Layer`, `Patch`, `Connector`, `Treeline`, `AreaList`, `Profile`, `Polyline`, `Ball`, `Pipe`, etc. provide drawable domain entities with geometry and metadata.
- **Graphics helpers**: caching/painters under `display.graphics` to render annotations efficiently.
- **3D hooks**: `display.d3d` integrates with 3D Viewer/Java3D for volumetric visualization.

## Persistence
- **Loader/FSLoader/DBLoader**: abstract storage backends for project state and imagery; manage ID resolution, caching, and XML/DB serialization.
- **DBObject**: base class for persistable domain objects providing ID, loader access, and change tracking.
- **XMLOptions**: serialization preferences for import/export.

## Trees / Templates
- **TemplateThing/TemplateTree**: define metadata schema for project elements.
- **ProjectTree/LayerTree/DNDTree**: Swing trees for browsing and manipulating hierarchy; issue commands back to `Project` and `Display`.

## Alignment and Transforms
- **`mpicbg.trakem2.align`**: alignment workflows (`Align`, `ElasticMontage`, `RegularizedAffineLayerAlignment`), block matching tasks, and tile abstractions.
- **mpicbg.trakem2.transform + bunwarpj.trakem2.transform**: transform classes (affine/similarity/translation and spline-based) with serialization wrappers.

## Imaging and Analysis
- **ini.trakem2.imaging**: filters and image operations supporting patch preparation and visualization.
- **Contrast normalization utilities**: `ContrastEnhancerWrapper` wraps ImageJ's `ContrastEnhancer` to equalize histograms or stretch ranges across patches/layers; `Loader` orchestrates homogenization during grid/text imports and exposes menu actions to apply the wrapper layer-wise or relative to a reference patch.
- **mpi.fruitfly.math/registration**: FFT cross-correlation and numerical utilities backing registration and measurement routines.
- **lenscorrection**: distortion correction workflow and models.

## Automation
- **plugin**: `TPlugIn` interfaces and discovery for menu-driven extensions.
- **scripting**: language-specific adapters exposing project/display APIs for batch operations.

## Utilities
- **Bureaucrat/Worker**: background task orchestration and progress reporting.
- **Utils/IJError**: logging and error handling wrappers for ImageJ context.
- **ProjectToolbar/Search**: UI helpers for toolbar state and searching metadata/displayables.
