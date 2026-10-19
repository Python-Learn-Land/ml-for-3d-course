# 通过 API 运行

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://githubtocolab.com/dylanebert/ml-for-3d-course-notebooks/blob/main/capstone.ipynb)

> 🕹️ 在线交互 Demo：https://dylanebert-LGM-tiny-api.hf.space

要通过 API 运行，就不要去复制 [LGM-tiny](https://huggingface.co/spaces/dylanebert/LGM-tiny) 这个 Space，而是复制 [LGM-tiny-api](https://huggingface.co/spaces/dylanebert/LGM-tiny-api) 这个 Space。它包含如下的 `app.py`。

```python
import gradio as gr
from gradio_client import Client, file


def run(image_url):
    client = Client("dylanebert/LGM-tiny")
    image = file(image_url)
    result = client.predict(image, api_name="/predict")
    return result


demo = gr.Interface(
    fn=run,
    title="LGM Tiny API",
    description="An API wrapper for [LGM Tiny](https://huggingface.co/spaces/dylanebert/LGM-tiny). Intended as a resource for the [ML for 3D Course](https://huggingface.co/learn/ml-for-3d-course).",
    inputs=gr.Textbox(label="Image URL", placeholder="Enter image URL, e.g. https://huggingface.co/datasets/dylanebert/iso3d/resolve/main/jpg@512/a_cat_statue.jpg"),
    outputs=gr.Model3D(),
    examples=[
        "https://huggingface.co/datasets/dylanebert/iso3d/resolve/main/jpg@512/a_cat_statue.jpg"
    ],
    allow_duplication=True,
)
demo.queue().launch()
```

这种方式在 CPU 上就能运行，但它依赖的是原始的 LGM-tiny，而不是你自己定制的模型。不过，如果你的关注点在 UI/UX 或下游任务上，这可能是可以接受的。
