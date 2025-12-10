# Design Notes

Guiding principles inferred from the codebase and legacy docs.

- **Project-centric workflow**: everything hangs off a `Project` that owns loaders, trees, displays, and plugins.
- **Persistence-first domain model**: domain objects extend `DBObject` with IDs and loader references to keep UI and storage consistent.
- **Layered visualization**: `LayerSet`/`Layer` manage Z-ordering while `Displayable` subclasses provide semantic annotations and imagery.
- **Extensibility**: plugin discovery via `.trakem2` descriptors and scripting adapters enable user extensions without modifying core code.
- **Performance awareness**: alignment pipelines use concurrent block matching; rendering uses painter caches; external guidance recommends tuned JVM flags for large datasets.
- **Separation of storage backends**: filesystem vs database loaders allow different deployment contexts while sharing the same domain model.
