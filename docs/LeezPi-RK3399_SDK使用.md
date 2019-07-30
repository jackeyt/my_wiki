# LeezPi-RK3399_SDK使用

[TOC]

# leez-SBC linux-sdk代码获取

 leez-SBC官方 [github 地址](https://github.com/leez-SBC)

## 获取SDK源码

### 1. 获取repo

* https方式

  ```
  git clone https://github.com/leez-SBC/repo.git
  ```

### 2. 初始化仓库

* 创建leez-sdk 目录

  ```
  mkdir leez-sdk && cd leez-sdk
  ```

  **Note: 使用上面步骤获取到的repo**

* 初始化repo仓库

  ```
  ../repo/repo init \
  --repo-url=https://github.com/leez-SBC/repo \
  -u https://github.com/leez-SBC/manifests \
  -b master \
  -m rk3399_linux_release.xml
  ```

### 3. 同步代码

**Note: 使用上面步骤获取到的repo**

```
../repo/repo sync
```
**Note: 上面命令经常会因网络而中断，使用以下脚本可保持同步**

```
#! /bin/bash
../repo/repo sync
while [ $? -ne 0 ]; 
do
        ../repo/repo sync
        done
```

### 4.配置编译环境

安装编译所需工具，确保工具都正确安装：

~~~
sudo apt-get install repo git-core gitk git-gui gcc-arm-linux-gnueabihf u-boot-tools device-tree-compiler \
gcc-aarch64-linux-gnu mtools parted libudev-dev libusb-1.0-0-dev python-linaro-image-tools \
linaro-image-tools autoconf autotools-dev libsigsegv2 m4 intltool libdrm-dev curl sed make \
binutils build-essential gcc g++ bash patch gzip bzip2 perl tar cpio python unzip rsync file bc wget \
libncurses5 libqt4-dev libglib2.0-dev libgtk2.0-dev libglade2-dev cvs git mercurial rsync openssh-client \
subversion asciidoc w3m dblatex graphviz python-matplotlib libc6:i386 libssl-dev texinfo \
liblz4-tool genext2fs lib32stdc++6

~~~

### 5.编译SDK

#### 5.1配置编译文件

选择开发板对应的配置文件。配置文件会链接到`device/rockchip/.BoardConfig.mk`，查看该文件可确认当前所使用的配置文件：

```
./build.sh BoardConfig_debian.mk

# 文件路径在 `device/rockchip/rk3399/BoardConfig_debian.mk`

```

配置文件`device/rockchip/rk3399/BoardConfig_debian.mk`如下：

```
#!/bin/bash

# Target arch
export RK_ARCH=arm64
# Uboot defconfig
export RK_UBOOT_DEFCONFIG=rk3399
# Kernel defconfig
export RK_KERNEL_DEFCONFIG=rockchip_linux_defconfig
# Kernel dts
export RK_KERNEL_DTS=rk3399-leez-linux
# boot image type
export RK_BOOT_IMG=boot.img
# kernel image path
export RK_KERNEL_IMG=kernel/arch/arm64/boot/Image
# parameter for GPT table
export RK_PARAMETER=parameter-buildroot.txt
# Buildroot config
export RK_CFG_BUILDROOT=rockchip_rk3399
# Recovery config
export RK_CFG_RECOVERY=rockchip_rk3399_recovery
# ramboot config
export RK_CFG_RAMBOOT=
# Pcba config
export RK_CFG_PCBA=rockchip_rk3399_pcba
# Build jobs
export RK_JOBS=12
# target chip
export RK_TARGET_PRODUCT=rk3399
# Set rootfs type, including ext2 ext4 squashfs
export RK_ROOTFS_TYPE=ext4
# yocto machine
export RK_YOCTO_MACHINE=rockchip-rk3399-sapphire-excavator
# rootfs image path
export RK_ROOTFS_IMG=rockdev/rootfs.${RK_ROOTFS_TYPE}
# Set oem partition type, including ext2 squashfs
export RK_OEM_FS_TYPE=ext2
# Set userdata partition type, including ext2, fat
export RK_USERDATA_FS_TYPE=ext2
# Set flash type. support <emmc, nand, spi_nand, spi_nor>
export RK_STORAGE_TYPE=emmc
#OEM config
export RK_OEM_DIR=oem_normal
#userdata config
export RK_USERDATA_DIR=userdata_normal
#misc image
export RK_MISC=blank-misc.img
#choose enable distro module
export RK_DISTRO_MODULE=
```


`BoardConfig_debian.mk`文件默认配置为编译 Buildroot 固件，下面对 Buildroot 相关配置进行说明：

~~~
# Buildroot config
export RK_CFG_BUILDROOT=rockchip_rk3399     # Buildroot 根文件系统配置文件

# 文件路径在 `buildroot/configs/rockchip_rk3399_defconfig`

~~~

~~~
# Recovery config
export RK_CFG_RECOVERY=rockchip_rk3399_recovery     # recovery 模式下根文件系统配置文件（可省略）

# 文件路径在 `buildroot/configs/rockchip_rk3399_recovery_defconfig`

~~~

~~~
# rootfs image path
export RK_ROOTFS_IMG=buildroot/output/$RK_CFG_BUILDROOT/images/rootfs.$RK_ROOTFS_TYPE   # Buildroot 根文件系统镜像路径

# 本例中，文件路径在 `buildroot/output/rockchip_rk3399/images/rootfs.ext4`
# 注：该文件路径将在首次编译根文件系统后生成

~~~

执行编译命令时，将会根据`.mk`文件进行编译。

#### 5.2全自动编译

全自动编译会编译并打包固件`update.img`，生成固件目录`rockdev/`：

~~~
./build.sh

~~~

#### 5.3部分编译

*   编译 kernel

~~~
./build.sh kernel

~~~

*   编译 kernel modules

~~~
./build.sh modules

~~~

*   编译 u-boot

~~~
./build.sh uboot

~~~

*   编译 rootfs

1.  编译 Buildroot rootfs

编译 Buildroot 根文件系统，将会在`buildroot/output`生成编译输出目录：

~~~
./build.sh buildroot

# 注：确保作为普通用户编译 Buildroot 根文件系统，避免不必要的错误。编译过程中会自动下载所需软件包，请保持联网状态
~~~

2.  编译 debian9 rootfs

~~~
./build.sh debian

# 注：确保作为普通用户编译 debian9 根文件系统，避免不必要的错误。编译过程中会自动下载所需软件包，请保持联网状态
~~~

3.  编译 debian10 rootfs

~~~
./build.sh distro

# 注：确保作为普通用户编译 debian10 根文件系统，避免不必要的错误。编译过程中会自动下载所需软件包，请保持联网状态
~~~

4.  编译 yocto rootfs

~~~
./build.sh yocto

# 注：确保作为普通用户编译 yocto 根文件系统，避免不必要的错误。编译过程中会自动下载所需软件包，请保持联网状态
~~~


*   编译 recovery

~~~
./build.sh recovery

# 注：确保作为普通用户编译 yocto 根文件系统，避免不必要的错误。编译过程中会自动下载所需软件包，请保持联网状态
~~~

## 5.4固件打包
### 更新链接

为确保`rockdev/`目录下文件链接正确，更新各部分镜像链接：

~~~
./mkfirmware.sh

~~~

### 打包固件

将`rockdev`目录的各部分镜像打包成固件`update.img`：

~~~
./build.sh updateimg

~~~

## 6Buildroot 介绍

### 6.1output 目录

Buildroot 编译输出结果保存在`output`目录，具体目录由配置文件决定，本例保存在`buildroot/output/rockchip_rk3399`目录，后续可以在该目录执行`make`编译根文件系统。

采用全自动编译方式时，默认会生成`buildroot/output/rockchip_rk3399_recovery`目录，这是`recovery`的编译输出目录。

子目录说明：

*   `build/`包含所有的源文件，包括 Buildroot 所需主机工具和选择的包，这个目录包含所有 模块源码。
*   `host/`主机端编译需要的工具包括交叉编译工具。
*   `images/`包含压缩好的根文件系统镜像文件。
*   `staging/`这个目录类似根文件系统的目录结构，包含编译生成的所有头文件和库，以及其他开发文件，不过他们没有裁剪，比较庞大，不适用于目标文件系统。
*   `target/`包含完整的根文件系统，对比`staging/`，它没有开发文件，不包含头文件，二进制文件也经过`strip`处理。

### 6.2自定义 Buildroot

下文将介绍一些自定义 Buildroot 的方法。

#### 6.2.1模块配置

默认编译好的根文件系统不一定满足我们的需求，我们可能需要增加一些第三方包，或者修改包的配置选项，Buildroot 支持图形化方式去做选择配置：

~~~
cd buildroot/output/rockchip_rk3399/

# 进入图形化配置界面，选择所需模块，保存退出
make menuconfig

# 保存到配置文件 'buildroot/configs/rockchip_rk3399_defconfig'
make savedefconfig

#编译 Buildroot 根文件系统
make

~~~

需要了解的是：

*   进行编译时，Buildroot 根据配置，会自动从网络获取相关的软件包，包括一些第三方库，插件，实用工具等，放在dl/目录。
*   软件包会解压在`output/build/`目录下，然后进行编译。
*   如果要修改软件包的源码，可以通过打补丁的方式进行修改，补丁集中放在`package/`目录，Buildroot 会在解压软件包时为其打上相应的补丁。

#### 6.3.2busybox 配置修改

busybox 用于管理系统的命令工具，可按如下方式修改：

~~~
cd buildroot/output/rockchip_rk3399/

# 进入图形化配置界面，选择所需工具，退出保存
make busybox-menuconfig

# 保存到配置文件 `board/rockchip/common/base/busybox.config`
make busybox-update-config

make

~~~

#### 6.3.3文件系统覆盖

文件系统覆盖是指在目标文件系统编译完成后将文件覆盖到文件系统目录。通过这种方式，我们可以简单的添加或修改一些文件：

*   本例覆盖目录`buildroot/board/rockchip/rk3399/fs-overlay-64`
*   公有覆盖目录`buildroot/board/rockchip/common`

例：`buildroot/board/rockchip/rk3399/fs-overlay-64/etc/input-event-daemon.conf`将覆盖文件系统的`/etc/input-event-daemon.conf`文件。

### 7Buildroot 官网

更加详细具体的开发技巧可到 Buildroot 官网学习。

[Buildroot 官网](https://buildroot.org/)

[Buildroot 开发手册](https://buildroot.org/downloads/manual/manual.html)

