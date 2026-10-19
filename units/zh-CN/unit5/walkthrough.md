# 完整演示

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://githubtocolab.com/dylanebert/ml-for-3d-course-notebooks/blob/main/capstone.ipynb)

本节会一步步带你走完最简单的流程：基于 [LGM](https://huggingface.co/spaces/ashawkey/LGM) 搭建一个 Demo，这也是前面各单元讲过的内容。

## 1. 复制模型

先从复制 [LGM-full](https://huggingface.co/dylanebert/LGM-full) 模型开始。

1. 前往 [Repo duplicator](https://huggingface.co/spaces/huggingface-projects/repo_duplicator)。
2. 在你的[账户设置](https://hf.co/settings/tokens)中生成一个 `write token`（写入令牌），并把它复制到 `token` 字段。
3. 在 `source_repo` 字段中输入 `dylanebert/LGM-full`。
4. 在 `dst_repo` 字段中输入 `{username}/{model_name}`，把 `{username}` 换成你的 Hugging Face 账户用户名，`{model_name}` 换成你喜欢的任意名称，比如 `LGM`。
5. 点击 Submit。

你会看到一个标有「find your repo **here**」的链接，它指向 `http://huggingface.co/{username}/{model_name}`。

![Repo duplicator](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/duplicate.png)

恭喜！现在你可以在 Diffusers 中使用这个模型了，它也会出现在 [hf.co/models](https://huggingface.co/models?pipeline_tag=image-to-3d&sort=trending) 上。

## 2. 复制 Space

前往 [LGM Tiny](https://huggingface.co/spaces/dylanebert/LGM-tiny) Space，它提供了一个简化版的图像转 3D Demo。

1. 点击 `Duplicate Space` 按钮。
2. 选择免费硬件，或者如果可用，选择 ZeroGPU Nvidia A100。
3. 点击右上角的 `Files`。
4. 点击 `app.py` 查看 Demo 源代码。
5. 点击 `edit` 修改代码。
6. 把两处 `dylanebert/LGM-full` 替换成你的模型路径，例如 `{username}/{model_name}`。
7. 点击 `Commit changes`。

> 🕹️ 在线交互 Demo：https://dylanebert-LGM-tiny.hf.space

恭喜！你已经创建了一个 Demo，并满足了本毕业设计项目的最低要求。

## 那我该怎么运行它？

这个 Demo 需要 GPU，所以在免费硬件上跑不起来。不过有很多免费的选择：

1. **在本 Notebook 中运行**：快速验证代码是否可用。
2. **本地运行**：克隆你的 Space 并在本地运行。
3. **社区资助（Community grant）**：在做很酷的东西？可以在 Space 设置中申请社区 GPU 资助。
4. **通过 API 运行**：灵活性较低，但可以在免费硬件上运行。

接下来的几个章节会分别带你了解每一种方案。
