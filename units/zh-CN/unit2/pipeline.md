# 流水线

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://githubtocolab.com/dylanebert/ml-for-3d-course-notebooks/blob/main/multi_view_diffusion.ipynb)

在我们的例子中，我们将使用一个预训练好的流水线：

```python
import torch
from diffusers import DiffusionPipeline

multi_view_diffusion_pipeline = DiffusionPipeline.from_pretrained(
    "dylanebert/multi-view-diffusion",
    custom_pipeline="dylanebert/multi-view-diffusion",
    torch_dtype=torch.float16,
    trust_remote_code=True,
).to("cuda")
```

模型名称是 [dylanebert/multi-view-diffusion](https://huggingface.co/dylanebert/multi-view-diffusion)，它是 [ashawkey/mvdream-sd2.1-diffusers](https://huggingface.co/ashawkey/mvdream-sd2.1-diffusers) 的一个镜像。对于任何预训练模型，你都能在 Hugging Face Hub 上通过 `https://huggingface.co/<model-name>` 找到它的模型卡（model card），其中包含该模型的相关信息。

在我们的例子中，还需要额外加载自定义流水线（同样位于 `dylanebert/multi-view-diffusion`）才能使用这个模型。这是因为 diffusers 官方并不支持 3D。所以为了本课程的讲解，我把这个模型封装进了一个自定义流水线，让你可以用它来完成 3D 任务。

## 加载一张图片

```python
import requests
from PIL import Image
from io import BytesIO


image_url = "https://huggingface.co/datasets/dylanebert/3d-arena/resolve/main/inputs/images/a_cat_statue.jpg"
response = requests.get(image_url)
image = Image.open(BytesIO(response.content))
image
```

![猫雕像](https://huggingface.co/datasets/dylanebert/3d-arena/resolve/main/inputs/images/a_cat_statue.jpg)

通过这段代码，我们加载并展示了著名的[猫雕像（Cat Statue）](https://huggingface.co/datasets/dylanebert/3d-arena/resolve/main/inputs/images/a_cat_statue.jpg)，它常被用于图像转 3D（image-to-3D）的演示。

## 运行流水线

```python
import numpy as np

def create_image_grid(images):
    images = [Image.fromarray((img * 255).astype("uint8")) for img in images]

    width, height = images[0].size
    grid_img = Image.new("RGB", (2 * width, 2 * height))

    grid_img.paste(images[0], (0, 0))
    grid_img.paste(images[1], (width, 0))
    grid_img.paste(images[2], (0, height))
    grid_img.paste(images[3], (width, height))

    return grid_img

image = np.array(image, dtype=np.float32) / 255.0
images = multi_view_diffusion_pipeline("", image, guidance_scale=5, num_inference_steps=30, elevation=0)

create_image_grid(images)
```

最后，我们在这张图片上运行流水线。

`create_image_grid` 函数并不属于流水线本身，它只是一个用来把结果以网格形式展示的辅助函数。

要运行流水线，我们只需先把图片转换成归一化的 numpy 数组：

`image = np.array(image, dtype=np.float32) / 255.0`

然后把它传给流水线：

`images = multi_view_diffusion_pipeline("", image, guidance_scale=5, num_inference_steps=30, elevation=0)`

其中 `guidance_scale`、`num_inference_steps` 和 `elevation` 这几个参数是多视图扩散模型特有的。

![多视图的猫](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/multi-view-cats.png)

## 小结

恭喜！你已经运行了一个多视图扩散流水线。

那么，接下来要不要把它托管成你自己的 Demo？
