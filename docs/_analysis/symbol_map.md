# TrakEM2 Symbol Map

This document provides a map of the key packages, classes, and interfaces in the TrakEM2 codebase. Its purpose is to aid in understanding the software's architecture and to guide future development and re-implementation efforts.

## High-Level Package Structure (`ini.trakem2`)

The core logic of TrakEM2 is organized under the `ini.trakem2` package. The sub-packages represent distinct functional subsystems.

| Package       | Inferred Responsibility                                      |
|---------------|--------------------------------------------------------------|
| `analysis`    | Tools for quantitative analysis of project data.             |
| `display`     | Core UI, rendering, and visualization components.            |
| `imaging`     | Image processing and manipulation utilities.                 |
| `io`          | Data import/export and serialization.                        |
| `parallel`    | Concurrency and parallel processing management.              |
| `persistence` | Handles saving/loading of projects to/from different backends. |
| `plugin`      | Integration points with the ImageJ/Fiji plugin ecosystem.    |
| `scripting`   | Support for scripting languages like Beanshell, Jython, etc. |
| `tree`        | Data structures for representing hierarchical project elements.|
| `utils`       | General-purpose utility classes and helper functions.        |

## Root Package: `ini.trakem2`

This package contains central classes that manage the overall application state, projects, and user interaction.

| Class              | Inferred Responsibility                                       |
|--------------------|---------------------------------------------------------------|
| `Project`          | Represents a single TrakEM2 project, likely holding all data. |
| `ControlWindow`    | The main GUI window for managing a project and its layers.    |
| `New_Project`      | Command/Action to create a new file-based project.            |
| `Open_Project`     | Command/Action to open an existing file-based project.        |
| `New_DB_Project`   | Command/Action to create a new database-backed project.       |
| `Open_DB_Project`  | Command/Action to open an existing database-backed project.   |

---
*This document is a work in progress. It will be expanded as analysis continues.*

## Package: `ini.trakem2.display`

This is the largest and most complex package, responsible for all 2D visualization, user interaction, and canvas management. It defines the core interfaces for anything that can be displayed and manipulated.

| Class / Interface | Inferred Responsibility | Notes |
|---|---|---|
| `Display` | The main controller for a 2D canvas view. Manages layers, rendering, and input events. | Appears to be a central hub for visualization. |
| `Displayable` | An interface for any object that can be drawn on the canvas (e.g., images, shapes). | The primary abstraction for all visual elements. |
| `Layer` | A container for `Displayable` objects at a specific Z-coordinate. | Represents a single slice or plane in the project. |
| `LayerSet` | Manages a collection of `Layer` objects. | Represents the entire volume or stack of layers. |
| `DisplayCanvas` | The low-level GUI component (likely extends `java.awt.Canvas`) where rendering occurs. | Handles painting and mouse/keyboard events. |
| `Mode` | An interface for different user interaction modes (e.g., selection, transform, drawing). | Implemented by classes like `DefaultMode`, `AffineTransformMode`, etc. |
| `Patch` | A `Displayable` that represents an image tile. | One of the most common and fundamental object types. |
| `Polyline`, `AreaList`, `Pipe`, `Treeline` | `Displayable` classes representing vector shapes (lines, areas, tubes). | Used for segmentation, tracing, and annotation. |
| `Connector`, `Node`, `Tree` | Classes for representing and visualizing graph-like or tree-like relationships. | Key for connectomics and lineage tracing. |
| `ZDisplayable`| An interface for objects that exist across multiple Z-slices. | e.g., `AreaTree`, `Tree`, `Connector`. |
| `Channel` | Manages properties for a single color channel. | |
| `Selection` | Manages the set of currently selected `Displayable` objects. | |

## Package: `ini.trakem2.persistence`

This package handles the storage and retrieval of all project data. It provides an abstraction layer that separates the core application from the underlying storage backend (file system or database). It also manages image tiling and mipmap generation.

| Class / Interface | Inferred Responsibility | Notes |
|---|---|---|
| `Loader` | The central abstract class or interface for loading and saving project data. | Defines the API for data persistence. |
| `FSLoader` | A `Loader` implementation for projects stored on the local file system. | Manages the directory structure and XML project file. |
| `DBLoader` | A `Loader` implementation for projects stored in a relational database. | Interacts with the database via JDBC. Uses `DBObject`. |
| `DBObject` | Base class/interface for all objects that can be stored in the database. | Provides methods for serialization/deserialization. |
| `TMLHandler` | A SAX-based XML handler for parsing the `.tml` or `.xml` project files. | TML likely stands for "TrakEM2 Markup Language". |
| `Cache` | Manages an in-memory cache of objects (e.g., images, project elements) to improve performance. | Crucial for handling large datasets. |
| `ProjectTiler` | Creates and manages tiled representations of large images. | |
| `DownsamplerMipMaps` | Generates mipmaps (image pyramids) for efficient multi-resolution viewing. | |
| `XMLOptions`| Helper class for reading and writing configuration and properties as XML. | |

## Package: `ini.trakem2.tree`

This package defines the core data model of a TrakEM2 project, which is structured as a hierarchical tree of generic objects. It also provides the UI components for displaying and manipulating this tree.

| Class / Interface | Inferred Responsibility | Notes |
|---|---|---|
| `Thing` | The base class for any object in the project tree hierarchy. | A fundamental concept. `Displayable` objects are likely `Thing`s. |
| `ProjectThing` | A wrapper that associates a `Thing` with a `Project`. | The root of the hierarchy. |
| `LayerThing` | A wrapper that associates a `Thing` with a specific `Layer`. | |
| `ProjectTree` | A `javax.swing.JTree` component for visualizing the entire project hierarchy. | The main navigation and organization view for the user. |
| `LayerTree` | A `JTree` component for visualizing the hierarchy of a single layer. | |
| `DNDTree` | A base class for trees that supports Drag and Drop operations. | `ProjectTree` and `LayerTree` likely extend this. |
| `TrakEM2MLParser` | Parses the `TrakEM2ML` (XML) data format to reconstruct the project tree. | Works in conjunction with the `persistence` package. |

## Package: `ini.trakem2.analysis`

This package provides tools for performing quantitative analysis on the project data, with a specific focus on graph-based representations of the data.

| Class / Interface | Inferred Responsibility | Notes |
|---|---|---|
| `Graph` | A data structure representing a mathematical graph. | Composed of `Vertex` instances. |
| `Vertex` | Represents a node in a `Graph`. | |
| `Centrality` | Computes network centrality measures on a `Graph`. | Useful for finding important nodes in connectomics data. |
... 
| `Compare` | Provides tools to compare different project elements or analysis results. | |

## Other Packages

Below is a summary of the remaining packages in `ini.trakem2`.

### Package: `ini.trakem2.imaging`
**Responsibility:** Image processing, stitching, and segmentation algorithms.
- **Key Classes:** `StitchingTEM`, `PhaseCorrelationCalculator`, `Segmentation`, `Blending`.

### Package: `ini.trakem2.io`
**Responsibility:** Handlers for importing and exporting specific data formats.
- **Key Classes:** `AmiraImporter`, `NeuroML`, `ImageSaver`, `RagMipMaps`.

### Package: `ini.trakem2.parallel`
**Responsibility:** A small framework for managing concurrency and background tasks.
- **Key Classes:** `ExecutorProvider`, `TaskFactory`, `ParallelMapping`.

### Package: `ini.trakem2.plugin`
**Responsibility:** The primary entry point for ImageJ/Fiji to interact with TrakEM2.
- **Key Classes:** `TPlugIn`.

### Package: `ini.trakem2.scripting`
**Responsibility:** Exposes scripting functionality.
- **Key Classes:** `PatchScript`.

### Package: `ini.trakem2.utils`
**Responsibility:** A broad collection of utility and helper classes.
- **Key Classes:** `Utils`, `Bureaucrat` (handles tasks), `Montage`, `Render`, `ProjectToolbar`, `IJError`.


