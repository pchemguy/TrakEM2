# TrakEM2 Software Architecture

This document provides a high-level overview of the TrakEM2 software architecture. Its goal is to explain the primary subsystems, their responsibilities, and how they interact.

## 1. Overview

TrakEM2 is a plugin for ImageJ/Fiji designed for managing and processing large collections of 2D images, particularly for 3D reconstruction from serial sections. Its architecture is designed to handle large, multi-resolution datasets efficiently and to provide a flexible framework for visualization, annotation, and analysis.

The architecture can be broken down into three primary subsystems:

1.  **Data Model (The `tree` Subsystem):** Defines the structure and content of a project.
2.  **Persistence (The `persistence` Subsystem):** Handles the storage and retrieval of the data model.
3.  **UI and Display (The `display` and `ini.trakem2` packages):** Manages all visualization and user interaction.

A central, top-level class, `ini.trakem2.Project`, acts as a "god object" that holds references to all major components and orchestrates the interactions between these subsystems.

![High-level Architecture Diagram](https://i.imgur.com/3A5s3sW.png)

*Note: This is a conceptual diagram. The arrow indicates the direction of data flow and dependency.*

## 2. Core Subsystems

### 2.1. Data Model (`tree` package)

The core of any TrakEM2 project is a hierarchical tree structure. This design allows for complex, nested relationships between different data elements.

- **`Thing` Class:** The fundamental building block of the project hierarchy. Nearly every object in a project—from an image patch to a connector to an entire layer—is a subclass or composition of `Thing`.
- **`ProjectThing`:** A wrapper that associates a `Thing` with a `Project` and forms the nodes of the main project data tree. The entire project is represented as a tree of `ProjectThing` objects.
- **Hierarchy:** The project's content is organized as a tree of `Thing`s. This hierarchy is presented to the user through UI components like `ProjectTree`, allowing for direct manipulation of the project structure.
- **Composition:** Complex objects are built by composing simpler `Thing`s. For example, a `LayerSet` contains a list of `Layer`s, and each `Layer` contains a set of `Displayable` objects (like `Patch` and `Polyline`), which are themselves `Thing`s.

### 2.2. Persistence (`persistence` package)

The persistence subsystem is responsible for saving and loading the `Thing` hierarchy to and from a storage medium. It provides a clean abstraction layer that separates the core application from the storage backend.

- **`Loader` Interface:** The central abstraction for data persistence. Concrete implementations like `FSLoader` (File System) and `DBLoader` (Database) handle the specific details of each storage method.
- **File-based Storage (`FSLoader`):** The default backend uses an XML-based format (`.tml` or `.xml`). The structure of the XML file mirrors the `Thing` hierarchy. This loader is managed by `TrakEM2MLParser`, a SAX-based XML parser.
- **Database Storage (`DBLoader`):** TrakEM2 also supports a database backend, where project data is stored in a relational database. `DBObject` is the base class for all objects that can be persisted in the database.
- **Image Caching & Tiling:** This layer is also responsible for generating and managing image mipmaps and tiles (`DownsamplerMipMaps`, `ProjectTiler`). This ensures that large images can be viewed efficiently at any zoom level without consuming excessive memory.

### 2.3. UI and Display Subsystem (`display` and `ini.trakem2` packages)

This is the largest and most complex subsystem, responsible for all visualization and user interaction.

- **`ControlWindow`:** A singleton class that manages the main application `JFrame`. It uses a `JTabbedPane` to host multiple open `Project`s. Its primary role is to assemble the UI, arranging the `ProjectTree`, `LayerTree`, and `TemplateTree` from a `Project` into a set of nested `JSplitPane`s.
- **`Display`:** The controller for a single 2D visualization window (`JFrame`). Each `Display` is associated with a single `Layer` and contains all the UI components for that view, including the canvas, navigator, and side panels. It is the main hub for handling user interaction events for a specific view.
- **`DisplayCanvas`:** The heavyweight AWT component where all rendering occurs. It uses a double-buffering strategy (painting to an off-screen image first) to provide smooth panning and zooming.
- **`Displayable` Interface:** The key contract for any object that can be drawn on the canvas. Any `Thing` that is visible must implement this interface and its `paint()` method. This includes `Patch` (for images), `Polyline`, `AreaList`, `Treeline`, etc.
- **`Mode`-based Interaction:** User interaction on the canvas is handled by a state machine pattern. The `Display` object has a current `Mode` (e.g., `DefaultMode`, `AffineTransformMode`). The `Mode` object receives mouse and keyboard events from the canvas and determines how to interpret them, enabling different behaviors for selection, transformation, drawing, etc.

## 3. Control and Data Flow

### 3.1. Application Startup and Project Loading

1.  A `Project` is created or opened via static methods in the `Project` class (e.g., `Project.openFSProject(...)`).
2.  The appropriate `Loader` (e.g., `FSLoader`) is instantiated and parses the data source (e.g., an XML file).
3.  The `Loader` reconstructs the entire `ProjectThing` hierarchy in memory, including the `Project` object itself, the `LayerSet`, and all `Displayable` objects.
4.  The `Project` object, now fully hydrated, calls `ControlWindow.add(...)`, passing its tree models (`ProjectTree`, `LayerTree`, `TemplateTree`).
5.  The `ControlWindow` creates a new tab and assembles the tree components into the main UI.
6.  If the project file contains information about previously open `Display` windows, they are recreated via the `Display.openLater()` mechanism.

### 3.2. Rendering and User Interaction

1.  A user action (e.g., panning the mouse) or a programmatic call triggers `Display.repaint()`.
2.  The `Display` forwards the call to its `DisplayCanvas`.
3.  The `DisplayCanvas` paints all visible `Displayable` objects onto its off-screen buffer.
4.  The `DisplayCanvas` then paints the buffer to the screen, followed by interactive elements like selection handles.
5.  A mouse click on the canvas is captured by the `DisplayCanvas` and passed to the `mousePressed()` method of the current `Mode` object.
6.  The `Mode` object interprets the event. For example, in `DefaultMode`, a click might change the active `selection`.
7.  The `Mode` calls methods on the `Display` or `Selection` object (e.g., `display.select(...)`).
8.  The `Display.setActive()` method is called, which updates the UI state (highlights the new object, updates side panels) and triggers a repaint.
9.  Right-clicking opens a large, context-sensitive `JPopupMenu` built by `Display.getPopupMenu()`, which is the entry point for most user-facing commands.
10. Selecting a menu item fires an `actionPerformed` event in the `Display` class, which executes the corresponding logic, often in a background thread via the `Bureaucrat` class.
