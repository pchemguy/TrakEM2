# Symbol Map

Package-level overview and notable classes for TrakEM2. Content generated during Phase 1 to guide deeper analysis.

## Packages

- **bunwarpj.trakem2.transform**: bridge wrappers around Bunwarpj transformations for TrakEM2 alignment pipelines (e.g., B-spline transforms).
- **ini.trakem2**: entry points for project lifecycle (project creation/open, control window wiring) with `Project`, `ControlWindow`, and DB-backed project helpers.
- **ini.trakem2.analysis**: measurement, statistics, and validation utilities (e.g., histogram/intensity analyses) built on project models.
- **ini.trakem2.display**: canvas/viewport widgets (`Display`), layered image structures (`Layer`, `LayerSet`, `Stack`, `Patch`), and drawable domain objects (`Connector`, `Polyline`, `Profile`, `Treeline`, `AreaList`, etc.). Includes editing, labeling, painting, and inspection helpers plus 3D (OpenGL/Java3D) hooks under `display.d3d`.
- **ini.trakem2.display.graphics**: rendering primitives and painters used by `Display` (stroke caching, composite painters, and image rendering helpers).
- **ini.trakem2.display.inspect**: quality control tools for inspecting alignment/segmentation artifacts.
- **ini.trakem2.display.paint**: brush-based painting and annotation utilities integrated with the display stack.
- **ini.trakem2.imaging**: acquisition and processing utilities (filters, channel management) sitting between `display` and external imaging libraries.
- **ini.trakem2.io**: import/export pipelines for project data (XML, images, scripts) plus filesystem utilities.
- **ini.trakem2.parallel**: concurrency helpers for tiling and rendering workloads.
- **ini.trakem2.persistence**: storage abstraction (`Loader`, `DBLoader`, `FSLoader`, `DBObject`, `XMLOptions`) handling database/file-backed assets and caching.
- **ini.trakem2.plugin**: plugin integration via `TPlugIn` and menus populated by `Project`.
- **ini.trakem2.scripting**: Jython/BeanShell/JavaScript entry points for scripting operations on project models.
- **ini.trakem2.tree**: hierarchical metadata structures (`ProjectThing`, `LayerThing`, `TemplateThing`) and Swing tree views (`ProjectTree`, `LayerTree`, `DNDTree`).
- **ini.trakem2.utils**: shared utilities (logging via `Utils`, progress management via `Bureaucrat`, error helpers, toolbar wiring, search, etc.).
- **lenscorrection**: distortion correction models (`PolynomialModel2D`, `NonLinearTransform`) and workflow driver `Distortion_Correction`.
- **mpi.fruitfly.* (general, math, datastructures, registration)**: numerical helpers, custom arrays, image filters, and FFT-based cross-correlation used by alignment/registration steps.
- **mpicbg.trakem2.align (+ concurrent)**: elastic/affine alignment pipelines (`Align`, `ElasticMontage`, `RegularizedAffineLayerAlignment`), block matching callables, and similarity/translation tiles.
- **mpicbg.trakem2.transform**: transformation wrappers aligned with mpicbg stack (affine, similarity, translation) plus serialization support.
- **mpicbg.trakem2.util**: helper utilities for montage/alignment data preparation.
- **org.janelia.intensity**: intensity mapping utilities (e.g., `LinearIntensityMap`) for image normalization.

## Key Objects and Notes

- **Project (ini.trakem2.Project)**: central controller coordinating loaders, trees, displays, plugins, and undo/redo; extends `DBObject` for persistence.
- **Display and Displayable (ini.trakem2.display)**: renderable elements and canvases managing layered imagery and annotations.
- **Layer/LayerSet**: represent Z-stacks and collections of `Displayable` objects; central to registration and visualization flows.
- **Loader/DBLoader/FSLoader**: encapsulate access to project resources stored on disk or in databases; manage caching and link resolution.
- **Templates/Thing trees**: metadata schemas to classify domain objects and drive GUI trees.
- **Alignment classes (mpicbg.trakem2.align)**: implement affine/elastic montage workflows, driven by block matching and tile transforms.
- **Scripting entry points**: allow batch automation, bridging Java core to user-provided scripts.
- **Lens correction**: specialized image distortion correction pipeline built as plugin-level tool.
- **Contrast normalization**: `ContrastEnhancerWrapper` encapsulates contrast stretching/equalization options; `Loader` hosts ho
mogenization hooks during imports and menu actions.

## Uncertainties / TODO

- Need deeper inspection of individual algorithms (e.g., block matching parameters) for Phase 3.
- Wiki content currently unreachable; pending confirmation and incorporation once available.
