# Paxini 工作目录

> GitHub 仓库只提交源码和说明文档。`.venv/`、`Dex_Quest/recordings/`、
> SDK 安装包、`PXDex/` 和 `PXCap/` 中体积较大的产品资料保留在本机，
> 不纳入版本控制。克隆仓库后，请从官方渠道获取 SDK 安装包。

## 目录

```text
Paxini/
├── .venv/           # Dex_Quest 与 py_demo 共用的 Python 环境
├── Dex_Quest/       # PXDex 手/双相机 + Quest 3 6D pose 同步采集
├── py_demo/         # PXDex 基础测试、运动和相机示例
├── sdk/
│   ├── xrobotoolkit/# XRoboToolKit APK 与 PC Service 安装包
│   └── pxhandsdk/   # PXDex 安装包
├── PXDex/           # PXDex 产品资料、SDK 文档和 URDF
└── PXCap/           # PXCap 产品资料和 URDF
```

其中 `.venv/`、安装包、`PXDex/`、`PXCap/` 和采集输出仅存在于本机，
不会出现在 GitHub 仓库中。

## Python 环境

两个代码目录共用根目录下的 `.venv`：

```bash
cd /home/carrie/my_project/Paxini
source .venv/bin/activate

python -c "import pxhandsdk, xrobotoolkit_sdk, cv2, numpy; print('environment ok')"
```

该环境使用 `--system-site-packages`，因此可以访问通过 DEB 安装的
`pxhandsdk`。`xrobotoolkit-sdk`、OpenCV 和 NumPy 位于共享虚拟环境中。

如果以后需要重建环境：

```bash
cd /home/carrie/my_project/Paxini
/usr/bin/python3 -m venv --system-site-packages .venv
source .venv/bin/activate
python -m pip install --upgrade xrobotoolkit-sdk opencv-python numpy
sudo dpkg -i sdk/pxhandsdk/pxhandsdk_1.0.0.0.ubantu2204_amd64.deb
```

不要通过 pip 安装 `pxhandsdk`，应使用官方提供的 DEB。

## 同步采集

```bash
cd /home/carrie/my_project/Paxini
source .venv/bin/activate
cd Dex_Quest

python collect_hand_pose.py \
  --pose-source controller \
  --pose-side right
```

详细说明见 [Dex_Quest/README.md](Dex_Quest/README.md)。

## PXDex 示例

```bash
cd /home/carrie/my_project/Paxini
source .venv/bin/activate
cd py_demo

python test.py
```

详细说明见 [py_demo/README.md](py_demo/README.md)。
