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

## Contrast Normalization (ini.trakem2.imaging / Loader)
- **Goal**: Harmonize contrast across patches or layers to remove seams and prepare data for stitching/import.
- **Inputs**: Collections of `Patch`/`Layer` objects; user options for saturation percentage, normalization vs. equalization, statistics source (stack, per-image, or reference patch), and whether to reuse existing min/max ranges.
- **Process**:
  - `ContrastEnhancerWrapper` wraps ImageJ's `ContrastEnhancer`, presenting a dialog to collect parameters then either equalize
 histograms (via `EqualizeHistogram` filters) or stretch histograms using stack/reference statistics before regenerating mipmap
s.
  - During grid/text imports (`Loader.insertGrid`, `Loader.importImages`), homogenization can auto-compute common min/max and me
an from central 50% of images (sorted by standard deviation), apply the ranges to all patches, then regenerate mipmaps.
- **Outputs**: Updated per-patch min/max ranges and regenerated mipmaps yielding visually consistent contrast across the select
ed scope.

## Scripting Automation Patterns (ini.trakem2.scripting)
- **Goal**: Provide programmatic access to project operations for batch processing.
- **Inputs**: Scripts invoking project/display APIs; optional headless mode settings.
- **Process**: Convenience wrappers call core APIs for alignment, export, and measurement; rely on loader/persistence for IO.
- **Outputs**: Automated workflows (e.g., batch registration) reproducible via scripts.
