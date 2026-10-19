# 本地运行

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://githubtocolab.com/dylanebert/ml-for-3d-course-notebooks/blob/main/capstone.ipynb)

下面的说明是在 WSL2 Ubuntu 22.04 + RTX 4090 环境下测试通过的。具体步骤会因你的环境配置不同而有差异，也可能无法正常运行。

1. 如果尚未安装，先安装 `git`、`python 3.10` 和 `cuda`。
2. 打开你的终端。
3. 克隆你的 Space 仓库，把下面的 URL 替换成你自己的 Space URL。

```bash
git clone https://huggingface.co/spaces/dylanebert/LGM-tiny
```

4. 进入该 Space 文件夹。

```bash
cd LGM-tiny
```

5. 创建一个虚拟环境并安装必要的依赖。

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

6. 运行 Demo。

```bash
python app.py
```
