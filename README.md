<p align="center">
  <a href="#build-framework">
  <img src=".github/armbian-logo.png" alt="Armbian logo" width="144">
  </a><br>
  <strong>Armbian Linux Build Framework (Z96A RK3568 定制版)</strong><br>
<br>
<a href=https://github.com/armbian/build/graphs/contributors><img alt="GitHub contributors" src="https://img.shields.io/github/contributors-anon/armbian/build?logo=stackexchange&label=Contributors&style=for-the-badge&branch=main&logoColor=white"></a>
<a href=https://github.com/armbian/os><img alt="Artifacts generation" src="https://img.shields.io/github/actions/workflow/status/armbian/os/complete-artifact-matrix-all.yml?logo=dependabot&label=CI%20Build&style=for-the-badge&branch=main&logoColor=white"></a>
<a href=https://github.com/armbian/build/commits/main><img alt="GitHub last commit (branch)" src="https://img.shields.io/github/last-commit/armbian/build/main?logo=github&label=Last%20commit&style=for-the-badge&branch=main&logoColor=white"></a>
</p>

> **📢 项目说明：朝歌 Z96A 云电脑专用定制版**
>
> 本项目 Fork 自 **[momokind/armbian-build](https://github.com/momokind/armbian-build)**（上游为 Armbian 官方构建框架），专为特定的硬件平台进行了源码级的重构与编译修复。
> 
> 💻 **适用设备型号:** 朝歌 Z96A 移动云电脑终端笔记本 (搭载 Rockchip RK3568 芯片)
> 
> 🚀 **本分支相比原版的核心新增与修复特性:**
> * **编译防坑修复:** 彻底解决了 Ubuntu Jammy 基础文件系统构建阶段的 `base-files` 包降级致命报错；强行绕过了已 404 失效的 `150balbes/wifi` (RTL8822BS) 第三方源码拉取环节，确保自动化编译一路绿灯。
> * **多媒体 Zero-Copy 硬件加速:** 内置自动化定制脚本 (`customize-image.sh`)，自动部署 Amazingfate 维护的 Rockchip 多媒体 PPA 源、MPP 视频转译层与底层 `udev` 提权规则。打通了 Chromium 浏览器的 V4L2 与 Rkvdec2 VPU，完美实现流媒体视频的高效低功耗零拷贝硬解。
> * **高阶内核驱动补全 (Kconfig):** 补全了官方镜像中阉割的底层关键模块。原生唤醒 **KVM 硬件虚拟化** (释放 ARMv8 EL2 层性能)、**TUN/TAP** (打通 Wireguard/Tailscale 隧道连接)、**exFAT/NTFS** 外置存储内核级全速读写，以及 **MACVLAN/Bridge/eBPF** (无缝支撑高级 Docker 容器网络拓扑与旁路路由探测)。
> * **系统引导级防砖保护:** 在系统镜像打包末期自动触发 `apt-mark hold` 状态锁死机制，冻结内核 (`linux-image`) 与引导装载器 (`u-boot`) 版本。彻底消除用户日常执行 `apt upgrade` 时，由于拉取到不兼容主线内核导致设备重启变砖的风险。

## What does this project do?

- Builds custom **kernel**, **image** or a **distribution** optimized for low-resource hardware,
- Include filesystem generation, low-level control software, kernel image and **bootloader** compilation,
- Provides a **consistent user experience** by keeping system standards across different platforms.

## Getting started

### Requirements for self hosted

- x86_64 / aarch64 machine
- at least 2GB of memory and ~35GB of disk space for VM, container or bare metal installation
- [Armbian / Ubuntu Jammy 22.04.x](https://github.com/armbian/sdk) for native building or any Docker capable Linux for containerised
- Windows 10/11 with WSL2 subsystem running Ubuntu Jammy 22.04.x
- Superuser rights (configured sudo or root access).
- Make sure your system is up-to-date! Outdated Docker binaries, for example, can cause trouble.

For stable branch use `--branch=v23.11`

```bash
apt-get -y install git
git clone --branch=main https://github.com/wanghaoqd/armbian-build-sunniwell-Z96a
cd armbian-build-sunniwell-Z96

# Z96A 推荐的一键编译指令 (带 Cinnamon 桌面与硬件加速)：
./compile.sh build \
    BOARD=z96a-rk3568-laptop \
    BRANCH=legacy \
    BUILD_DESKTOP=yes \
    BUILD_MINIMAL=no \
    DESKTOP_ENVIRONMENT=cinnamon \
    DESKTOP_ENVIRONMENT_CONFIG_NAME=config_base \
    DESKTOP_APPGROUPS_SELECTED='browsers desktop_tools multimedia internet office' \
    KERNEL_CONFIGURE=no \
    RELEASE=jammy
