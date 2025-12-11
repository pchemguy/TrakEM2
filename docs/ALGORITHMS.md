# TrakEM2 Algorithm Documentation

This document describes the key algorithms and computational strategies used within TrakEM2.

## 1. 2D Transformation Model

TrakEM2 uses a powerful, two-level model to handle the spatial transformation of `Patch` (image) objects. This allows for both fast, simple transformations and complex, non-linear deformations.

### 1.1. Level 1: `java.awt.geom.AffineTransform`

Every `Displayable` object, including `Patch`, has a standard Java `AffineTransform`. This transform is applied directly by the Java 2D rendering engine (`Graphics2D`).

-   **Purpose:** Handles standard transformations like translation, scaling, and rotation.
-   **Performance:** These operations are generally fast and can be hardware-accelerated.
-   **Application:** The `at` field in the `Displayable` class holds this transform. It is applied during the `paint()` call just before drawing the image to the canvas.

### 1.2. Level 2: `mpicbg.trakem2.transform.CoordinateTransform`

For more complex deformations, a `Patch` can have an optional `CoordinateTransform`. This object comes from the `mpicbg` library and can represent a wide variety of non-linear transformations.

-   **Purpose:** Handles complex, non-rigid deformations such as lens distortion correction or elastic alignment from tools like bUnwarpJ.
-   **Data Storage:** The transform itself is not stored in the main XML project file. Instead, it is saved in a separate external file (e.g., in the `trakem2.cts/` directory), and the `Patch` only stores a reference ID (`ct_id`).
-   **Application:** When a `CoordinateTransform` is present, it is not applied directly during every repaint, as this would be too slow. Instead, a `TransformMesh` is used.

### 1.3. `TransformMesh` for Non-Linear Rendering

When a `Patch` with a `CoordinateTransform` needs to be rendered, the following process occurs:

1.  A `TransformMesh` is created from the `CoordinateTransform`. The mesh is a grid of points that covers the original image.
2.  The `CoordinateTransform` is applied to every vertex in the mesh to find its new, deformed position.
3.  The `DisplayCanvas` then renders the image by texturing it onto this deformed mesh, interpolating the pixels between the vertices.
4.  The bounding box of the deformed `TransformMesh` is calculated, and the `Patch`'s standard `AffineTransform` is updated to translate the patch so its top-left corner aligns with the new bounding box. This ensures the rest of the TrakEM2 system can correctly handle the position of the deformed patch.

...
This two-level system allows TrakEM2 to combine the flexibility of non-linear transformations with the performance of standard affine transformations. The expensive mesh deformation is only calculated when needed, and the result is "baked" into the patch's affine transform and bounding box for fast rendering in subsequent frames.

## 2. Area Interpolation

`AreaList` objects are used to create 2D segmentations on different layers. The area interpolation algorithm provides a powerful way to automatically generate segmentations for empty layers that lie between two segmented layers, greatly speeding up the process of 3D reconstruction.

The main entry point is the `interpolate(Layer first, Layer last, ...)` method in the `AreaList` class. It identifies all empty layers between `first` and `last` and generates new `Area` shapes for them.

The algorithm employs two different strategies depending on the complexity of the shapes being interpolated.

### 2.1. Simple Case: Singular Interpolation

If the `Area` on the starting layer and the `Area` on the ending layer are both "singular" (meaning they consist of a single, non-branching shape), a simple interpolation method is used.

-   **Algorithm:** The contours of the start and end areas are treated as polygons. The algorithm generates intermediate polygons by linearly interpolating the coordinates of the vertices of the start polygon towards the corresponding vertices of the end polygon.
-   **Use Case:** This method is fast and effective for simple, continuous objects that change their shape smoothly from one layer to the next without splitting or merging.
-   **Implementation:** This is handled by the `AreaUtils.singularInterpolation(...)` method.

### 2.2. Complex Case: Distance Map Interpolation

If either the start or end `Area` is complex (i.e., it contains multiple disconnected shapes, or holes), the simple vertex interpolation will fail. In this case, a more robust method based on distance maps is used.

-   **Algorithm:**
    1.  A 2D **distance map** (also known as a distance transform) is generated for both the start and end `Area`. In a distance map, the value of each pixel represents its distance to the nearest edge of the area.
    2.  Intermediate distance maps are created for the in-between layers by linearly interpolating the distance values of the start and end maps.
    3.  A new binary image is created from each intermediate distance map by thresholding it at zero. The pixels with positive distance values are inside the new area, and those with negative values are outside.
    4.  An `Area` shape is generated from this binary image.
-   **Use Case:** This method can handle complex topological changes like an object splitting into two, or two objects merging into one. The distance map provides a robust representation of the shape that can be smoothly interpolated.
-   **Implementation:** This is handled by `AreaUtils.manyToManyInterpolation(...)`, which in turn uses classes like `BinaryInterpolation2D`. The user can also force this method to be used for all interpolations via a project property.
