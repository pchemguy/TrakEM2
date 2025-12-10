# Dependency Notes

Narrative summary of major dependencies between subsystems.

- `ini.trakem2` (core): orchestrates UI trees (`tree`), display (`display`), and persistence (`persistence`). `Project` wires loaders, templates, and plugins.
- `display` depends on `persistence.Loader` for resolving imagery and metadata, and on `utils` for logging, workers, and user dialogs.
- `display.graphics` provides rendering helpers used exclusively by `display` canvases.
- `tree` components reflect the same domain objects managed by `display`; they query `Loader` for metadata and signal `Project` to update.
- `persistence` relies on `java.sql`/filesystem APIs and is invoked throughout the UI, alignment, and scripting layers for state management.
- `mpicbg.trakem2.align` depends on `display` classes (notably `Patch` and `Layer`) for input data and on `mpicbg` math/transform packages for optimizers.
- `lenscorrection` utilities use `mpicbg` transforms and `imagej` APIs while integrating through plugin entry points in `Project`.
- Scripting packages call into `ini.trakem2` public APIs and indirectly touch alignment/persistence subsystems.
- External library reliance: ImageJ/Fiji for UI + image types, mpicbg for transforms/optimization, Java AWT/Swing, database drivers for DB mode.
