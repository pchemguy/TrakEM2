# Agent Task Plan: TrakEM2 Documentation and Architecture Reconstruction

This document outlines the phased workflow for analyzing the TrakEM2 codebase, generating comprehensive documentation, and preparing for a future Python re-implementation of key features.

## Phase 0: Initial Repository Reconnaissance

**Status:** Completed

### Summary of Findings:

*   **Project Structure:** The repository is a standard Maven project. Source code is located in `src/main/java`, resources in `src/main/resources`, and tests in `src/test/java`.
*   **Core Technology:** This is a plugin for ImageJ/Fiji, as confirmed by the `pom.xml` which specifies `net.imagej:ij` and a `pom-scijava` parent.
*   **Key Dependencies:** The project relies on a rich ecosystem of scientific imaging and utility libraries, including `imglib2` (for image data structures), `mpicbg` (for transformations and registration), `java3d` (for 3D visualization), and `postgresql` (for database storage).
*   **Documentation Structure:** The `docs/` directory exists but its analytical subdirectories (`_analysis/`, `_generated/`) are empty. The overall documentation structure prescribed by the agent workflow will be created here.
*   **Main Package:** The `pom.xml` properties indicate that the primary package is `ini.trakem2`. This will be the starting point for the analysis.

## Phase 1: Symbol Extraction & Package Map

**Status:** Completed

### Summary of Findings:

*   The entire `ini.trakem2` package has been mapped at a high level in `docs/_analysis/symbol_map.md`.
*   The analysis confirms that the core architecture revolves around a `Thing` based data model (`tree` package), which is stored via the `persistence` package and visualized by the `display` package.
*   Other packages provide supporting functionality like analysis, imaging, and scripting.

## Phase 2: Architecture Reconstruction

**Status:** Pending

### Plan:

1.  **Identify Core Components:** Use the `symbol_map.md` to select the most critical classes for deep-dive analysis. The initial candidates are:
    *   `ini.trakem2.Project` (The central data object)
    *   `ini.trakem2.ControlWindow` (The main UI controller)
    *   `ini.trakem2.display.Display` (The 2D canvas controller)
    *   `ini.trakem2.tree.Thing` (The base data model object)
    *   `ini.trakem2.persistence.Loader` (The data persistence abstraction)

2.  **Analyze Execution Flow:** Read the source code of the core components to understand how they interact. Focus on:
    *   Project loading and initialization.
    *   The rendering pipeline (from `Thing` to pixels on the `DisplayCanvas`).
    *   User interaction and event handling.

3.  **Draft Initial Architecture Document:** Create the `docs/ARCHITECTURE.md` file. The first version will contain:
    *   A high-level overview of the major subsystems (Data Model, UI/Display, Persistence).
    *   A description of the relationships between these subsystems.
    *   A simple diagram (described in text) of the core components and their primary interactions.

4.  **Document Findings:** Store detailed notes, code snippets, and sequence diagrams (in text) in `docs/_analysis/execution_flows.md`.
