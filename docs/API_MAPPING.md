# TrakEM2 API Mapping (Java to Python)

This document provides a conceptual mapping from the Java implementation of TrakEM2 to a potential high-level, script-friendly Python API. The goal is to abstract away the GUI-centric, event-driven nature of the Java code and present a set of clean, procedural functions that would be intuitive for scripting and for future re-implementation efforts.

## 1. Guiding Principles

-   **Object-Oriented:** The Python API should be object-oriented. A `Project` object would be the main entry point, and it would contain methods to access and manipulate its contents, such as `LayerSet`, `Layer`, and `Displayable` objects.
-   **Data-Centric:** The API should focus on manipulating the data model, not the GUI. Functions should operate directly on `Displayable` objects and their properties.
-   **Pythonic:** Use Pythonic conventions, such as keyword arguments, properties for simple attributes, and standard data structures like lists and dictionaries. Libraries like `numpy` for coordinates and `scikit-image` for image operations should be preferred.

## 2. Core API Concepts

### Project and Layer Management

#### **Function:** Open a Project
-   **Concept:** Load a TrakEM2 project from a file path.
-   **Java Implementation:** `ini.trakem2.Project.openFSProject(String path)`
-   **Python Proposal:**
    ```python
    from trakem2py import Project

    project = Project.open("path/to/project.xml")
    ```

#### **Function:** Get Active Layer
-   **Concept:** Get a reference to the currently active layer in a view. In a headless context, this could be the first layer or a layer specified by the user.
-   **Java Implementation:** `ini.trakem2.display.Display.getFrontLayer()`
-   **Python Proposal:**
    ```python
    # Get the layer at a specific index
    layer = project.layerset.get_layer(0)

    # Or by Z-coordinate
    layer = project.layerset.get_layer_by_z(100.0)
    ```

### Creating Displayable Objects

#### **Function:** Create a Patch (Image)
-   **Concept:** Add a new image to a specific layer at a given position.
-   **Java Implementation:** `ini.trakem2.Project.getLoader().importImage(...)`, followed by `layer.add(patch)`.
-   **Python Proposal:**
    ```python
    import numpy as np

    # Assuming image_data is a numpy array
    image_data = np.zeros((1024, 1024), dtype=np.uint8)

    patch = layer.add_patch(
        image_data,
        x=100,
        y=150,
        title="New Image"
    )
    ```

#### **Function:** Create an AreaList
-   **Concept:** Create a new, empty `AreaList` for segmentation.
-   **Java Implementation:** `new AreaList(project, title, x, y)`, followed by `layer_set.add(arealist)`.
-   **Python Proposal:**
    ```python
    arealist = project.layerset.add_arealist(title="Neuron Segmentation")
    ```

#### **Function:** Add an Area to an AreaList
-   **Concept:** Add a 2D shape to an `AreaList` on a specific layer.
-   **Java Implementation:** `arealist.addArea(layer_id, java.awt.geom.Area)`.
-   **Python Proposal:**
    ```python
        from shapely.geometry import Polygon
    
        # Using a shapely Polygon object
        polygon = Polygon([(0, 0), (10, 0), (10, 10), (0, 10)])
        arealist.add_area(layer, polygon)
        ```
    
    ### Manipulating Displayable Objects
    
    #### **Function:** Affine Transformation
    -   **Concept:** Apply an affine transformation (translation, rotation, scale, shear) to a `Displayable`.
    -   **Java Implementation:** `displayable.setAffineTransform(java.awt.geom.AffineTransform)`. The transform is usually created and manipulated in one of the `...Mode` classes.
    -   **Python Proposal:**
        ```python
        import numpy as np
    
        # Get a patch object (assuming it already exists)
        patch = layer.get_displayable_by_title("My Image")
    
        # Define a 2x3 affine matrix (e.g., for rotation)
        angle = np.deg2rad(15)
        cos_a, sin_a = np.cos(angle), np.sin(angle)
        affine_matrix = np.array([
            [cos_a, -sin_a, 0],
            [sin_a,  cos_a, 0]
        ])
    
        patch.set_transform(affine=affine_matrix)
        ```
    
    #### **Function:** Non-Linear Transformation
    -   **Concept:** Apply a complex, non-linear deformation to a `Patch`.
    -   **Java Implementation:** `patch.setCoordinateTransform(mpicbg.trakem2.transform.CoordinateTransform)`. The transform object is often created by an alignment or registration algorithm.
    -   **Python Proposal:**
        ```python
        from skimage.transform import PiecewiseAffineTransform
    
        # Assuming 'patch' is a trakem2py Patch object
        # and we have source and destination control points
        src_points = [[0, 0], [0, 500], [500, 0]]
        dst_points = [[10, 5], [10, 505], [510, 2]]
    
        # Create a transform object from a library like scikit-image
        skimage_transform = PiecewiseAffineTransform()
        skimage_transform.estimate(src_points, dst_points)
    
        # The Python API would accept this kind of object
        patch.set_transform(nonlinear=skimage_transform)
        ```
    
    ```

---
*This document is a work in progress.*
