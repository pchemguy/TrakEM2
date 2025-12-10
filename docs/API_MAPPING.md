# API Mapping (Java → Conceptual → Python)

Guidance for translating TrakEM2 APIs into Pythonic equivalents.

## Core Project APIs
- **Java**: `Project`, `Loader`, `LayerSet`, `Layer`, `Patch`, `Display`.
- **Concepts**: project handle managing persistence; hierarchical scene graph of layers/displayables; render/update loop; plugin menu actions.
- **Python sketch**: `Project` class managing storage adapters (filesystem/DB wrappers), `LayerSet`/`Layer` dataclasses, `Display` controller using e.g., PyQt/napari; plugins registered via entry-point hooks.

## Persistence
- **Java**: `Loader`, `DBLoader`, `FSLoader`, `DBObject`, `XMLOptions`.
- **Concepts**: abstract repository storing images/metadata; ID resolution; caching; XML serialization.
- **Python sketch**: repository interface with backends (local folder, SQL database); use `dataclasses` + `pydantic` for schema; serialization via XML/JSON; caching via `lru_cache` or diskcache.

## Alignment
- **Java**: `Align`, `ElasticMontage`, `RegularizedAffineLayerAlignment`, `BlockMatchPairCallable`, mpicbg transform classes.
- **Concepts**: block matching to produce correspondences → optimize transforms → apply to patches.
- **Python sketch**: NumPy/SciPy for optimization; scikit-image for transforms; concurrent.futures for block matching; data classes for tiles/correspondence sets.

## Imaging / Filters
- **Java**: `ini.trakem2.imaging.*`, `mpi.fruitfly.math/registration`, `org.janelia.intensity.LinearIntensityMap`.
- **Concepts**: image filters, FFT-based cross-correlation, intensity normalization.
- **Python sketch**: use NumPy/SciPy/scikit-image; wrap FFT via `numpy.fft` or `scipy.signal`; map intensity curves via vectorized operations.

## Scripting / Plugins
- **Java**: `ini.trakem2.plugin.TPlugIn`, scripting adapters for BeanShell/Jython/JS.
- **Concepts**: extend menus with plugin descriptors; expose automation through scripting languages.
- **Python sketch**: entry-point registration (e.g., setuptools entry_points) for plugins; provide Python API layer for batch scripts; optional CLI bindings.

## UI and Visualization
- **Java**: Swing-based `Display`, `Displayable` painters, optional 3D viewer hooks.
- **Concepts**: interactive canvas with layered rendering and tool-driven editing.
- **Python sketch**: adopt napari or PyQtGraph for 2D/3D visualization; map `Displayable` to layer types; tools implemented via widgets and callbacks.

## Notes
- Maintain data model parity (IDs, transforms, metadata) to ensure faithful translation.
- Account for threading differences—Python GIL may require multiprocessing for heavy block matching.
- ImageJ-specific classes (e.g., `ij.ImagePlus`) should map to NumPy arrays or dask-backed images.
