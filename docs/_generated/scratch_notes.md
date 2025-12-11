# Scratch Notes - Raw External Content

This file contains raw, unprocessed text fetched from external documentation sources. It serves as a cache and a source for more detailed analysis.

---

## SOURCE: TRAKEM2_PLUGIN_PAGE

**URL:** [https://imagej.net/plugins/trakem2](https://imagej.net/plugins/trakem2)

**Content:**

TrakEM2 is an ImageJ plugin for morphological data mining, three-dimensional modeling and image stitching, registration, editing and annotation.

- Author: Albert Cardona, Stephan Saalfeld and others
- License: GPL-3
- Sources: https://github.com/trakem2/TrakEM2
- Javadocs: [https://javadoc.scijava.org/Fiji/](https://javadoc.scijava.org/Fiji/)
- Bugs: https://github.com/trakem2/TrakEM2/issues

**Functionality**

TrakEM2 is a plugin for sequential image alignment, 3D segmentation and annotation. Some of its features are:
- Segmentation of 3D image stacks into objects, which can be semantically ordered in a tree. Example: mitochondria inside a neuron, the neuron inside a ganglion, etc.
- Measurements of volumes, surfaces, and lengths.
- Image registration using SIFT and global optimization of sets of images.
- 3D visualization via the [3D Viewer](https://imagej.net/plugins/3d-viewer) plugin.
- Annotation of images with floating text labels.

**Status**
- TrakEM2 is a stable plugin. No new features are under development.
- The API of TrakEM2 is stable: methods will not be removed or their signature changed. New methods and classes may be added.
- Partial support: we will answer questions in the community channels and issue reports, but bug fixes may not happen in a timely manner.

**Citing**
If you use TrakEM2 in your research, please cite its primary publication:
Cardona, A.; Saalfeld, S.; Schindelin, J.; Arganda-Carreras, I.; Preibisch, S.; Longair, M.; Tomancak, P.; Hartenstein, V. & Douglas, R. J. (2012), "TrakEM2 for neural circuit reconstruction.", PloS one 7(6): e38011, PMID 22723842, doi:10.1371/journal.pone.0038011

**Performance**
For heavy-duty, memory-intensive tasks, run Fiji with specific JVM flags. For example, to assign 500 gigabytes of RAM to Fiji, use:
`./ImageJ-linux64 -Xms500g -Xmx500g -XX:+UseG1GC -verbose:gc -XX:+PrintGCDateStamps`
The `-Xms` and `-Xmx` flags set the initial and maximum heap sizes, respectively. The `-XX:+UseG1GC` flag enables the concurrent garbage collector, which is recommended. The `-verbose:gc -XX:+PrintGCDateStamps` flags log garbage collection details, which can be useful for debugging.

You can also improve performance by adjusting display properties:
- In "Display - Properties...", disable "snapshots mode" and "Prepaint". This will result in faster browsing of very large image displays, at the expense of seeing a checkered pattern while the image is loading.
- When importing a large collection of images, disable "enable mipmaps". This avoids multiple mipmap generations. After importing, you can enable mipmaps again.
- In "Project - Properties...", set mipmap threads to the number of CPU cores to accelerate mipmap regeneration.
- When generating Gaussian mipmaps, there are different strategies depending on image size and available RAM:
  - If the source image is smaller than 2 gigabytes and you have enough RAM, mipmaps can be generated from the full image in memory.
  - If the source image is larger than 2 gigabytes, mipmaps are generated from an intermediate, smaller version of the image to avoid memory errors.
  - The "pre-scale" parameter allows creating an even smaller version of the image, which can improve performance on systems with limited RAM. For example, a pre-scale of 0.25 on an 8-core machine with 16 GB of RAM can be a good starting point for large images.

Faster XML loading and lower memory consumption can be achieved by increasing the quadtree bucket size in "Display - Properties...", particularly for projects with large images or few objects per layer.

A rule of thumb for RAM allocation is to multiply the size of the largest single 2D image in your dataset by 10, and ensure that each CPU core has at least that much RAM available. For example, for 2k x 2k 16-bit images (8 MB), you would need 80 MB per core. With an 8-core machine, this would be 640 MB, but with a 32-core machine, you would need 2.5 GB.

For graphics cards, it's recommended to use the largest affordable option in terms of computing power and internal memory.
