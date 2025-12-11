# Guidance for AI Agents

This document provides instructions and best practices for AI agents interacting with the TrakEM2 codebase. Adhering to this guidance will ensure efficient, accurate, and helpful contributions.

## 1. Primary Objective

Your primary objective is to assist developers in understanding, modifying, and extending the TrakEM2 software. A key long-term goal is to facilitate the re-implementation of TrakEM2 features in Python. All your documentation and analysis work should support this objective.

## 2. Getting Started: The Documentation Corpus

Before attempting to answer any questions or modify any code, you **must** familiarize yourself with the existing documentation. The repository contains a set of documents designed specifically for you.

Your first step should always be to consult these files in the following order:

1.  **`docs/ARCHITECTURE.md`**: Start here to get a high-level understanding of the main subsystems (Data Model, Persistence, UI/Display) and how they interact.
2.  **`docs/COMPONENTS.md`**: Once you understand the architecture, consult this document for detailed descriptions of key classes like `Patch`, `AreaList`, `Treeline`, and `Connector`.
3.  **`docs/ALGORITHMS.md`**: For questions related to how specific computations are performed (e.g., non-linear transformations, area interpolation), refer to this file.
4.  **`docs/_analysis/symbol_map.md`**: Use this file as a quick reference or index to discover the purpose of various packages and classes.
5.  **`docs/_analysis/execution_flows.md`**: For questions about process sequences (like project loading), review the flows documented here.

**Do not read the Java source code directly unless these documents fail to provide the necessary information.** The documentation is your primary, curated source of truth.

## 3. How to Answer User Questions

When a user asks a question about the codebase (e.g., "How does segmentation work?", "Where is the code for image alignment?"), follow this process:

1.  **Consult the Docs:** Use the documentation corpus to find the answer. For example, a question about segmentation should lead you to the `AreaList` section in `docs/COMPONENTS.md` and the interpolation section in `docs/ALGORITHMS.md`.
2.  **Synthesize an Answer:** Do not simply copy-paste from the documents. Synthesize a concise, clear answer based on the information you find.
3.  **Provide References:** Cite your sources by referring the user to the relevant document (e.g., "For more details, you can refer to the 'Area Interpolation' section in `docs/ALGORITHMS.md`.").
4.  **Resort to Code Last:** Only if the documentation is insufficient should you read the relevant Java source file. If you do, consider whether the documentation needs to be updated with the information you discovered.

## 4. Approaching a Python Re-implementation Task

When asked to write a Python version of a TrakEM2 feature, you must follow a structured, documentation-driven approach.

1.  **Understand the Feature:** First, use the existing documentation (`ARCHITECTURE.md`, `COMPONENTS.md`, `ALGORITHMS.md`) to fully understand the feature's purpose and implementation in Java.
2.  **Consult the API Map:** Review `docs/API_MAPPING.md`. This document provides the conceptual bridge from Java to a clean Python API. Your implementation should align with the patterns and function signatures proposed in this document.
3.  **Propose a Plan:** Outline the steps you will take. For example:
    *   "First, I will define a Python class for the `AreaList` object."
    *   "Next, I will implement a function to load the area data from the XML structure."
    *   "Then, I will write the `interpolate` method, using the `scikit-image` library for the distance transform."
4.  **Implement Incrementally:** Write the Python code in small, logical chunks.
5.  **Explain Your Code:** Briefly explain the mapping between your Python code and the original Java concepts. For example: "This Python function `arealist.add_area(polygon)` corresponds to the `AreaList.addArea(layer_id, java.awt.geom.Area)` method in Java, but uses a more Pythonic `shapely.Polygon` object as input."
