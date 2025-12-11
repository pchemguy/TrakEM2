# TrakEM2 Component Documentation

This document provides detailed descriptions of the key components (classes and interfaces) within the TrakEM2 codebase. It builds upon the high-level overview in `ARCHITECTURE.md` by diving into the specific responsibilities and interactions of major classes.

## 1. `display` Package Components

The `display` package contains all the visual elements that can be placed on a 2D canvas. The primary contract for these elements is the `Displayable` interface.

### 1.1. `Displayable` Interface

This is the most important interface in the `display` package. Any object that can be drawn on the `DisplayCanvas` must implement it. Key methods include:

- `paint(Graphics2D g, ...)`: Draws the object on the given graphics context.
- `getBoundingBox()`: Returns the smallest rectangle enclosing the object.
- `isLinked()`: Checks if the object is linked to other objects.
- `getProject()`: Returns the `Project` to which the object belongs.



### 1.2. `Patch` Class

The `Patch` class is the most fundamental component for displaying image data in TrakEM2. It represents a single 2D image tile that can be transformed and arranged on the canvas.

#### Key Responsibilities:

-   **Image Data Handle:** A `Patch` does not store pixel data directly. Instead, it acts as a lightweight handle that holds metadata about an image, such as its file path, dimensions, and contrast settings (`min`/`max`).
-   **Data Virtualization:** It relies entirely on the `Loader` subsystem to load, cache, and provide pixel data (`ImageProcessor` or `ImagePlus` objects) on demand. This is essential for managing memory when working with very large images.
-   **Transformation:** The position, scale, and shape of a patch are defined by a sophisticated, two-level transformation model:
    1.  **`AffineTransform`:** Every patch has a standard `java.awt.geom.AffineTransform`. This is used for fast, hardware-accelerated translation, scaling, and rotation.
    2.  **`CoordinateTransform`:** A patch can optionally have a complex, non-linear `mpicbg.trakem2.transform.CoordinateTransform`. This is used for advanced deformations like lens correction or elastic alignment. When present, the `paint()` method uses a `TransformMesh` to render the warped image, and the patch's bounding box is adjusted accordingly.
-   **Alpha Masking:** A `Patch` can have an alpha mask, which is a separate 8-bit grayscale image that defines per-pixel transparency. This allows for non-rectangular images and "cutting out" regions.
-   **Painting Pipeline:** The `paint()` and `prePaint()` methods manage rendering.
    -   They calculate the required magnification based on the current view.
    -   They request the appropriately scaled mipmap from the `Loader`.
    -   `prePaint()` can use a lower-resolution mipmap for a fast preview while the correct version is loaded in the background.
...-   **Stack Membership:** Patches can be linked to other patches across different layers to form a virtual stack. The `isStack()` and `getStackPatches()` methods facilitate operations on the entire stack at once.

### 1.3. `AreaList` Class

The `AreaList` is a `ZDisplayable` component used for creating and managing 2D segmentation masks that span multiple layers. It is TrakEM2's primary tool for region-of-interest annotation and label field creation.

#### Key Responsibilities:

-   **Multi-layer Area Storage:** An `AreaList` acts as a container for multiple `java.awt.geom.Area` objects, with each area corresponding to a specific `Layer`. The core data structure is a `HashMap<Long, Area>` that maps layer IDs to their respective `Area` shapes.
-   **Lazy Loading:** To conserve memory, the actual `Area` data is not loaded from the database until it is needed for painting or interaction. A static sentinel value (`UNLOADED`) is used as a placeholder in the `HashMap` until `loadLayer()` is called to fetch the real data.
-   **Coordinate System:** All `Area` objects are stored in the `AreaList`'s local coordinate system. The `AreaList` itself has a global `AffineTransform` that positions it correctly on the canvas. During painting, this transform is applied to the `Area` of the current layer.
-   **User Interaction:** When a user paints with the brush tool, the mouse events are handled by a temporary `AreaWrapper` object. This helper class manages the modification of the `Area` by adding the brush strokes. When the interaction is complete, the `AreaList` may recalculate its bounding box and transform to tightly enclose its contents.
-   **3D Interpolation:** The class provides powerful functionality to interpolate missing segmentations. The `interpolate()` method can generate new `Area` shapes for empty layers that lie between two layers with existing segmentations, effectively filling gaps in 3D.
-   **Measurement:** The `measure()` method calculates volumetric properties by integrating the 2D area measurements across the Z-dimension of the stack, taking layer thickness into account.

### 1.4. `Treeline` Class

The `Treeline` class is a specialized `ZDisplayable` for creating, visualizing, and analyzing tree-like structures, such as neuronal arbors, blood vessels, or other branching filaments. It extends the generic `Tree` class.

#### Key Responsibilities:

-   **Hierarchical Data Model:** A `Treeline` is composed of a hierarchy of `RadiusNode` objects. Each node stores an (x, y, layer) coordinate and a `Float` value representing the radius at that point. This node-based structure with radii is the core of its data model.
-   **2D Rendering:** `Treeline` provides a volumetric appearance in the 2D canvas. Instead of just drawing lines between nodes, the `paintData()` method in `RadiusNode` renders each segment (the connection between a parent and child node) as a filled polygon. This polygon connects the circle defined by the parent's radius to the circle defined by the child's radius, creating the appearance of a tapered tube.
-   **User Interaction:**
    -   Users can create and extend a `Treeline` using the `PEN` tool, clicking to add new nodes.
    -   It has a specialized interaction for editing radii: holding `Shift+Alt` while dragging the mouse or scrolling the mouse wheel adjusts the radius of the nearest node.
-   **3D Mesh Generation:** A key feature is its ability to generate a 3D mesh representation for visualization in viewers like the ImageJ 3D Viewer. The `generateMesh()` method creates a "ball-and-stick" model where each `RadiusNode` is a sphere and each segment is a cylindrical tube.
-   **Quantitative Analysis:** It supports complex measurements along paths within the tree. It can calculate not only the path length but also the volume of the represented tube (by summing the volumes of the truncated cone segments) and provide statistics on the radius along the path.

### 1.5. `Connector` Class

The `Connector` class is a specialized `ZDisplayable` used to define directed, one-to-many relationships between other objects in a project. It is the fundamental component for building connectivity graphs, with its primary use case being the annotation of polyadic synapses (one presynaptic terminal connecting to multiple postsynaptic partners).

#### Key Responsibilities:

-   **Graph Edge Representation:** A `Connector` represents an edge or a set of edges in a graph. It cleverly re-purposes the `Tree` data structure inherited from `Treeline`:
    -   The **`root`** node of the tree represents the **origin** of the connection (e.g., the presynaptic terminal).
    -   The direct **children of the `root`** node represent the **targets** of the connection (e.g., the postsynaptic densities).
-   **Specialized Node and Painting:** It uses a `ConnectorNode`, which, unlike a `Treeline` node, is painted as a simple circle. The UI provides distinct visual cues: the origin node is drawn as a green circle with an "o", and target nodes are white circles with an "x".
-   **Connectivity Analysis:** The core function of the `Connector` is exposed through two key methods:
    -   `getOrigins()`: This method determines which `Displayable` object(s) are located under the area of the origin (`root`) node.
    -   `getTargets()`: This method returns a list of sets, where each set contains the `Displayable` object(s) located under the area of a specific target node.
    -   By using these methods, TrakEM2 can query all connectors to build a complete, project-wide connectivity graph for analysis.
-   **User Interaction:** Users create connectors with the `PEN` tool. The first click sets the origin, and subsequent clicks add targets. The nodes can be dragged to fine-tune their position.


---
*This document is a work in progress.*
