# **External Documentation Index**

*This file serves as a structured manifest of all external documentation sources.
Agents MUST read this file and use it as the primary source of truth for external references.*

---

# **0. Instructions for AI Agents**

When performing Stage 1 tasks:

1. **Read this file first** before attempting external documentation integration.
2. For each `SOURCE` block:
    * Load the `url`.
    * Extract relevant content.
    * Write summaries and mappings into:
        * `docs/LEGACY_REFERENCES.md`
        * `docs/_generated/scratch_notes.md`
        * `docs/_analysis/symbol_map.md` (if referencing classes)
    * Update the corresponding `extraction_status` field.
3. Ask the user if a URL is unreachable or incomplete.

---

# **1. External Source Manifest**

Each entry defines a single authoritative source of documentation.

---

### **SOURCE: TRAKEM2_PLUGIN_PAGE**

id: trakem2_plugin_page
type: website
url: [https://imagej.net/plugins/trakem2](https://imagej.net/plugins/trakem2)
priority: high
notes:
* Official ImageJ plugin documentation overview.
* May contain current descriptions of functionality + screenshots.
  extraction_status: done
  summary:
  - Highlights TrakEM2 as an ImageJ plugin for morphological data mining, 3D modeling, stitching/registration, editing, and annotation.
  - Mentions integration with the 3D Viewer and provides JVM tuning guidance (prefers G1GC for large datasets).

---

### **SOURCE: TRAKEM2_GITHUB_WIKI**

id: trakem2_github_wiki
type: github_wiki
url: [https://github.com/trakem2/TrakEM2/wiki](https://github.com/trakem2/TrakEM2/wiki)
priority: high
notes:
* Multiple pages; requires enumerating wiki subpages.
* Contains design discussions, usage notes, legacy documentation.
  extraction_status: blocked (404 during retrieval; needs follow-up)
  summary:
  - Retrieval from raw/wiki endpoints returned 404/HTML placeholders; content not yet integrated.

---

### **SOURCE: TRAKEM2_LEGACY_MANUAL**

id: trakem2_legacy_manual
type: html_manual
url: [https://syn.mrc-lmb.cam.ac.uk/acardona/INI-2008-2011/trakem2_manual.html](https://syn.mrc-lmb.cam.ac.uk/acardona/INI-2008-2011/trakem2_manual.html)
priority: medium
notes:
* Outdated but contains architectural insights and original author intent.
  extraction_status: done (overview)
  summary:
  - Sections cover Introduction, Setup, creating/saving/opening/exporting projects, modeling basics, usage tutorials, plugin extensibility, using TrakEM2 as a framework, and appendix.

---

### **SOURCE: FIJI_JAVADOC_FILTERED**

id: trakem2_fiji_javadoc
type: javadoc
url: [https://javadoc.scijava.org/Fiji](https://javadoc.scijava.org/Fiji)
priority: medium
notes:
* Must filter for classes containing “trakem2” in package or class name.
* Useful for understanding API surfaces and relationships.
  extraction_status: pending
  summary:
  (Agent will populate.)

---

# **2. Additional Potential Sources (User-Extendable)**

Add any new external assets in the same block format:

```
### SOURCE: NAME
id: name
type: <type>
url: <URL>
priority: <low|medium|high>
notes:
-
extraction_status: pending
summary:
```

---

# **3. Agent Action Checklist**

AI agents should execute the following:

* [x] Enumerate all SOURCE blocks
* [x] Fetch/extract each URL (where reachable)
* [x] Identify mentions of TrakEM2 components or algorithms
* [x] Insert structured summaries into LEGACY_REFERENCES.md
* [x] Cross-link architecture and component docs
* [x] Update extraction_status (pending → done)
* [x] Store detailed raw extractions in `docs/_generated/scratch_notes.md`
* [ ] Follow up on blocked/pending sources (GitHub wiki, Fiji javadoc)

---
