# Legacy References

External documentation summaries and linkage to code areas.

## ImageJ Plugin Page (imagej.net/plugins/trakem2)
- **Summary**: Describes TrakEM2 as an ImageJ plugin for morphological data mining, 3D modeling, stitching/registration, editing, and annotation. Notes integration with the 3D Viewer and provides JVM tuning guidance (switch to G1GC) for large-scale datasets.
- **Relevant Areas**: Core project workflow (`Project`, `Display`), alignment modules, 3D visualization hooks.
- **Status**: extracted; see `docs/_generated/scratch_notes.md` for notes.

## TrakEM2 User Manual (0.9a)
- **Summary**: HTML manual with sections on Introduction, Setup, creating/saving/opening/exporting projects, modeling basics, usage tutorials, plugin extensibility, using TrakEM2 as a framework, and appendix material.
- **Relevant Areas**: project lifecycle commands, modeling workflows, plugin API guidance.
- **Status**: extracted overview; deeper section-to-code mapping pending.

## GitHub Wiki
- **Summary**: Attempted to fetch wiki content via raw GitHub URLs but access returned 404/HTML placeholders during this pass.
- **Action**: needs follow-up to confirm availability or alternative mirror; update once accessible.

## Fiji Javadoc
- **Summary**: Not yet extracted in this pass. Use javadoc.scijava.org/Fiji filtered for `trakem2` packages for API cross-checks.
- **Action**: pending extraction and cross-linking to component docs.
