# Legacy and External Documentation References

This document contains summaries and insights extracted from external documentation sources, as defined in `docs/EXTERNAL_SOURCES.md`. Its purpose is to consolidate historical context and high-level feature descriptions that may not be immediately apparent from reading the source code.

## 1. TrakEM2 ImageJ.net Plugin Page

-   **Source ID:** `trakem2_plugin_page`
-   **URL:** [https://imagej.net/plugins/trakem2](https://imagej.net/plugins/trakem2)

### Summary

This page provides a high-level overview of TrakEM2, confirming its primary functions:

-   **Morphological Data Mining:** The core purpose is to analyze and model 3D structures from 2D images.
-   **Image Stitching and Registration:** Confirms the presence of SIFT-based registration algorithms.
-   **Segmentation and Modeling:** Highlights segmentation as a key feature, which maps to components like `AreaList` and `Treeline`. It mentions modeling capabilities that produce volumes, surfaces, and length measurements.
-   **3D Visualization:** Explicitly states that 3D visualization is handled by an external dependency, the **3D Viewer** plugin.
-   **Annotation:** Supports text labels and a hierarchical ordering of segmentations, which corresponds to the `ProjectTree` data structure.

### Architectural Insights

-   **Performance:** The page contains significant detail on performance tuning, which implies that the application is designed for very large datasets and that memory management is a primary concern. This reinforces the architectural choice of using lazy loading and mipmaps (as seen in `Loader` and `Patch`).
-   **Garbage Collection:** The recommendation to use the G1GC garbage collector (`-XX:+UseG1GC`) is a strong indicator that the application creates and discards many objects, and that managing GC pauses is critical for a smooth user experience.
-   **Mipmaps:** The detailed advice on mipmap generation strategies confirms their central role in the rendering pipeline.

---
*This document is a work in progress.*
