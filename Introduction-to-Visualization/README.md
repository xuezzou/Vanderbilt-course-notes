### Introduction to Visualization Course Notes
> Vanderbilt University Fall 2018, taught by Professor *Mattew Berger*
> 
> Transcribed by *Xue Zou*

#### Contents
- [Midterm Review](#midterm-review)
    - [d3](#d3)
    - [Data, Marks, Channels](#data-marks-channels)
    - [Pre-attentive Processing](#pre-attentive-processing)
    - [Graphical Perception](#graphical-perception)
    - [Rainbow Colormap](#rainbow-colormap)
    - [Colormaps](#colormaps)
    - [Line Plots](#line-plots)
    - [Multiple Views](#multiple-views)
    - [Spatial Arrangements](#spatial-arrangements)
    - [Interaction](#interaction)


- [Final Review](#final-review)
    - [Interaction](#interaction)
    - [Hierarchical Data Vis](#hierarchical-data-vis)
    - [Graph Vis](#graph-vis)
    - [Geo Data](#geo-data)
    - [Density Estimation](#density-estimation)
    - [Dimensionality Reduction](#dimensionality-reduction)
    - [Scalar Field Vis](#scalar-field-vis)
    - [Vector Field Vis](#vector-field-vis)


---

### Midterm Review

#### d3
- **data join**
    - **selections**
        - an array of *groups*, where each groups is an *array of elements* 
        - nested selections by calling `selectAll` on a selection
        - *method chaining* (aspect of `attr` returning the original `selection` object)
        - unlike `selectAll` `select` _propagates data_.
        - `select` _preserves the existing grouping_, and `selectAll` _creates new grouping structures_.
        - **keys**, **object constancy**
- scales (... and color)
    - map *abstract data* to *visual representation*
    - *data is the **domain**, visual encoding is the range*
        - also dependent on the data *attr types* (categorical, ordinal, quantitative), *channels* (spatial pos, size, color etc.)  
    - continous scales
    - `scale = d3.scaleLinear().domain([min_d,max_d]).range([min_x, max_x])`
    - `scale(d)`
    - `d3.scaleSqrt()`
    - `d3.scaleLog()`
    - `var min_circle_x = d3.min(circle_data, d => d[0]), max_circle_x = d3.max(circle_data, d => d[0])`
    - `d3.scaleQuantize()`
    - Band Scales 
        - domain: ordinal 
        ```javascript
        var bar_group_array = [0, 1, 2, 3];
        var bar_scale_group = d3.scaleBand().domain(bar_group_array).range([0,width])
        bar_scale_group.paddingInner(0.05);

        d3.selectAll('g').attr('transform',(d, i) => '')

        ```
- no need to worry about axes, labels, legengs, titles, etc...

#### Data, Marks, Channels

##### Data

- **Expressive**: convets all facts in the data, and only the facts

- Data Types: 
    - *item* 
    - *attribute*
    - *link*
    - *position* (spatial location 2D or 3D), *grid*
        - E.g. a car is an *item*, a car's horsepower, year, color are its *attributes*, user similarity of cars *link* (relationship between items)
- Dataset Types:
    - *table*
    - *networks*: given a set of items (nodes), a network defines a set of links (edges) between nodes establishing some kind of relationship
        - often represented with multiple tables
    - *tree*
    - *fields*
    - *geometry*: (curves, surfaces, volumes) Map!
- Combinations
    - *set*
    - *list* (items with a specified ordering)
    - *cluster* (derived from data)
- Attribute types
    - *Categorical: nominal*
    - *ordered: ordinal* (natural ordering) (can't necessarily perform mathematical orperations though)
    - *ordered: quantitaive* (math op)
    - diverging vs. sequential
    - hierarcihcal (*scale*, at what granularity are attributes described?)
- **Visual Marks**
    - geometric primitives, *points* *lines* *areas* purely geo types

- **Effectiveness** 
    - We decode a visual encoding through making *visual queries*
    - *attention* Top-down process
        - what you see depends, in large part, on what you are paying attention to 
    - Visual working memory: we can only hold about three objs in attention at one time *change blindness*
        - Visual working memory is limited
        - the limit: people experience **cognitive load**, do not take in all info 
    - **preattentive processing**
        - eye movements: fixation(attentive), saccade(preattentive)
    - choice of visual encodings (channels) shhould promote preattentive processing! 
    - ensemble encoding
    - being attentive (fixation)
        - **graphical perception**: our ability to extract quantitative info from graphs
        - **tasks** are integral to determing effectiveness
    - channel effectiveness
        - **our perception of sth changing is NOT linearly porportional to the *actual change** S = I^n Power Law
            - length n = 1, Saturation n = 1.7, brigthness n = 0.5  
            - (Channel Rankings)
                - position (aligned and unaligned) length(1D) angle area(2D size) depth(3D position) color luminance color brightness curvature volume  
    - nonaligned scales
    - Framed rects
    - **Weber's Law** dp = k dS/S
        - if the stimulus is small, it is easier to perceive differences
        - McGill and Cleveland 1984
            - User Studies
            - TASK: What ratio is the smaller value of the larger value? (position v.s. length)
    - Pie Charts (greated errors) vs. Bar Charts 

- other tips
    - Be familiar with these concepts
    - visual marks (points, lines, rects, etc.)
    - customize mark appearance via visual channels
    - map data items to marks, data attributes to channels: this gives us the space of data visualizations (re: assignment 3)
    - Data has different types of attributes, there are also different types of visual channels, need to be careful with this mapping!
        - **Expressivity** and **effectiveness** principles
    - Ways to combine channels for a single data attribute?
        - data attribute has 30 categories, run into issue when using data, can't use color to represent all the attributes   
     

#### Pre-attentive Processing
- What is it?
    - Examples 
- What can be pre-attentively process?
    - Pre-attentive visual channels
    - hue shape length orientation curvature size density depth etc.  
- What can't we pre-attentively process? 
    - most combinations of visual channels

#### Graphical Perception
- Effectiveness rankings
- Why is one more effective than the other? [Cleveland & McGill] paper
    - data line scale/line axes, fall apart?

#### Rainbow Colormap
- bad bad not good
    - why rainbow is bad? ineffective, several reasons
- but why bad?

#### Colormaps
- What are the circumstances under which we should use a particular color map?
- Consider: **luminance**, **chromaticity**, "get it right in black and white", spatial contrast, color spaces for interpolation, simultaneous contrast, etc... *color is important*!
    - one color next to another one, perceptual difference
    - Lab, luminace channel, perceptual uniformity

#### Line Plots
- Why use line plots?
    - why line plot over scatter plot
    - cast?  
- What considerations do we need to make when creating line plots? Perceptual factors?
    - key factor regarding line plot

#### Spatial Arrangements
- What are the advantages and disadvantages of the different ways we spatially arrange data?
    - tradeoff in different visual encodings

#### Multiple Views
- What are the different ways in which we spatially lay out data across multiple views?
    - trellis plot, superinposition
- What factors are at play when considering multiple views?
    - e.g. if we have a trellis plot, full of scatterplots, and individual plots have widely varying data scales...?
        - fixed scale? varied? particular issue with scale 

#### Interaction
- Information-seeking mantra!
    - concept
- What does it mean to change our viewpoint?
    - change camera
    - panning and zooming
    - corresponding geo transformation
- What are the types of ways in which we perform interaction for coordinated views?

---

### Final Review

#### Interaction

> - **different brushing mechanisms for** 
>       - <mark>scatterplot Matrix</mark>
>       - <mark>parallel coordinate plots</mark>
> - Design space of interactions for mult Views
>       - source, target, links, aggregation/de-aggregation
 
- different types of interaction
    - Manipulating View: Changing our viewpoint, animated transitions, adjusting arrangments
    - Brushing
    - Coordinated Views: linked selection/manipulation between many views
- Process of interacting with Data
    - Overview First
    - Zoom
    - Filter
    - Details-on-demand
- Semi-automatic navigation
    - *sense of context* that is maintained by a smooth transition when navigating with a camera by the user
    - Pan and Zoom: *Smooth* and **Efficient**
- Animation impact 
    - memory of marks & ratio assessments
    - static & animation & staged animation
    - static alwyas perform largest error
- Other interaction
    - Spatial Rearrangement
        - Recall: Plots involving categories are highly dependent on ordering of categories
        - ordering impacts how we perceive patterns
        - allow the user to arrange categories themselves
    - manipulate scales / colormaps / parameters of a vis technique
    - more options, in general, better (but can backfire)

---

- **Brushing**
    - **Selection** (fundamental action for interactive vis) is realized by *brushing*: a user performs some action on marks
        - Procedure: 
            - Mouse click
            - draw a shape with mouse: rect / circle / lasso
            - simply hover mouse cursor over marks
        - **Output**: selection, that often serves as **input** for subsequent operation
    - Why Brush
        - Change view, highlight data, filter out data, filter out attributes, obtain more details about data/attributes, *coordinated views*
    - Lifecycle
        - Initiate
        - begin: drag a shape, update view of selected (highlight), trigger other selections (e.g. multiple views)
        - stop: change viewport, filter, details-on-demand
        - clear
- Coordinated Views (interaction between views)
    - design decisions
        - visual encoding (same or different)
        - is brushing linked between views
        - data sharing (are data items / data attribute disjoint? overlap? )
            - e.g. scatterplot matrix: same data items disjoint data attribute
            - subset
        - navigation (is synchronized between views)
- Formalized Linked Brushig
    - Source
    - Target
        - de-aggregation (one-to-one)
    - Link
        - minimizing occlusion between links


#### Hierarchical Data Vis

> - know different approaches, advantages/disadvantages
>       - containment methods (like treemaps)
>       - node-link diagrams (tidier tree layout)
>       - icicle plots, sunburst, etc.
>       - details on treemap, squarified treemap alg
>       - compound graphs - *hierarchical edge bundling*

- **Containment**
- Tasks in Visualizing Hierarchical Data
    - Attribute-free (e.g. just the topological structure)
    - Attribute-based (e.g. quantitative)
        - *derivation*: aggregating quantitative data to non-leaf nodes
- Vis Representation(marks, channels) 
    - Node-link diagram 
        - channels: y-pos for depth, x-pos for nodes at a given depth
    - Icicle
        - channels: y-pos for depth, x-pos for nodes layout 
    - Radial node-link
    - Concentric Circles
        - Channels: radius for depth, angle for node positioning at each depth
    - **Treemap**
        - Channels: x-y position of rectangles, area (quantitative value)
        - Marks: 2D area for nodes, edges are implicit from *area containment*
- Treemap Alg
    - Along which axis do we create children rects
        - can alternate per level
        - can also decide on the longer of the 2 sides
    - `a0 = (w x h) s0 / s = (w0 x h)`
    - `x0 = x(bar) + w x (s0 / s)`
    - Order? it matters 
        - difficult to determine, making *local* decisions at each node, but we are unaware of *global* layout of rects
    - Cushioning emphasize nesting
- [Squarified Treemaps](https://www.win.tue.nl/~vanwijk/stm.pdf)
    - minimize *aspect ratios*
    - stack horizontally
    - Given: *target rect areas*
    - *must have equal width*
    - `sum of ai = h x w(~)`
    - `w(~) = sum of ai / h` `hi = ai / w(~)`
    - This computation is necessary for determing whether or not to add a rectangle to an existing stack of rects (or begin a new stack started in the remaining subrect)
- Impact of Aspect Ratio
    - Bigger differences, more difficult to estimate ratios
    - For extreme aspect ratios, different orientations impact perception
    - At the extremes (square and skinny rectangles), humans perform worst at ratio assessments
- Impact of Data Density
    - Recall channel effectiveness: spatial channel > length channel > area channel
    - But what if the data density is high
    - Perception is worse at hierarchical bar charts from the data
- Guidlines
    - Avoid extreme aspect ratios (close to 1, >>>1)
    - Treemaps are good for **high density**
    - Treemaps are effective in comparing non-leaf nodes

---

- Node-link vs. Containment
- Node-link
    - better for visually querying paths between nodes
- Tidier Drawing of (Binery Trees)
    - Aesthetic: (Symmetry: aid in perception)
    - Alg
        - Visit nodes of a tree in postorder traversal (e.g. left, right, then we process)
        - If child: assign arbitrary position
        - If parent, and has 2 children
            - Overlap the 2 child subtrees, push them apart until they no longer overlap, with some prescribed minimum distance. Store relative shifts at nodes.
        - Compute absolute positions in a preorder traversal 
    - Makeing it efficient
        - only compare **contours**
        - Test: does the left subtree's *right* contour overlap with the right subtree's *left* contour?
        - Store contour nodes via so-called *threads*
        - from quadratic to linear time
    - [Tidier Trees](https://beta.observablehq.com/@mbostock/d3-tidy-tree) 
- [Radial Tidier Trees](https://beta.observablehq.com/@mbostock/d3-radial-tidy-tree)
- Icicle: Focus (allow user to expand rects) + Context(shrink the sibling rects)
- Sunburst: Focus(allow user to expand arcs) + Context (Shrink the arcs of siblings)

---
- Compound Views
    - Use hierarchy to *position nodes*, then *draw remaining edges* of the leaf nodes
    - Hierarchical Edge Bundling
        - Adv: perceive hierarchical relationships from leaf relationships, identify clusters, mitigate clutter 
        - Paths *Least Common Ancestors*
        - Challenges: *Ambuiguity* clutter still a problem(particularly long paths obscuring small paths)
    - Edges: B-Spline Curves
- **Ambiguity** Still Problematic

    - multiple curves may consist of the same control points
    - depending on the order (3) and number of control points (LCA), the curves can exactly match
- Resolving ambiguity
    - *Shrink the control Points, to "straighten" the curve* (belta smaller less ambiguity) 
    - (adjust control points to make every path "unique")
    - opacity: blend paths
- Limitation of HEB 
    - Relationship between non-leaf nodes is *implicit*, based on path geometries
    - Showing all at once, leads to **clutter**
    - consider *interaction*


#### Graph Vis

> - Force-directed Graph Layout
>       - General procedure
>       - Attractive/repulsive Forces
> - Matrix vis: showing edges as a matrix view
>       - why better than node-link diagrams? Worse?

- Aesthetics in Graph Layouts
    - minimize edge crossings
    - minimize edge bends
    - maximize symmetry
    - maximize the min angle between edges leaving a node
    - maximize edge orthogonality
    - (directed edges) maximize consistent flow direction)
- **Force-directed Graph Layout**
    - Intuition 
        - Nodes that contain an edge should be close to each other
        - Nodes that do not contain an edge shhould be far from each oth
    - a random pos to every node
    - associate a **displacement vector** with every node 
        - If one node is close to another node, and they do not form an edge, then move them apart!
        - If one node is far apart from another node, and they do form an edge, then move them together! 
    - Move each node along its displacement vector
- Hooke's Law
    - *`F = kd`* d: distance - signed, so the force is *directional*
    - k = C x sqrt(area/|V|) Prescibed Distance
        - Stiffness: the average distance we expect for nearby nodes 
        - Intuition: general expectation of edge length, distance less than k repulse 
    - Repulsive Forces 
        - if two nodes are nearby (as measured by stiffness) and do not form an edge, then they should be repulsed!
        - scale f(d) = k^2 / d 
            - when d is already large, then not that much impact
    - Attractive Forces
        - Attraction: if two nodes are far away (as measeured by stiffness) and thehy are connected by an edge, then they should be attracted!
        - f(d) = d^2 / k amount of attraction (d large, then large influence)
- Process ()
    - initialize node pos
    - for every pair of nodes
        - If they are not connected, accumulate their **repulsive force** into a displacement vector
        - If they are connected by an edge, accumulate their **attractive force** into a displacement vector (sum)
    - For each node: 
        - Add the displacement vector to the node, taking care of the **boundary**
        - And Repeat
- Step Size 
    - update u = u+ du
    - the update might be really big
    - we need to control ohw muchh we step at each iteration 
        - `u = u + n(i)d(u)` 
        - `n(i + 1) = lambda n(i) , lambda < 1` decrease the update from the last one
        - `n(0)` is tuned by the user
        - nodes have only 1 -degree are forced into outside
-Problems
    - *Local minima abound*!
        - Dependent on initialization of node positions, optimization parameters (step size, decay, etc...)
        - poor computational comlexity for large graphs  

---

- **Tutte Embedding**
    - Problem with Force-Directed Layouts
        - not deterministic because of local property
    - Planar: graph can be drawn in 2D with no edge crossings
    - 3-connected: if I remove *any* 3 edges from theh graph, it remains connected  
    - unique with *boundary*
    - Place points in the center (Bartcentric Coordinates)
        - solve the Graph Laplacian (with the righ hand side of the matrix as boundary) gives us the positions (the *interior*)
- Spetural Graph Theory
    - compute the eigenvectors/values of the Laplacian
    - set the boundary free
    - minimizes the distances of edges in the graph
- **Matrix Views**
    - Visualize a graph in terms of a matrix
    - each row index: a unique node, each col index: a unique node 
    - Ordering?
        - Fiedler vector
            - The eigenvector of the Graph Laplacian with the second smallest eigenvalue
            - it minimizes "distances"
            - highly connected: similar value
            - Provides an ordering for the nodes: those that have similar values should be nearby in the ordering (likely clusters)
    - Spectral Reordering
        - Compute the Fiedler vector
        - sort the Fiedler vector
        - permute the nodes based on the sorted indices
- Node-Link vs. Matrix 
    - Node-link Diagram Advantages
        - No order dependency
        - Easier to trace paths, not just edges
        - Scalability: can handle of order 10-100K nodes (depending on edge distribution)
    - Matrix Advantages
        - Clusters, and connectivity in general, are more apparent
        - No overdraw issues - can easily identify edges, as well as labels


#### Geo Data
> - Criteria in map projections 
> - (no need to memorize different types of proj)
> - Simplification of geo shape data (poly-line data)
>       - Douglas-Peucker Alg
>       - Visvalingam & Whyatt Alg

- Geospatial data: ingredients
    - geo-derived attributes: parimeter, area, the "center", etc..
    - external attr: anything! (e.g. census data)
- color: sequential 
- Cartography
    - study of creating maps
    - factors: *projetcion*, scalability, intrinsic 

- project preserves *distance*
- a mapping from Earth to 2D domain does not exist! 2D domain has zero (Gaussian) curvature, the Earth does not
- [Area-Preserving Projections](https://bl.ocks.org/syntagmatic/raw/ba569633d51ebec6ec6e/)
    - e.g. color-mapping countries by their area
- Angle-Preserving Projections
    - e.g. viewing a single state, min distortion of TN, max distortion on other States
- [Tissot](https://www.jasondavies.com/maps/tissot/) 
- Simplification (Poly-line)
    - Douglas-Peucker Alg
        - Form line between start and end points
        - Compute distance from each point to the line 
        - Identify point with largest distance
        - If this distance is larger than a user-defined threshold, then divide and conquer
        - Repeat! Stopping condition: maximum distance is below threshold
        - Invariant: resulting simplified curve is guaranteed to be at most threshold distance away from original curve
        - *Worst* case: quadratic!
    - Visvalingam & Whyatt Algorithm
        - Form triangle for every 3-sequence of points
        - Compute area for each triangle, store in a priority queue
        - Repeat:
            - Pop triangle with smallest area from queue, simplify
            - Update triangles adjacent to simplified segment
            - … until a user-specified area threshold is met
         - Complexity: O(nlogn)

#### Density estimation

> - Kernel Density Esitimation
>      - basic idea (derive a continuous function that captures density of density of discrete set of data)

- asocciate a quantitative attribute derived from the points - **density**
- *Goal*: assign for any value in the domain a quantitative value that captures how many points are nearby
- So how do we estimate density? We use a so-called kernel function - typically a Gaussian

- **sigma** contrls thhe spread / the bandwidth
    - decay slow sigma large, decay fast sigma slow
    - plot like a hat 
    - Intuition: if distance is much less than the bandwidth, then the Gaussian is close to 1; if distance is much greater than bandwidth, then Gaussian is close to 0.
- for value in domain , take its density estimate as the sum of the kernel over the entire dataset
    - sum of the gussian x and y and close to K is close to one
    same gussian summing over fewer points (outside the spread contribute less) low density
    - counting point nearby the gussian 

- K: scale const: *normaolization has no impact on visualize density*
    - normalization const, 
    scale everything by const amount
    still linear interpolation of that space
    other function / other scale did change thhe map
- 1D -> 2D: same idea, we just replace squared diff with squared Euclidean distance!


#### Dimensionality Reduction
> - What is it?
> - Principal Component Analysis
>   - Assumptions made on high-dim data?
>   - What are the principal components Interpretation for vis in the **2D** projection
>   - Nonlinear Dimensionality Reduction
>   - Isomap: major steps involved (we go from a matrix of estimated geodesic distances, to a different matrix … for what reason?)

- Given data in high-dim space, find low dimensional **representation**
- Linear Dimensionality Reduction
    - Assumption: Points in high-dimensional space live on or near a *low-dimensional* subspace
- Why dimensionality reduction? 
    - helps us understand high dimensional data
    - uncover latent structure in data!
- **Principal Component Analysis**
    - Intuition: find directions that *go thru the data*
    - [PCA Formulations](http://setosa.io/ev/principal-component-analysis/)
        - Maximize variance
        - first direction from eigenvector of the *Covariance Matrix*
        - second direction 
            - want next direction to maxize variance *left over* from the previous direction
            - Directions should be **orthogonal** 
        - Low-dimensional representation: project (mean-centered) data onto the top principal coordinates
- PCA: Noiseless Case
    - if the data truly lives in a low-dim subspace, we recover the subspace
- PCA: Noise Case
    - Basic idea: orthogonal complement of subspace is mostly noise, so projection naturally serves to denoise
- PCA is very sensitive with outlier! completely mess up the covariance matrix

---
- Non-linear
    - Assumption: Points in high-dimensional space live on or near a *low-dimensional* **manifold**
    - Intuition
        - like graph layouts
        - points that are "close" in high dim space should be "close" in 2D
        - points that are "far" in high him space should be "far" in 2D
    - Geodesic distances: approximated by computing the **shortest path** d(i, j) between a pair of nodes in the graph
    - Computing it efficiently: Dijkstra's alg
- **Isomap**
    - objective: Find 2D points, such that the distance between any two points in 2D equals their approximated geodesic distance
    - `Z transpose(Z) = S` Factorize the geodesic distance matrix
    - D -> S -> Z


#### Scalar Field Vis
> - Interpolation!
>   - (namely) bilinear interpolation
> - isocontouring
>   - 2D: gives closed curve (contouring squares)
>   - 3D: gives closed surface (contouring tetrahedra)
>   - The cases for contouring

- Interpolation
    - nearest neighbor interpolation
    - linear interpolation
    - barycentric interpolation
    - bilinear interpolation
        - `f(x, y) = [1-y y][first row f00 f01 second row f01 f11] (transpose of[1 - x x]`
- isocoutouring
    - an *isocontour* for a given value `{p | p = f-1(v)}`
    - more formally known as the *pre-image of a function*
    - closed (except at boundaries)
    - nested: isocontours of differert values do not cross
        - no intersection or trying to map different one to 2 
    - Contours and Gradients
        - Gradients: points in the direction where function is *changing the most*
        - Gradient is perpendicular to isocontour
        - steepest directions 
- How do we compute these contours?
    - sps we have a scalar field defined on a regular grid
    - look at each cell, one-by-one
    - derive *geometric primitives* for each cell when we detect that the contour is *crossing the cell*
    - how do we know if the contour is crossing the cell?
    - **Sign changes**
- 2D Cases
    - No crossing (2 cases) (all four have f < v or f > v)
    - 1 crossing (8 cases, between sign changes and rotations, one have f < v / f > v or sign opposite)
    - 1 crossing (4 cases, between sign changes and rotations, two with f < v on one side and two with f > v on one side
    - 2 crossings (2 cases, f < v on two opposite corners)
        - Ambiguity, where to put the crossings
        - use **bilinear interpolation** to clarify
        - bilinear interpolation gives rise to *hyperbolic* arcs
        - by comparing the pos of the asymptotes and points lying on the border of the cell we can determine the direction of the crossing

---

- 3D scalar Field
- isocontour gives us a surface
- Volume Domain
    - (2D) triangle mesh: (3D) tetrahedral mesh
    - (2D) quadrilateral mesh: (3D) hexahedral mesh
- **Tetrahedron**
    - generalization of a triangle to 3D (**4 vertices** 6 edges 4 triangles)
    - to compute *edge crossings*
        - no crossing all f > c of f < v 2 cases
        - 1 crossing (also 8 cases when 1 is f < v and 3 f > v)
        - 6 cases when 2 have f < v and 2 f > v
- a regular 3D grid / contour a cube
    - 2^8 = 256 cases


#### Vector Field Vis
> - Three main types: Glyphs, geometry-based, image-based
>   - integral curves
>   - Streamlines
>   - LIC
>   - Pathlines, timeslines, streaklines
>   - FTLE

- Glyphs:
    - represent a vector with some shape that shows the magnitude and direction.
    - vector/glyph placement can be **uniform** or **jittered** (based on data points) on a graph.
    - examples include: arrows, hedgehog plots
- Geometry: integral curves 
    - We can use integral curves to determine the path of how a particle will travel in the vector field.
    - A curve is an integral curve of a vector field, if tangent curve at each point is equal to vector field at that point. Use Euler's Method (from Ordinary Diff Eq) to compute integral curves.
    - Image: https://prnt.sc/lsj8xc
- Streamlines:
    - sets of integral curves for a steady vector field (vector field that does not change over time)
    - Challenges:
        - seeding: where do we place points for computing integral curves
        - how long do we integrate particles for?
- Image-based: Line Integral Convolution (LIC)
    - most common image based method is LIC.
    - Diagram: https://prnt.sc/lsjb14
    - Circular flow field: https://prnt.sc/lsjbgr
    - Turbulent flow: https://prnt.sc/lsjbkc
- Pathlines, Timelines, Streaklines and Streamlines:
    - We need to consider these for time-varying vector field.
    - **Pathlines**:
        - are the trajectories that individual fluid particles follow. These can be thought of as "recording" the path of a fluid element in the flow over a certain period. The direction the path takes will be determined by the streamlines of the fluid at each moment in time.
    - **Streamlines**: 
        - are a family of curves that are instantaneously tangent to the velocity vector of the flow. These show the direction in which a massless fluid element will travel at any point in time.
    - **Timelines**: 
        - are the lines formed by a set of fluid particles that were marked at a previous instant in time, creating a line or a curve that is displaced in time as the particles move. 
        - [Diagram](https://prnt.sc/lsjdtw)
    - **Streaklines**: 
        - Fix a point at a specific position. Look at all the integral curves that pass through there. Make sure all curves have the same ending time step. Form a curve that connects all of the points.
        - [Diagram](https://prnt.sc/lsjf4g)
    - **Pathlines** 
        - may intersect, means a clutter of particles at some specific time. Streamlines never intersect, because that means some point in the vector field has more than one velocity vector (not possible).
- Finite-Time Lyapunov Exponent (FTLE)
    - [example](http://shaddenlab.berkeley.edu/uploads/LCS-tutorial/FTLE-interp.html)
    - Given a set of points close to each other, after a series of time steps, how far is their **separation**?
    - Cauchy-Green Tensor, tells us for a given vector, how much that vector stretches (separation) and in what direction.
    - We take maximum eigenvalues and use a scalar field to compute this visualization: https://prnt.sc/lsjhi3