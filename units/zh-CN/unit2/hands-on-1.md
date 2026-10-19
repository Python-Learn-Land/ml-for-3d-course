# 动手实践（第一部分）

是时候托管你自己的 Demo 了！在这一部分，你将：

1. 在 Hugging Face 上创建一个模型。
2. 把必要的文件上传到模型仓库。

## 创建模型

首先访问 [huggingface.co](https://huggingface.co)，登录或注册一个账号。

然后点击左上角的 `New` -> `Model`。输入模型名称，再点击 `Create model`。

你的模型将会创建在 `https://huggingface.co/<your-username>/<your-model-name>` 这个地址下。

## 访问令牌

出于安全考虑，你需要创建一个访问令牌（access token）才能向模型上传文件。

前往 [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)，创建一个具有 `write`（写入）权限的新访问令牌。

当 git 要求你输入用户名和密码时，用户名填你的用户名，密码填这个访问令牌。

## 上传文件

如果你还没有安装 [git](https://git-scm.com/downloads)，请先下载。

打开终端，然后克隆我现有的 [multi-view-diffusion](https://huggingface.co/dylanebert/multi-view-diffusion) 模型仓库：

```bash
git clone https://huggingface.co/dylanebert/multi-view-diffusion
cd multi-view-diffusion
```

接着，把这些文件上传到你自己的模型仓库：

```bash
git remote set-url origin https://huggingface.co/<your-username>/<your-model-name>
git push
```

记得把 `<your-username>` 和 `<your-model-name>` 替换成你自己的用户名和模型名称。

恭喜！如果一切顺利，你应该能在 `https://huggingface.co/<your-username>/<your-model-name>` 看到你的文件了。
