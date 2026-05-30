# YOLOv7-tiny TensorFlow2 目标检测

这是一个基于 TensorFlow 2.x 的 YOLOv7-tiny 目标检测工程，包含训练、预测、评估、视频检测、FPS 测试和 ONNX 导出相关代码。

## 目录

1. [项目结构](#项目结构)
2. [环境安装](#环境安装)
3. [快速预测](#快速预测)
4. [训练自己的数据集](#训练自己的数据集)
5. [评估模型](#评估模型)
6. [GitHub/Gitee 三方同步](#githubgitee-三方同步)
7. [常见注意事项](#常见注意事项)

## 项目结构

| 路径 | 说明 |
| :-- | :-- |
| `model_data/` | 类别文件、anchors、字体文件和预训练权重。该目录已纳入版本管理，上传远程库时会一起提交。 |
| `nets/` | YOLOv7-tiny 网络结构和训练损失相关代码。 |
| `utils/` | 数据读取、图像处理、回调、mAP 等工具代码。 |
| `utils_coco/` | COCO 数据集格式相关辅助脚本。 |
| `VOCdevkit/` | VOC 数据集目录模板。 |
| `logs/` | 训练过程中保存的模型权重和日志，默认不提交到远程库。 |
| `img/` | 示例图片。 |

## 环境安装

建议使用 Python 3.7，并按 `requirements.txt` 安装依赖：

```bash
pip install -r requirements.txt
```

主要依赖版本：

```text
tensorflow-gpu==2.2.0
h5py==2.10.0
numpy==1.18.4
opencv_python==4.2.0.34
Pillow==8.2.0
```

如果使用 CPU 环境，可以根据本机情况将 TensorFlow 依赖调整为 CPU 版本。

## 快速预测

默认预测配置位于 `yolo.py`：

```python
"model_path": "model_data/yolov7_tiny_weights.h5"
"classes_path": "model_data/coco_classes.txt"
"anchors_path": "model_data/yolo_anchors.txt"
```

运行预测脚本：

```bash
python predict.py
```

在交互输入中填写示例图片路径：

```text
img/street.jpg
```

如需检测自己的图片，输入对应图片路径即可。视频、摄像头、FPS、批量目录预测、热力图和 ONNX 导出等模式可以在 `predict.py` 顶部的 `mode` 参数中切换。

## 训练自己的数据集

本工程默认使用 VOC 格式数据集。

1. 将标注文件放入：

```text
VOCdevkit/VOC2007/Annotations/
```

2. 将图片文件放入：

```text
VOCdevkit/VOC2007/JPEGImages/
```

3. 修改类别文件。

可以直接编辑 `model_data/voc_classes.txt`，也可以新建自己的类别文件，例如 `model_data/cls_classes.txt`：

```text
cat
dog
person
```

4. 修改 `voc_annotation.py` 中的 `classes_path`，确保它指向训练使用的类别文件。

5. 运行数据集划分脚本：

```bash
python voc_annotation.py
```

6. 修改 `train.py` 中的关键参数：

```python
classes_path = "model_data/voc_classes.txt"
anchors_path = "model_data/yolo_anchors.txt"
model_path = "model_data/yolov7_tiny_weights.h5"
```

如果要从零开始训练，可以将 `model_path` 设置为空字符串。

7. 开始训练：

```bash
python train.py
```

训练产生的权重默认保存在 `logs/` 目录。如果需要把某个训练好的权重也上传到远程库，请将它复制到 `model_data/` 后再提交。

## 评估模型

评估 VOC 数据集 mAP 前，先确认 `get_map.py` 和 `yolo.py` 中的类别、权重路径一致：

```python
classes_path = "model_data/voc_classes.txt"
```

然后运行：

```bash
python get_map.py
```

评估结果会输出到 `map_out/`，该目录默认不提交到远程库。

## GitHub/Gitee 三方同步

同步脚本是 `github_gitee_sync.py`，用于补齐 GitHub 仓库并把本地、GitHub、Gitee 三端同步到同一分支。

推荐直接运行菜单：

```bash
python github_gitee_sync.py
```

菜单功能：

- `1`：创建/补齐 GitHub 公开仓库。当前目录必须已经是本地 Git 仓库；如果缺少 GitHub `origin`，脚本会按当前文件夹名创建公开 GitHub 仓库并添加远端。
- 如果 GitHub 网页仓库已经删除，但本地 `origin` 仍保留旧地址，菜单功能 `1` 会提示是否改成当前文件夹名对应的新公开库。
- `2`：三方同步本地、GitHub、Gitee。同步前要求工作区干净，脚本不会自动提交未提交改动。
- `0`：退出。

如果要在非交互环境中运行，可以直接指定功能：

```bash
python github_gitee_sync.py --ensure-github
python github_gitee_sync.py --sync
```

首次自动创建 GitHub 仓库前，推荐先登录 GitHub CLI：

```powershell
gh auth login
```

如果已经登录，脚本会自动复用 `gh auth token`，不需要手动设置环境变量。也可以手动设置 GitHub token：

```powershell
$env:GITHUB_TOKEN="your-github-token"
```

如果要自动创建或检查 Gitee 仓库，还需要设置：

```powershell
$env:GITEE_ACCESS_TOKEN="your-gitee-token"
```

常用检查命令：

```bash
python github_gitee_sync.py --dry-run --ensure-github
python github_gitee_sync.py --dry-run --sync
```

注意事项：

- GitHub 仓库默认公开，名称默认严格使用当前文件夹名；只有传 `--github-private` 才创建私有库。
- 如果已有 `origin` 但不是 GitHub 地址，脚本默认停止；确认要改远端时再传 `--fix-remote`。
- 非交互运行 `--ensure-github` 时，如果旧 GitHub 仓库已删除且仓库名不同，默认仍会停止；确认要直接替换远端时追加 `--fix-remote`。
- 三方同步时，Gitee 仓库名会跟随最终 GitHub 仓库名；如果本地 `gitee` 远端还是旧仓库名，脚本会自动改成同名目标。
- 如果同步时提示工作区不干净，请先手动检查并提交或暂存改动，再重新运行同步。
- 如果发生合并冲突，请手动解决冲突、提交后再重新运行 `python github_gitee_sync.py --sync`。

## 常见注意事项

- `train.py`、`voc_annotation.py`、`get_map.py` 和 `yolo.py` 中的 `classes_path` 应保持一致。
- 修改类别数量后，旧权重可能出现 shape 不匹配，这是正常现象；可以使用预训练权重跳过不匹配层，或从零开始训练。
- `model_data/` 已经从忽略列表中移除，当前目录中的 `.h5`、`.txt`、`.ttf` 文件都会上传远程库。
- GitHub 单个文件大小限制为 100MB。当前 `model_data` 内权重文件未超过限制，可以直接提交。如果后续权重超过限制，建议使用 Git LFS 或 GitHub Release。
- `logs/`、`map_out/`、数据集目录和缓存目录通常较大，默认仍然不提交。
