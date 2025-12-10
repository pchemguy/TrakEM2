# Architecture

TrakEM2 is an ImageJ plugin organized around a project-centric model. Projects wire together persistence, a layered display stack, metadata trees, and alignment/analysis utilities.

## Subsystems

- **Core Control (ini.trakem2)**: `Project` manages lifecycle, wiring loaders, GUI trees, and plugin registration. Control windows launch dialogs for new/open DB/FS projects.
- **Data Model & Persistence (ini.trakem2.persistence + tree)**: domain objects extend `DBObject` and are identified by IDs. `Loader` abstracts storage, with `FSLoader` for filesystem projects and `DBLoader` for relational backing. Trees (`ProjectTree`, `LayerTree`) mirror the object hierarchy.
- **Display Stack (ini.trakem2.display)**: `LayerSet` holds ordered `Layer` instances containing `Displayable` items like `Patch`, `Connector`, `Treeline`, `AreaList`, etc. `Display` coordinates rendering via `display.graphics` painters and handles interaction/editing.
- **Alignment & Transforms (mpicbg.trakem2.*, bunwarpj.trakem2.transform)**: alignment commands collect patches/tiles, run block matching (concurrent or single-threaded), and optimize affine/elastic transforms which are persisted back to patches. Transform wrappers provide serialization and interaction with mpicbg and Bunwarpj math.
- **Imaging & Filters (ini.trakem2.imaging, mpi.fruitfly.*)**: imaging utilities provide channel operations and filters; math/datastructures packages supply numerical primitives and FFT-based cross-correlation used by alignment.
- **Scripting & Plugins (ini.trakem2.plugin, ini.trakem2.scripting)**: plugin discovery scans `.trakem2` descriptors inside JARs. Scripting layers expose automation hooks for alignment/export via BeanShell/Jython/JS.
- **Utilities (ini.trakem2.utils, parallel)**: logging, worker orchestration (`Bureaucrat`, `Worker`), toolbar management, and concurrency helpers for rendering/alignment workloads.

## Data Flow

1. **Load/Create Project** → initialize `Loader` (FS or DB) and domain templates.
2. **Populate LayerSet** → display `Displayable` objects; user edits and alignment operations modify in-memory state.
3. **Persist State** → `Loader` serializes objects (XML/db) and caches imagery. Trees reflect updated metadata, displays re-render via painters.
4. **Analyze/Align** → alignment modules compute transforms using math utilities; results stored back to `Patch`/`Layer` and re-rendered.
5. **Automate** → scripts or plugins call into core APIs to repeat steps headless or batch.

## External Dependencies

- ImageJ/Fiji runtime for plugin hosting, UI, and image primitives.
- mpicbg and Bunwarpj libraries for transform models and optimization.
- Java AWT/Swing and optional Java3D/OpenGL for 2D/3D visualization.
- JDBC drivers for DB-backed projects.

## Architectural Risks / Open Questions

- Need deeper review of concurrency policies in `align.concurrent` and `parallel` packages.
- GitHub wiki unavailable during this pass; cross-check documentation once accessible.
