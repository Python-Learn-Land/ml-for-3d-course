# Marching Cubes

Marching Cubes（行进立方体）是一种把体数据表示（volumetric representation）转换为稠密网格的算法。

![圆环点云](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/torus.gif)

1. **将空间划分为体素：** 把 3D 空间切分成一个体素（voxel，即立方体单元）网格。每个体素的大小决定了网格的分辨率。
2. **采样八个顶点位置：** 对每个体素，在其八个顶点（角）处采样密度值。根据密度判断每个顶点是在表面内侧还是外侧。
3. **确定三角形构型：** 每个体素有八个顶点，每个顶点有两种可能状态（内侧或外侧），因此共有 256 种可能的构型。每种构型对应一种特定的三角剖分模式。

下面我们更详细地走一遍这些步骤。

### 1. 将空间划分为体素

第一步是把 3D 空间切分成一个体素网格。每个体素的大小将决定网格的分辨率。

### 2. 采样八个顶点位置

对每个体素，算法会在其八个顶点处采样密度值。根据密度，每个顶点会被判定为处于表面 `inside`（内侧）或 `outside`（外侧）。

### 3. 确定三角形构型

每个体素的八个顶点各有两种可能状态，因此会产生 2⁸ = 256 种可能的构型。每种构型对应一种特定的三角剖分模式。

![Marching Cubes 查找表](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/MarchingCubesCases.png)

### 4. 生成网格

为了生成最终网格，算法会逐个「行进」（march）遍历每个体素，并套用对应的三角形构型 —— 「Marching Cubes」这个名字就是这么来的。

![Marching Cubes 头部模型](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/Marchingcubes-head.png)

这个过程会产出一个稠密、粗糙的网格，用来逼近该体积的表面。

## 局限性

虽然 Marching Cubes 网格适合用于可视化，但由于以下几个限制因素，它们大多不适合用于游戏等生产环境：

1. **多边形数量：** 高多边形数的网格在渲染时需要更多计算，这会显著影响实时应用的性能。
2. **边流（Edge Flow）：** 糟糕的边流会影响网格在动画中的形变表现，产生折痕（creasing）和挤压（pinching）等不良瑕疵。
3. **贴图：** 稠密而不规则的拓扑结构会让 UV 展开和贴图变得复杂，从而导致贴图瑕疵。

![网格拓扑](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/topology.jpg)

### 改进

像 [FlexiCubes](https://research.nvidia.com/labs/toronto-ai/flexicubes/) 这样的技术，通过允许网格顶点移动来创建更平滑的表面，解决了其中一些局限。这种方法被 [InstantMesh](https://huggingface.co/spaces/TencentARC/InstantMesh) 采用，而 InstantMesh 是目前[领先](https://huggingface.co/spaces/dylanebert/3d-arena)的开源 3D 流水线。不过，其产出的网格依然过于稠密，不适合投入生产。

## 在实践中

清理 Marching Cubes 输出的拓扑结构，往往比从零开始建一个网格还要耗费更多时间和精力。这就成了 3D 机器学习应用的一个重大瓶颈。如前所述，高斯泼溅为这个瓶颈提供了一种潜在的解决方案。

不过，最近也出现了直接针对这一瓶颈的研究工作，它使用可微分技术来产出拓扑质量更高的低多边形网格。这将在下一节中介绍。
