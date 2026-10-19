# 动手实践（第二部分）

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://githubtocolab.com/dylanebert/ml-for-3d-course-notebooks/blob/main/multi_view_diffusion.ipynb)

是时候托管你自己的 Demo 了！在这一部分，你将：

1. 用你自己的模型重新运行 Notebook。
2. 使用 Gradio 创建一个 Demo。
3. （可选）部署你的 Demo。

## 重新运行 Notebook

在 Notebook 中，把模型名称替换成你自己的模型名称：

```python
import torch
from diffusers import DiffusionPipeline

multi_view_diffusion_pipeline = DiffusionPipeline.from_pretrained(
    "<your-user-name>/<your-model-name>",
    custom_pipeline="dylanebert/multi-view-diffusion",
    torch_dtype=torch.float16,
    trust_remote_code=True,
).to("cuda")
```

然后重新运行 Notebook。你应该会得到和之前一样的结果。

![多视图的猫](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/multi-view-cats.png)

## Gradio Demo

现在，我们来创建一个 Gradio Demo：

```python
import gradio as gr

def run(image):
  image = np.array(image, dtype=np.float32) / 255.0
  images = multi_view_diffusion_pipeline("", image, guidance_scale=5, num_inference_steps=30, elevation=0)

  images = [Image.fromarray((img * 255).astype("uint8")) for img in images]

  width, height = images[0].size
  grid_img = Image.new("RGB", (2 * width, 2 * height))

  grid_img.paste(images[0], (0, 0))
  grid_img.paste(images[1], (width, 0))
  grid_img.paste(images[2], (0, height))
  grid_img.paste(images[3], (width, height))

  return grid_img

demo = gr.Interface(fn=run, inputs="image", outputs="image")
demo.launch()
```

`run` 方法把前面的所有代码整合进了一个函数中。然后 `gr.Interface` 方法用这个函数创建一个以 `image` 为输入、以 `image` 为输出的 Demo。

恭喜！你已经为自己的模型创建了一个 Gradio Demo。

## （可选）部署你的 Demo

你大概会希望自己的 Demo 能在 Colab 之外运行。

实现方式有很多种：

### 方案 1：创建一个 Space

前往 [Hugging Face Spaces](https://huggingface.co/spaces) 创建一个新的 Space。选择 `Gradio Space SDK`。在 Space 中新建一个名为 `app.py` 的文件，把 Gradio Demo 的代码粘贴进去。再把 Demo 的 [requirements.txt](https://huggingface.co/spaces/dylanebert/multi-view-diffusion/raw/main/requirements.txt) 也复制到 Space 中。

想查看完整示例，请访问这个 [Space](https://huggingface.co/spaces/dylanebert/multi-view-diffusion)，然后点击右上角的 `Files` 查看源代码。

> 注意：这种方式如果要公开托管，需要 GPU，是要花钱的。不过你可以按照[方案 3](#方案-3在本地运行)的说明，在本地免费运行这个 Demo。

### 方案 2：Gradio Deploy

Gradio 提供了 `gradio deploy` 命令，可以轻松地把你的 Demo 部署到服务器上。

更多细节请查看 [Gradio 官方文档](https://www.gradio.app/guides/sharing-your-app)。

### 方案 3：在本地运行

要在本地运行，只需把代码复制到一个 Python 文件中，然后在你自己的机器上运行即可。

完整的源文件应该长这样：

```python
import gradio as gr
import numpy as np
import torch
from diffusers import DiffusionPipeline
from PIL import Image

multi_view_diffusion_pipeline = DiffusionPipeline.from_pretrained(
    "dylanebert/multi-view-diffusion",
    custom_pipeline="dylanebert/multi-view-diffusion",
    torch_dtype=torch.float16,
    trust_remote_code=True,
).to("cuda")


def run(image):
    image = np.array(image, dtype=np.float32) / 255.0
    images = multi_view_diffusion_pipeline(
        "", image, guidance_scale=5, num_inference_steps=30, elevation=0
    )

    images = [Image.fromarray((img * 255).astype("uint8")) for img in images]

    width, height = images[0].size
    grid_img = Image.new("RGB", (2 * width, 2 * height))

    grid_img.paste(images[0], (0, 0))
    grid_img.paste(images[1], (width, 0))
    grid_img.paste(images[2], (0, height))
    grid_img.paste(images[3], (width, height))

    return grid_img


demo = gr.Interface(fn=run, inputs="image", outputs="image")
demo.launch()
```

要在 Python 虚拟环境中搭建并运行这个 Demo，执行以下命令：

```bash
# 环境搭建
python -m venv venv
source venv/bin/activate
pip install -r https://huggingface.co/spaces/dylanebert/multi-view-diffusion/raw/main/requirements.txt

# 运行
python app.py
```

> 注意：以上是在配备 NVIDIA RTX 4090 的机器上、使用 Python 3.10.12 和 CUDA 12.1 测试通过的。
