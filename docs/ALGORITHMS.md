# Algorithms

High-level descriptions of notable algorithms; details to be refined in future passes.

## Elastic/Affine Alignment (mpicbg.trakem2.align)
- **Goal**: Register overlapping image patches/layers using block matching and optimize affine or elastic transforms.
- **Inputs**: `Patch` collections from `LayerSet`, alignment parameters (block size, search radius, regularization weights).
- **Process**: Block matching (`BlockMatchPairCallable`) extracts correspondences; similarity/translation tiles feed solvers such as `RegularizedAffineLayerAlignment` or `ElasticMontage` to compute transforms.
- **Outputs**: Updated transform parameters stored in patch metadata; visualized in `Display` and persisted via `Loader`.
- **Notes**: Concurrent implementations available; further parameter audit needed.

## Lens Distortion Correction (lenscorrection)
- **Goal**: Correct microscope lens distortion using polynomial/BSpline models.
- **Inputs**: Calibration images or control points; model order; optimization settings.
- **Process**: Estimate distortion via `PolynomialModel2D`/`NonLinearTransform`; `Distortion_Correction` orchestrates fitting and applies transforms to patches.
- **Outputs**: Updated transform models applied to imagery; optional export of correction parameters.

## Intensity Mapping (org.janelia.intensity)
- **Goal**: Normalize image intensities between sections.
- **Inputs**: Image stacks; desired mapping curve parameters.
- **Process**: Compute mapping (e.g., `LinearIntensityMap`) and apply to pixel data before alignment/visualization.
- **Outputs**: Adjusted images improving stitching and visual uniformity.

## Scripting Automation Patterns (ini.trakem2.scripting)
- **Goal**: Provide programmatic access to project operations for batch processing.
- **Inputs**: Scripts invoking project/display APIs; optional headless mode settings.
- **Process**: Convenience wrappers call core APIs for alignment, export, and measurement; rely on loader/persistence for IO.
- **Outputs**: Automated workflows (e.g., batch registration) reproducible via scripts.
