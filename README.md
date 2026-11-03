# Paxini 工作目录

> GitHub 仓库只提交源码和说明文档。SDK 安装包作为
> [`sdk-v1.0.0` Release](https://github.com/Dongqiao6/Paxini/releases/tag/sdk-v1.0.0)
> 的附件提供。`.venv/`、`Dex_Quest/recordings/`、`PXDex/` 和 `PXCap/`
> 保留在本机，不纳入版本控制。

## 目录

```text
Paxini/
├── .venv/           # Dex_Quest 与 py_demo 共用的 Python 环境
├── Dex_Quest/       # PXDex 手/双相机 + Quest 3 6D pose 同步采集
├── py_demo/         # PXDex 基础测试、运动和相机示例
├── sdk/
│   ├── xrobotoolkit/# XRoboToolKit APK 与 PC Service 安装包
│   └── pxhandsdk/   # PXDex SDK 安装包
├── PXDex/           # PXDex 产品资料、SDK 文档和 URDF
└── PXCap/           # PXCap 产品资料和 URDF
```

`sdk/` 中的安装包从 GitHub Release 下载到对应子目录后使用，不会直接提交
到 Git 仓库。`.venv/`、`PXDex/`、`PXCap/` 和采集输出也仅保留在本机。

## SDK 安装包

三个安装包统一发布在
[`sdk-v1.0.0` Release](https://github.com/Dongqiao6/Paxini/releases/tag/sdk-v1.0.0)。

| 安装包 | 用途 | 仓库内放置位置 | 使用该包的程序 |
|---|---|---|---|
| [pxhandsdk_1.0.0.0.ubantu2204_amd64.deb](https://github.com/Dongqiao6/Paxini/releases/download/sdk-v1.0.0/pxhandsdk_1.0.0.0.ubantu2204_amd64.deb) | PXDex III 的 C++/Python 驱动和系统级 Python 包 | `sdk/pxhandsdk/` | `py_demo/`、`Dex_Quest/` |
| [XRoboToolkit_PC_Service_1.0.0_ubuntu_22.04_amd64.deb](https://github.com/Dongqiao6/Paxini/releases/download/sdk-v1.0.0/XRoboToolkit_PC_Service_1.0.0_ubuntu_22.04_amd64.deb) | 在 Ubuntu PC 上接收 Quest 跟踪数据，供 Python SDK 查询 | `sdk/xrobotoolkit/` | `Dex_Quest/quest3.py`、`Dex_Quest/collect_hand_pose.py` |
| [XRoboToolkit-Quest-1.0.1.apk](https://github.com/Dongqiao6/Paxini/releases/download/sdk-v1.0.0/XRoboToolkit-Quest-1.0.1.apk) | 安装在 Quest 3 中，采集并发送手柄和手部 6D pose | `sdk/xrobotoolkit/` | `Dex_Quest/collect_hand_pose.py` 的 Quest 数据源 |

下载后先校验文件完整性：

```bash
cd /home/carrie/my_project/Paxini
sha256sum -c sdk/SHA256SUMS
```

在 Ubuntu 22.04 PC 上安装两个 DEB。`apt` 会自动处理系统依赖：

```bash
sudo apt install \
  ./sdk/xrobotoolkit/XRoboToolkit_PC_Service_1.0.0_ubuntu_22.04_amd64.deb \
  ./sdk/pxhandsdk/pxhandsdk_1.0.0.0.ubantu2204_amd64.deb

sudo /usr/share/pxhandsdk/scripts/setup-serial-access.sh
```

`pxhandsdk` 必须安装到系统 Python，不能从 PyPI 安装同名包。安装串口权限脚本
后需要重新登录，`py_demo/` 才能访问 `/dev/ttyUSB*`。

通过 USB ADB 将 Quest APK 安装到头显：

```bash
adb devices
adb install -r sdk/xrobotoolkit/XRoboToolkit-Quest-1.0.1.apk
```

`Dex_Quest/` 还需要通过 pip 安装 Python 侧的 `xrobotoolkit-sdk`；它是代码依赖，
与上面的 PC Service 安装包不同。

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
