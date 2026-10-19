# 环境准备

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://githubtocolab.com/dylanebert/ml-for-3d-course-notebooks/blob/main/multi_view_diffusion.ipynb)

点击上方链接，即可打开包含本单元代码的 Colab Notebook。

## 运行时

多视图扩散，以及本课程的其他单元，都需要 GPU。

在 Notebook 中，点击 `Runtime` -> `Change runtime type`，并在硬件加速器中选择 `GPU`。

![更改运行时类型](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/change-runtime-type.png)

另外，如果你本地有 GPU，也可以在本地机器上运行这个 Notebook。

## 前置准备

要运行 Notebook 中的任意代码块，点击代码块左侧的 `Play` 按钮。

我们首先安装所有必需的依赖：

`pip install -r https://huggingface.co/spaces/dylanebert/multi-view-diffusion/raw/main/requirements.txt`

如果 Notebook 提示你重启会话，照做即可，然后重新运行该代码块。如果一切安装正常，你会在代码块旁边看到一个绿色的对勾。
