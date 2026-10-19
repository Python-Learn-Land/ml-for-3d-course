# 在 Notebook 中运行

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://githubtocolab.com/dylanebert/ml-for-3d-course-notebooks/blob/main/capstone.ipynb)

第一个选项是在 Colab Notebook 中运行。这是快速验证代码是否可用的最简便方式。

## 环境准备

1. 点击上方的「Open in Colab」按钮。
2. 把运行时类型改为 GPU。
3. 向下滚动到 `Run in this notebook` 部分。

## 运行 Demo

先安装依赖。

```bash
!pip install -r https://huggingface.co/spaces/dylanebert/LGM-tiny/raw/main/requirements.txt
```

然后运行 Demo 代码。这与 Space 中 `app.py` 的代码完全一致。为了确认你的模型工作正常，请把两处 `dylanebert/LGM-full` 都替换成你的 `{username}/{model_name}`，然后运行代码。

```python
import shlex
import subprocess

import gradio as gr
import numpy as np
import torch
from diffusers import DiffusionPipeline

subprocess.run(
    shlex.split(
        "pip install https://huggingface.co/spaces/dylanebert/LGM-mini/resolve/main/wheel/diff_gaussian_rasterization-0.0.0-cp310-cp310-linux_x86_64.whl"
    )
)

pipeline = DiffusionPipeline.from_pretrained(
    "dylanebert/LGM-full",
    custom_pipeline="dylanebert/LGM-full",
    torch_dtype=torch.float16,
    trust_remote_code=True,
).to("cuda")


def run(image):
    input_image = np.array(image, dtype=np.float32) / 255.0
    splat = pipeline(
        "", input_image, guidance_scale=5, num_inference_steps=30, elevation=0
    )
    splat_file = "/tmp/output.ply"
    pipeline.save_ply(splat, splat_file)
    return splat_file


demo = gr.Interface(
    fn=run,
    title="LGM Tiny",
    description="An extremely simplified version of [LGM](https://huggingface.co/ashawkey/LGM). Intended as resource for the [ML for 3D Course](https://huggingface.co/learn/ml-for-3d-course/unit0/introduction).",
    inputs="image",
    outputs=gr.Model3D(),
    examples=[
        "https://huggingface.co/datasets/dylanebert/iso3d/resolve/main/jpg@512/a_cat_statue.jpg"
    ],
    cache_examples=True,
    allow_duplication=True,
)
demo.queue().launch()
```

## Demo 代码拆解

我们来把这段 Demo 代码拆开看一看。

### 导入依赖

导入所需的库。

```python
import shlex
import subprocess

import gradio as gr
import numpy as np
import spaces
import torch
from diffusers import DiffusionPipeline
```

### 安装 diff-gaussian-rasterization

对于 LGM 的高斯泼溅步骤，我们需要安装一个自定义的 wheel 包。这是一种变通做法，目的是让这个 Space 能在 [ZeroGPU](https://huggingface.co/zero-gpu-explorers) 上运行。

```python
subprocess.run(
    shlex.split(
        "pip install https://huggingface.co/spaces/dylanebert/LGM-mini/resolve/main/wheel/diff_gaussian_rasterization-0.0.0-cp310-cp310-linux_x86_64.whl"
    )
)
```

### 构建流水线

构建 [LGM](https://huggingface.co/dylanebert/LGM-full) 流水线。请把 `dylanebert/LGM-full` 替换成你的 `{username}/{model_name}`。

```python
pipeline = DiffusionPipeline.from_pretrained(
    "dylanebert/LGM-full",
    custom_pipeline="dylanebert/LGM-full",
    torch_dtype=torch.float16,
    trust_remote_code=True,
).to("cuda")
```

### 定义 run 函数

定义 run 函数，它接收一张图片并返回一个 ply 文件。

1. 把图片转换成 numpy 数组，并归一化到 [0, 1]。
2. 使用默认参数运行流水线。
3. 把 ply 文件保存到 `/tmp/output.ply`。
4. 返回该 ply 文件。

```python
@spaces.GPU
def run(image):
    input_image = np.array(image, dtype=np.float32) / 255.0
    splat = pipeline(
        "", input_image, guidance_scale=5, num_inference_steps=30, elevation=0
    )
    splat_file = "/tmp/output.ply"
    pipeline.save_ply(splat, splat_file)
    return splat_file
```

### 创建 Demo

使用 [Gradio](https://www.gradio.app/guides/quickstart) 创建 Demo，它会帮我们处理 UI 部分。

```python
demo = gr.Interface(
    fn=run,
    title="LGM Tiny",
    description="An extremely simplified version of [LGM](https://huggingface.co/ashawkey/LGM). Intended as resource for the [ML for 3D Course](https://huggingface.co/learn/ml-for-3d-course/unit0/introduction).",
    inputs="image",
    outputs=gr.Model3D(),
    examples=[
        "https://huggingface.co/datasets/dylanebert/iso3d/resolve/main/jpg@512/a_cat_statue.jpg"
    ],
    cache_examples=True,
    allow_duplication=True,
)
demo.queue().launch()
```

![LGM Tiny Demo](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/lgm-tiny.png)
