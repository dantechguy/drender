*This source code was extracted and copied from another project to this repository.*

# Dan's Renderer

**What is it?** A declarative 3D renderer library which only draws with canvas primitives, and therefore cannot use a Z-buffer and instead uses a new implementation of the Painter's Algorithm.  
**Why?** I was curious how a Painter's algorithm implementation (and associated problems and optimisations) would differ from the traditional Z-buffer approach.

Has several interesting novel implementation details which differ from a Z-buffer implementation:
- **Proper tri-sorting.** A key part to the painter's algorithm is to sort the tris in the scene back-to-front for rendering, and you can't just sort by distance. See `src/sorting/sort_tri_tri.dart` for the algorithms for this.
- **Declarative.** You define the world by composing objects and modifiers in a tree. For example, to create a rotated, cube-shaped room to stand in you would do something similar to: `rotated(45, inverted(cube()))`. The invert step flips the direction of the normal of the cube's faces, so they are visible from the inside. See `src/scene_items/` for all of the shapes and modifiers, including lighting.
- **Optimisations.** An O(n^2) sort must be performed (as a naiive topological sort, treating the objects as a DAG will not always work), so reducing the number of objects in each comparison space is important. This is why grouping objects into their own render-layers (in which they are only sorted within that layer) is crucial to good performance.
- **Tri slicing.** When a tri intersects the camera plane or another tri, it must be split into two separate tris during the render phase to appear valid. For the camera, this is because otherwise the half of the tri behind the camera would still be rendered, and you can't just cull the tri altogether as it is still partially visible.
