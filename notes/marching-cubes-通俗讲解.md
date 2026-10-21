# Marching Cubes 通俗讲解

> 本文是对 [units/zh-CN/unit1/meshes.md](../units/zh-CN/unit1/meshes.md) 与 [units/zh-CN/unit4/marching-cubes.md](../units/zh-CN/unit4/marching-cubes.md) 的补充笔记，用更通俗的方式把 Marching Cubes 讲清楚。
>
> 文中所有组合数、面数、精度数据都由参考实现（Python + numpy 与 TypeScript 两份，逐位对拍）实测得到，不是估算。
> 交互可视化有两个入口，内容相同：
>
> 1. **仓库内独立网页（推荐，任何浏览器都能开）**：[interactive/marching-cubes.html](interactive/marching-cubes.html)。
>    单文件、零依赖、可离线，双击或 `python3 -m http.server` 后访问即可。四个页签：二维直觉 / 256→15 查表 / 三维实跑 / 代价与局限。
> 2. **Codex Canvas**：`~/.cursor/projects/home-linux-note-ml-for-3d-course/canvases/marching-cubes-explorer.canvas.tsx`，在 Codex 里点开可在聊天旁并排打开。

## 一句话理解

Marching Cubes（行进立方体）就是：**把一团连续的「场」切成无数小方块，逐个方块判断表面从哪儿穿过，再把穿过的地方拼成三角形**，最终得到一张三角网格。

它是一个「扫描式贴瓷砖」算法：不做任何几何优化，只是逐格查表、拼接。

## 它到底在解决什么问题

神经网络很容易学、也很容易输出的 3D 表示，通常是一个**场（field）**：

```
输入：空间坐标 (x, y, z)
输出：一个标量
```

常见的两种：

| 表示 | 含义 | 物体内部 | 物体外部 |
| --- | --- | --- | --- |
| 密度场（density） | 该点的「物质浓度」 | 值大 | 值小 |
| 有符号距离场（SDF） | 到最近表面的距离 | 负数 | 正数 |

问题是：游戏引擎、渲染器、3D 打印切片软件都**只认顶点和三角面**。场没法直接喂进去。

Marching Cubes 就是这个翻译官：把场的**等值面**（比如 SDF = 0 的那层壳，或密度 = 某个阈值的那层壳）提取成三角网格。

```
神经网络 → 场（SDF / 密度） → Marching Cubes → 三角网格 → 引擎 / 渲染 / 打印
```

课程里说的「第 1 步进展迅猛，第 2 步自 1980 年代以来几乎没变」，指的就是这个翻译环节。

## 算法四步

### 1. 把空间切成体素网格

把物体所在的空间切成 `N × N × N` 个小立方体（体素，voxel）。

- `N` 就是**分辨率**。`N` 越大，表面越光滑，但计算量和面数暴涨（见下文实测数据）。
- 一共有 `(N-1)³` 个小立方体需要逐个检查。

### 2. 在每个立方体的 8 个角上采样

对每个小立方体，查询它 8 个角点处的场值，再和阈值 `level` 比较：

- 场值 `< level` → 这个角在表面**内侧**（inside）
- 场值 `>= level` → 这个角在表面**外侧**（outside）

关键洞察：**如果一条棱的两端一个在内、一个在外，那么表面必然穿过这条棱**（连续函数的介值定理）。所以「被切开的棱」（cut edge）就是表面进入/离开这个立方体的位置。

### 3. 查表确定三角形构型

8 个角各有内/外两种状态，所以一共有 `2⁸ = 256` 种组合。

每种组合对应一张**预先算好的模板**：这个立方体里该放几个三角形、顶点怎么连。运行时不做几何推理，纯查表。

被切开的棱数决定了三角形顶点的位置，用**线性插值**估算精确坐标：

```
t = (level - f_a) / (f_b - f_a)      # f_a、f_b 是棱两端的场值
交点 = a + t × (b - a)               # a、b 是棱两端的坐标
```

因为顶点总是落在**棱上**，相邻立方体共享同一条棱，所以拼出来的网格天然是水密（watertight）的——没有缝、没有洞。

### 4. 逐格「行进」

挨个立方体套用模板，把所有小三角形拼起来，就是完整表面。像士兵列队一样一格一格走过整个网格——**这就是名字里 marching（行进）和 cubes（立方体）的由来**。

## 直观类比

### 二维版本：等高线地图（Marching Squares）

二维更好懂。你有一张海拔场，想找出「海拔 = 100 米」的那条线：

1. 把地图切成小方格
2. 看每格四个角哪些高于 100、哪些低于 100
3. 在跨越 100 米的边上打点
4. 把点连成线段

这就是 **Marching Squares**。二维一共 `2⁴ = 16` 种组合，考虑旋转、镜像、内外互换后只剩 **4 种基本形状**；其中 2 种是鞍点（对角两内两外），需要额外规则决定怎么连。

Marching Cubes 只是把它升到三维：线变成了面，16 变成了 256。

### 另一个类比：积木拼曲面外壳

用一堆小积木块去逼近一个曲面外壳，每块积木只负责自己内部那一小片面，最后严丝合缝拼成一个封闭的壳。

## 256 → 15：为什么查找表这么小

256 种组合看起来很多，但绝大部分是**同一个形状的不同摆放**：

- 立方体有 24 种旋转（proper rotations）
- 「内外互换」（complement）得到的表面形状完全相同，只是法线方向反过来

把这两种等价关系都算上，256 种组合坍缩成 **15 种基本构型**（含「表面完全不穿过这个立方体」这一种）。这正是 1987 年原始论文的做法，也是文献里常说的「15 种 unique cases」。

> 如果再额外允许镜像（旋转 + 反射 = 48 种对称），会进一步合并成 **14 种**——因为其中一对构型互为镜像。经典文献引用的 15，是「旋转 + 内外互换」这一口径。

下表是这 15 种基本构型的实测统计（本文参考实现算出）：

| 序号 | 代表掩码 | 二进制 | 内侧角数 | 被切棱数 | 环数 | 三角形数 | 歧义面数 | 代表多少种掩码 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 0 | `00000000` | 0 | 0 | 0 | 0 | 0 | 2 |
| 1 | 1 | `00000001` | 1 | 3 | 1 | 1 | 0 | 16 |
| 2 | 3 | `00000011` | 2 | 4 | 1 | 2 | 0 | 24 |
| 3 | 5 | `00000101` | 2 | 6 | 2 | 2 | 1 | 24 |
| 4 | 20 | `00010100` | 2 | 6 | 2 | 2 | 0 | 8 |
| 5 | 7 | `00000111` | 3 | 5 | 1 | 3 | 0 | 48 |
| 6 | 21 | `00010101` | 3 | 7 | 2 | 3 | 1 | 48 |
| 7 | 26 | `00011010` | 3 | 9 | 3 | 3 | 3 | 16 |
| 8 | 15 | `00001111` | 4 | 4 | 1 | 2 | 0 | 6 |
| 9 | 23 | `00010111` | 4 | 6 | 1 | 4 | 0 | 12 |
| 10 | 27 | `00011011` | 4 | 6 | 1 | 4 | 0 | 8 |
| 11 | 29 | `00011101` | 4 | 6 | 1 | 4 | 0 | 12 |
| 12 | 30 | `00011110` | 4 | 8 | 2 | 4 | 2 | 24 |
| 13 | 60 | `00111100` | 4 | 8 | 2 | 4 | 2 | 6 |
| 14 | 90 | `01011010` | 4 | 12 | 4 | 4 | 6 | 2 |

「代表多少种掩码」一列加起来正好是 256。

几个可以直接读出来的结论：

- **一个立方体最多贡献 5 个三角形**（实测 256 种掩码的三角形数分布：0 个 ×2、1 个 ×16、2 个 ×50、3 个 ×80、4 个 ×76、5 个 ×32）。
- **最多 4 个独立的环**（分布：0 环 ×2、1 环 ×162、2 环 ×82、3 环 ×8、4 环 ×2）。
- 被切棱数只可能是 0、3、4、5、6、7、8、9、12（分布：`{0:2, 3:16, 4:30, 5:48, 6:64, 7:48, 8:30, 9:16, 12:2}`）——不存在切 1、2、10、11 条棱的情况。

## 鞍点歧义：Marching Cubes 的著名坑

如果一个立方体的某个面上，四个角**交替**内外内外，那么这个面有 4 条棱都被切开。此时有两种连法：

```
内  外          内 ─ 外          内  ╲ 外
        或                   或
外  内          外 ─ 内          外  ╱ 内
```

两种连法给出完全不同的拓扑。原始 Marching Cubes 在这里是**任意选择**的，而且相邻立方体可能选得不一致，导致：

- 表面出现**裂缝**（不水密）
- 出现**非流形边**（一条边被 3 个以上三角形共享）

实测这个坑有多大：**256 种掩码里有 120 种至少含一个歧义面**（近一半）。按歧义面个数分布：`{0 个:136, 1 个:72, 2 个:30, 3 个:16, 6 个:2}`。

解决方案是 **Asymptotic Decider**（渐近判定器，Nielson & Hamann, 1991）：额外采样面的中心点，用它决定怎么配对。后来 Natarajan（1994）指出立方体**内部**也有歧义，提出基于插值临界点的判定，并给 Marching Cubes 增加了 4 个新构型。

本文的参考实现用的是「面中心采样」消歧，并保证了**镜像一致性**：消歧规则按角点身份而非棱序号决定配对，所以任意立方体的镜像/旋转变体必然得到一致的连接，相邻格子不会打架。实测 48 种对称变换 × 256 种掩码，无一例不一致。

> 这是自己实现 Marching Cubes 时最容易踩的坑。用 `skimage`、Open3D 这些成熟库不会遇到，它们内部已经处理好了。

## 为什么大家还在用它

- **通用**：对任何「能查询的隐式场」都适用，不挑具体表示。NeRF、三平面、SDF 网络、高斯泼溅转出来的场，都能直接喂进去。
- **水密**：输出通常是封闭流形曲面，适合碰撞检测、3D 打印、体积计算。
- **易并行**：每个立方体独立处理，天然适合 GPU。
- **现成**：`skimage.measure.marching_cubes`、Open3D、PyTorch3D、`trimesh`，以及可微分版本 DMTet / FlexiCubes。
- **简单可靠**：算法逻辑几十行就能写对，几十年没变意味着没人会踩到未修的坑。

## 局限：也就是课程说的那个「断层」

### 1. 面数爆炸

分辨率每提高一档，面数按平方增长。本文参考实现在同一台机器上（纯 Python 单线程）实测：

| 分辨率 N | 立方体数 `(N-1)³` | 球体顶点 | 球体三角面 | 圆环顶点 | 圆环三角面 | 耗时（球/环） |
| --- | --- | --- | --- | --- | --- | --- |
| 16 | 3,375 | 480 | 956 | 552 | 1,104 | 0.01s / 0.01s |
| 24 | 12,167 | 1,128 | 2,252 | 1,320 | 2,640 | 0.03s / 0.03s |
| 32 | 29,791 | 1,992 | 3,980 | 2,368 | 4,736 | 0.06s / 0.06s |
| 48 | 103,823 | 4,632 | 9,260 | 5,472 | 10,944 | 0.18s / 0.18s |
| 64 | 250,047 | 8,376 | 16,748 | 9,792 | 19,584 | 0.42s / 0.42s |
| 96 | 857,375 | 18,960 | 37,916 | 23,280 | 46,560 | 1.34s / 1.38s |
| 128 | 2,048,383 | 33,696 | 67,388 | 41,024 | 82,048 | 3.15s / 3.19s |

光是 N=128 的球体就已经 6.7 万面。生产环境里的角色模型通常希望控制在几千到几万面，而 Marching Cubes 随手就是几十万面。

有个很漂亮的自洽校验（也说明网格质量本身没问题）：

- 球体（亏格 0）：`面数 = 2 × 顶点数 - 4`。实测 N=128：`2 × 33696 - 4 = 67388` ✓
- 圆环（亏格 1）：`面数 = 2 × 顶点数`。实测 N=128：`2 × 41024 = 82048` ✓

这正是欧拉示性数 `V - E + F = 2 - 2g` 在「每条边恰好被 2 个三角形共享」时的推论。两条公式分毫不差，说明输出确实是干净的水密流形网格。

### 2. 边流（edge flow）很差

三角形全是查表拼出来的，走向由体素网格决定，跟形体结构毫无关系：

- 关节处一做动画就出现**折痕（creasing）**和**挤压（pinching）**
- 布线不跟随肌肉/结构走向，雕刻和形变都很痛苦
- 想加细节线（edge loop）得手工重建拓扑

### 3. 贴图困难

稠密而不规则的拓扑让 **UV 展开**和贴图变得复杂，容易产生贴图瑕疵。

### 4. 分辨率绑定 + 阶梯感

细节受格子大小限制，薄结构、锐利边角容易被抹平或产生锯齿。提高分辨率能缓解，但代价是面数爆炸（回到第 1 条）。

### 结果

清理 Marching Cubes 输出的拓扑结构，**往往比从零开始建一个网格还要耗费更多时间和精力**。这就是 3D 机器学习应用的重大瓶颈。

## 精度其实相当好

值得澄清一个误解：Marching Cubes 的**几何精度**并不差，差的是**拓扑质量**。

本文参考实现在 N=192 下与解析值对比：

| 形状 | 表面积（实测 / 解析） | 相对误差 | 体积（实测 / 解析） | 相对误差 |
| --- | --- | --- | --- | --- |
| 球体 R=1 | 12.56540 / 12.56637 | 0.0077% | 4.18817 / 4.18879 | 0.0148% |
| 圆环 R=1, r=0.4 | 15.78995 / 15.79137 | 0.0089% | 3.15726 / 3.15827 | 0.0319% |

也就是说，只要分辨率够，形状是准的。问题在于**为了准就得付出海量面片，而且这些面片的拓扑对艺术家毫无用处**。

顺带一提：本文实现还修正了三角形绕向（winding），保证法线一致朝外（球体的有符号体积为 `+4.188`，而非负值）。很多简化实现会漏掉这点，导致背面剔除、光照、体积计算全部出错。

## 后继与改进

| 方法 | 年份 | 解决什么 | 现状 |
| --- | --- | --- | --- |
| Marching Tetrahedra | — | 绕开专利，同时消除歧义问题（四面体没有鞍点歧义） | 常用 |
| Asymptotic Decider | 1991 | 面上的鞍点歧义 | 已并入主流库 |
| Natarajan 临界点判定 | 1994 | 立方体内部的歧义，新增 4 个构型 | 学术 |
| Dual Contouring | 2002 | 允许顶点移出格子，能保住锐边 | 常用 |
| DMTet（Deep Marching Tetrahedra） | NeurIPS 2021 | 可微分的等值面提取，支持梯度优化 | NVIDIA，有代码 |
| FlexiCubes | SIGGRAPH 2023 | 让顶点可移动，产出更平滑的表面，专为梯度优化设计 | [InstantMesh](https://huggingface.co/spaces/TencentARC/InstantMesh) 采用 |
| MeshAnything / MeshGPT | 2023–2024 | 把稠密网格转成**艺术家级低多边形**网格（自回归 Transformer） | 见 [网格生成](../units/zh-CN/unit4/mesh-generation.md) |

FlexiCubes 解决了平滑度和锐边问题，但**产出的网格依然过于稠密**，不适合直接投产。真正把「面数」这一条打下来的，是 MeshAnything 这类可微分拓扑简化路线。

## 专利这段历史

Marching Cubes 曾经被专利覆盖，这也是它发展史上一个有趣的插曲：

- 论文：Lorensen & Cline, *Marching cubes: A high resolution 3D surface construction algorithm*, ACM SIGGRAPH Computer Graphics 21(4):163–169, 1987 年 8 月
- 专利：**US 4,710,876**，受让人 General Electric（通用电气），发明人 Harvey E. Cline、William E. Lorensen
- 申请日 1985-06-05，授权日 1987-12-01，**2005-06-05 到期**，现状态 Expired – Lifetime
- 专利期内，社区为了绕开它开发了 **Marching Tetrahedra**（顺带解决了歧义问题）
- 2005 年之后可以免版税自由使用

最初的动机是 **CT 医学影像可视化**——从一堆断层扫描切片里重建出头部/脑部结构的表面（论文里那个例子约 15 万个三角形）。这也解释了为什么它天然处理「密度场 + 阈值」这种输入形式。

## 想自己跑一下

### 最省事：用现成库

```python
import numpy as np
from skimage.measure import marching_cubes
import trimesh

g = np.linspace(-1.5, 1.5, 64)
x, y, z = np.meshgrid(g, g, g, indexing="ij")
sdf = np.sqrt(x**2 + y**2 + z**2) - 1.0          # 一个球
h = g[1] - g[0]
verts, faces, _, _ = marching_cubes(sdf, level=0.0, spacing=(h, h, h))
trimesh.Trimesh(verts, faces).export("sphere.obj")
print(len(verts), "verts,", len(faces), "tris")
```

把 `64` 改成 `128`，面数会从 1.6 万涨到 6.7 万——这就是上文那条曲线。

### 想看清算法：最小可运行实现

下面这份是本文验证过逻辑的极简版（约 60 行），刻意保留了消歧和绕向处理，可以直接跑：

```python
import numpy as np

COR = [(0,0,0),(1,0,0),(1,1,0),(0,1,0),(0,0,1),(1,0,1),(1,1,1),(0,1,1)]
ED  = [(0,1),(1,2),(2,3),(3,0),(4,5),(5,6),(6,7),(7,4),(0,4),(1,5),(2,6),(3,7)]
# 面的角点按「从立方体外侧看逆时针」排列，这是绕向一致的关键
OFACES = [(0,3,2,1),(4,5,6,7),(0,1,5,4),(3,7,6,2),(0,4,7,3),(1,2,6,5)]
EO = {}
for ei,(a,b) in enumerate(ED):
    EO[(a,b)] = ei; EO[(b,a)] = ei
WALK = [[(EO[(f[k],f[(k+1)%4])], f[k], f[(k+1)%4]) for k in range(4)] for f in OFACES]


def segments(vals, level):
    """返回有向线段 (tail_edge, head_edge)。每条被切棱恰好当一次尾、一次头。"""
    ins = [1 if v < level else 0 for v in vals]
    cut = [1 if ins[a] != ins[b] else 0 for a, b in ED]
    segs = []
    for fi, walk in enumerate(WALK):
        cs = [(e, a, b) for (e, a, b) in walk if cut[e]]
        if len(cs) == 2:
            tail = cs[0][0] if ins[cs[0][1]] == 0 else cs[1][0]
            head = cs[1][0] if tail == cs[0][0] else cs[0][0]
            segs.append((tail, head))
        elif len(cs) == 4:                                  # 鞍点面
            f = OFACES[fi]
            center_in = (sum(vals[c] for c in f) / 4) < level
            target = 0 if center_in else 1                  # 与中心状态相反的角
            for k in range(4):
                c = f[k]
                if ins[c] != target:
                    continue
                ea = EO[(f[(k-1) % 4], c)]
                eb = EO[(c, f[(k+1) % 4])]
                segs.append((ea, eb) if ins[c] else (eb, ea))
    return cut, segs


def loops(cut, segs):
    nxt = dict(segs)
    seen, out = set(), []
    for s in nxt:
        if s in seen:
            continue
        loop, cur = [], s
        while cur not in seen:
            seen.add(cur); loop.append(cur); cur = nxt[cur]
        out.append(loop)
    return out


def marching_cubes(field, level=0.0, spacing=(1.0, 1.0, 1.0)):
    nx, ny, nz = field.shape
    sp = np.asarray(spacing, float)
    vids, verts, faces = {}, [], []

    def edge_id(ga, gb):
        key = (ga, gb) if ga < gb else (gb, ga)
        if key not in vids:
            fa, fb = float(field[key[0]]), float(field[key[1]])
            d = fb - fa
            t = 0.5 if abs(d) < 1e-12 else (level - fa) / d
            p = (np.array(key[0], float) + t * (np.array(key[1], float) - np.array(key[0], float))) * sp
            vids[key] = len(verts); verts.append(tuple(p))
        return vids[key]

    for i in range(nx - 1):
        for j in range(ny - 1):
            for k in range(nz - 1):
                vals = [float(field[i+dx, j+dy, k+dz]) for dx, dy, dz in COR]
                cut, segs = segments(vals, level)
                if not segs:
                    continue
                ids = [-1] * 12
                for e in range(12):
                    if cut[e]:
                        a, b = ED[e]
                        ids[e] = edge_id((i+COR[a][0], j+COR[a][1], k+COR[a][2]),
                                         (i+COR[b][0], j+COR[b][1], k+COR[b][2]))
                for lp in loops(cut, segs):
                    for q in range(1, len(lp) - 1):
                        faces.append((ids[lp[0]], ids[lp[q]], ids[lp[q+1]]))
    return np.array(verts).reshape(-1, 3), np.array(faces, dtype=np.int64).reshape(-1, 3)
```

验证方法（本文实测结果）：

紧接上一段代码运行（`marching_cubes` 即上面定义的函数）：

```python
g = np.linspace(-1.5, 1.5, 128)
x, y, z = np.meshgrid(g, g, g, indexing="ij")
v, f = marching_cubes(np.sqrt(x**2 + y**2 + z**2) - 1.0, 0.0, (g[1]-g[0],)*3)

a, b, c = v[f[:,0]], v[f[:,1]], v[f[:,2]]
print("体积", (np.einsum('ij,ij->i', a, np.cross(b, c)) / 6).sum())   # +4.187（解析 4.189）
print("面积", 0.5 * np.linalg.norm(np.cross(b-a, c-a), axis=1).sum())  # 12.564（解析 12.566）
```

## 术语对照

| 英文 | 中文 |
| --- | --- |
| Marching Cubes | 行进立方体 |
| Marching Squares | 行进正方形（二维版本） |
| isosurface | 等值面 |
| scalar field | 标量场 |
| signed distance field (SDF) | 有符号距离场 |
| voxel | 体素 |
| cut edge | 被切棱（表面穿过的棱） |
| ambiguous / saddle case | 歧义构型 / 鞍点构型 |
| asymptotic decider | 渐近判定器 |
| watertight | 水密 |
| manifold | 流形 |
| edge flow | 边流 |
| topology | 拓扑 |
| genus | 亏格 |
| Euler characteristic | 欧拉示性数 |
| winding / orientation | 绕向 |
| differentiable | 可微分 |

## 参考

- Lorensen, W. E.; Cline, H. E. *Marching cubes: A high resolution 3D surface construction algorithm*. ACM SIGGRAPH Computer Graphics 21(4):163–169, 1987.
- US Patent 4,710,876（1985-06-05 申请，1987-12-01 授权，2005-06-05 到期）
- Nielson, G. M.; Hamann, B. *The asymptotic decider: Resolving the ambiguity in marching cubes*. Visualization '91, pp. 83–91.
- Natarajan, B. K. *On generating topologically consistent isosurfaces from polyhedral cells*. 1994.
- Shen, T. et al. *Deep Marching Tetrahedra: a Hybrid Representation for High-Resolution 3D Shape Synthesis*. NeurIPS 2021.
- Shen, T. et al. *Flexible Isosurface Extraction for Gradient-Based Mesh Optimization* (FlexiCubes). ACM TOG (SIGGRAPH) 2023.
- [Wikipedia: Marching cubes](https://en.wikipedia.org/wiki/Marching_cubes)
