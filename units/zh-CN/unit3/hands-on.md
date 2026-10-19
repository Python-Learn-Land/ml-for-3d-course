# 动手实践

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://githubtocolab.com/dylanebert/ml-for-3d-course-notebooks/blob/main/gaussian_splatting.ipynb)

本次动手实践的目标是构建一个文本转泼溅体（text-to-splat）的流水线，并以 [LGM](https://huggingface.co/spaces/dylanebert/LGM-mini)（Large Gaussian Model，大型高斯模型）作为示例。

它由生成式 3D 流水线的两个部分组成：

1. 多视图扩散
2. ML 友好的 3D（高斯泼溅）

## 环境准备

打开上方链接的 Colab Notebook。点击 `Runtime` -> `Change runtime type`，并在硬件加速器中选择 `GPU`。

然后先安装必要的依赖：

```python
!pip install -r https://huggingface.co/spaces/dylanebert/LGM-mini/raw/main/requirements.txt
!pip install https://huggingface.co/spaces/dylanebert/LGM-mini/resolve/main/wheel/diff_gaussian_rasterization-0.0.0-cp310-cp310-linux_x86_64.whl
```

和之前一样，如果 Notebook 提示你重启会话，照做即可，然后重新运行该代码块。

## 加载模型

就像在多视图扩散的 Notebook 中一样，加载预训练的多视图扩散模型：

```python
import torch
from diffusers import DiffusionPipeline

image_pipeline = DiffusionPipeline.from_pretrained(
    "dylanebert/multi-view-diffusion",
    custom_pipeline="dylanebert/multi-view-diffusion",
    torch_dtype=torch.float16,
    trust_remote_code=True,
).to("cuda")
```

这是因为多视图扩散是 LGM 流水线的第一步。

接着，加载生成式高斯泼溅模型，也就是 LGM 的核心贡献：

```python
splat_pipeline = DiffusionPipeline.from_pretrained(
    "dylanebert/LGM",
    custom_pipeline="dylanebert/LGM",
    torch_dtype=torch.float16,
    trust_remote_code=True,
).to("cuda")
```

## 加载一张图片

和之前一样，加载著名的猫雕像图片：

```python
import requests
from PIL import Image
from io import BytesIO

image_url = "https://huggingface.co/datasets/dylanebert/3d-arena/resolve/main/inputs/images/a_cat_statue.jpg"
response = requests.get(image_url)
image = Image.open(BytesIO(response.content))
image
```

## 运行流水线

最后，让图片依次通过这两个流水线。输出将是一个泼溅体数据矩阵，可以用 `splat_pipeline.save_ply()` 保存。

```python
import numpy as np
from google.colab import files

input_image = np.array(image, dtype=np.float32) / 255.
multi_view_images = image_pipeline("", input_image, guidance_scale=5, num_inference_steps=30, elevation=0)
```

![多视图的猫](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/multi-view-cats.png)

```python
splat = splat_pipeline(multi_view_images)

output_path = "/tmp/output.ply"
splat_pipeline.save_ply(splat, output_path)
files.download(output_path)
```

这里的 `files.download()` 用于在 Colab 中运行 Notebook 时把文件下载到你的本地机器。如果你是在本地运行 Notebook，可以删掉这一行。

恭喜！你已经运行了 LGM 流水线。

## Gradio Demo

现在，我们来创建一个 Gradio Demo，通过易用的界面端到端地运行这个模型：

```python
import gradio as gr

def run(image):
    input_image = image.astype("float32") / 255.0
    images = image_pipeline("", input_image, guidance_scale=5, num_inference_steps=30, elevation=0)
    splat = splat_pipeline(images)
    output_path = "/tmp/output.ply"
    splat_pipeline.save_ply(splat, output_path)
    return output_path

demo = gr.Interface(fn=run, inputs="image", outputs=gr.Model3D())
demo.launch()
```

这会创建一个 Gradio Demo，以一张图片作为输入，输出一个 3D 泼溅体。
