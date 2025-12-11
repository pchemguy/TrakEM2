# TrakEM2 Execution Flows

This document details the sequence of operations for key processes within the TrakEM2 application.

## 1. Project Loading (File System)

This flow describes what happens when a user opens an existing `.xml` project from the file system, based on the analysis of `ini.trakem2.Project.openFSProject`.

1.  **Entry Point:** `Project.openFSProject(String path, boolean open_displays)` is called.
2.  **Loader Invocation:** A new `FSLoader` instance is created. It is not yet associated with a `Project`.
3.  **Core Parsing (`FSLoader.openFSProject`):** The `FSLoader` is responsible for the heavyweight parsing of the XML file. This is a complex, multi-stage process that ultimately returns an array of root objects:
    *   `TemplateThing` (the `root_tt` for the template tree).
    *   `ProjectThing` (the `root_pt` for the project data tree).
    *   `LayerThing` (the `root_lt` for the layer tree).
    *   A `HashMap` of expanded node states for the UI.
    *   Crucially, during this process, the `Project` object itself is instantiated and associated with the `root_pt`.
4.  **Project Hydration:** Back in `Project.openFSProject`, the returned objects are assigned to the new `Project` instance's fields:
    *   `project.template_tree` is created with `root_tt`.
    *   `project.project_tree` is created with `root_pt`.
    *   `project.layer_tree` is created with `root_lt`.
    *   `project.layer_set` is retrieved from the `root_lt`.
5.  **Registration:** The newly hydrated `Project` is added to the static list of open projects (`al_open_projects`).
6.  **UI Construction:** `ControlWindow.add(...)` is called, which takes the three tree models (`template_tree`, `project_tree`, `layer_tree`) and adds them to the main UI window.
7.  **Display Restoration:** If `open_displays` is true, `Display.openLater()` is called. This process finds any `<t2_display>` tags in the XML and creates `Display` windows for the corresponding layers.
8.  **Autosave Initialization:** `project.restartAutosaving()` is called to start the periodic autosave timer.

### Key Takeaways:

*   The `Project` class orchestrates the loading process, but the `FSLoader` does the actual parsing and object creation.
*   The entire project—data, hierarchy, and UI state—is serialized into a single XML file.
*   The object model is instantiated first, and then the UI components (`JTree`s, `Display`s) are created and attached to the model.

## 2. Rendering Pipeline (2D Canvas)

This flow describes how objects are drawn onto the screen in a `Display` window. The process is orchestrated by the `Display` class and executed by the `DisplayCanvas`.

1.  **Repaint Trigger:** A repaint is triggered by user actions (e.g., panning, zooming, editing an object) or programmatically (e.g., `Display.repaint(...)`).

2.  **`Display.repaint()`:** The static `repaint` methods identify which `Display` instances are affected (e.g., all displays showing a specific `Layer`) and call the instance-level `repaint` method on them.

3.  **`DisplayCanvas.repaint()`:** The call is forwarded to the `repaint()` method of the `DisplayCanvas`. The canvas is a heavyweight AWT component. It uses double-buffering for smooth rendering.

4.  **Off-screen Buffer:** The `DisplayCanvas` maintains an off-screen `Image` buffer. If a repaint is triggered with `update_graphics = true` (which is the case when the underlying image data might have changed, such as when hiding an image), this buffer is recreated.
    *   The canvas iterates through all `Displayable` objects in the current `Layer` that intersect the visible rectangle (`srcRect`).
    *   It calls the `paint(Graphics2D g, Rectangle srcRect, double magnification, boolean active, int channels, Layer active_layer)` method on each `Displayable`.
    *   Images (`Patch` objects) are drawn first to form the background.
    *   Vector data (`Polyline`, `AreaList`, etc.) and other overlays are then drawn on top.

5.  **On-screen Painting:** The `paint(Graphics g)` method of the `DisplayCanvas` is called by the AWT event system.
    *   It draws the pre-rendered off-screen buffer to the screen.
    *   It then draws "active" decorations on top of the buffered image. This includes:
        *   Selection handles and bounding boxes for the `active` `Displayable` and other objects in the `selection`.
        *   Transformation handles if a transform `Mode` is active.
        *   The current ImageJ ROI, if any.
        *   The `GridOverlay`.

### Key Takeaways:

*   **Double Buffering:** The system relies heavily on an off-screen buffer (the "background image") to ensure fast repaints during simple panning and zooming. The expensive operation of drawing all the image patches is only done when necessary.
*   **Layered Painting:** Rendering is a layered process. The base images are drawn first, followed by vector data, and finally by transient on-screen decorations like selection handles.
*   **`Displayable` Interface:** The `paint` method on the `Displayable` interface is the key contract that allows any object to be rendered on the canvas. Each object knows how to draw itself.
*   **`Display` as Conductor:** The `Display` class orchestrates the entire process, managing state (like the active selection) and triggering repaints, while the `DisplayCanvas` does the low-level drawing work.
