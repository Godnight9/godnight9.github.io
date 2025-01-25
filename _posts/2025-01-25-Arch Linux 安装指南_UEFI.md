---
title: "Arch Linux 安装指南 (UEFI)"
date: 2025-01-25 10:00:00 +0800
categories: [OS, Linux]
tags: [OS, Linux, ArchLinux]
description: "本文将介绍 Arch Linux 在 UEFI 环境下的安装和简单配置。"
---

本文将介绍 Arch Linux 在 UEFI 环境下的安装和简单配置。

## 前期准备

### 0️⃣ 确保硬件达标

以下为 Arch Linux Wiki 所描述之要求：

> Arch Linux 被设计为可以运行在配置为最低 512 MiB 内存的 [x86_64](https://zh.wikipedia.org/wiki/X86-64) 架构的计算机上，但如果是从安装介质启动系统并成功安装在计算机硬盘中，则可能需要更多的内存。基本安装方式将占用小于 2 GiB 的硬盘存储空间。由于安装过程中需要从远程存储库获取软件包，计算机将需要一个有效的互联网连接。

请提前检查你的硬盘类型，SATA 硬盘与 NVMe 硬盘在安装过程中部分步骤有所不同，其他类型的硬盘不在本文讲解范围内。

请确保你的电脑能够正常的从 USB 随身碟引导，如不能，请刻录 DVD 光碟并从 DVD 光碟引导。

笔者在安装时所选择的环境为 UEFI + GPT 环境。Legacy BIOS + MBR 环境在创建引导等步骤有所不同，而且早已过时，此种情况不在本文讲解范围内。

### 1️⃣ 确保网路畅通

在 Arch Linux 的安装过程中，软件包的下载依赖网络连接。离线安装与在线安装有很大不同，此种情况不在本文的讲解范围内。Live CD 内已包含 `iwd` 和 `netctl` 等包，可连接 Wi-Fi 进行系统安装。

> 通过 Live CD 安装系统时无法显示和输入中文，请事先将 Wi-Fi 名称改为英文。
{: .prompt-warning }

### 2️⃣ 取得安装映像

我们可以访问 [Arch Linux 官方网站](https://archlinux.org/download/) 找到官方发布的 BitTorrent 种子和所有提供 Arch Linux 安装映像下载的镜像站。

在中国大陆，推荐通过镜像站下载安装映像。

笔者此处是在 [清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/) 下载的最新安装映像，下面以该镜像站为例讲述如何下载安装映像：

> 如果你未通过上方链接 (通过其他方式) 进入该镜像站，会进入到镜像站首页，你需要在页面内找到 `archlinux`{: .filepath}. 标签并点击进入，之后找到并点击 `iso` {: .filepath}.标签，后续步骤与下文讲述相同。
{: .prompt-tip }

打开上面的链接后，点击 `latest`{: .filepath}. 标签。

![01](/assets/article/202501/arch/01.png)

然后下载以 `archlinux-20XX.XX.XX-x86_64.iso`{: .filepath}. 命名的安装映像。

![02](/assets/article/202501/arch/02.png)

> 此处强烈建议下载最新版的安装映像！Arch Linux 是一个典型的采用滚动发布模式的 Linux 发行版，使用旧版本的安装映像并不能完全得到一个旧版本的 Arch Linux。
> {: .prompt-warning }

### 3️⃣ 制作安装介质

你可以选择使用 [balenaEtcher](https://etcher.balena.io/#download-etcher) / [Ventoy](https://www.ventoy.net/cn/index.html) / [Rufus (仅 Windows)](https://rufus.ie/) 等工具制作安装介质。

> 制作安装介质时会清空随身碟内的所有数据，请谨慎操作，务必提前备份。
{: .prompt-danger }

此处我们以 Windows 下相对简单的 balenaEtcher 为例：

首先，选取下载好的映像文件。

![03](/assets/article/202501/arch/03.png)

![04](/assets/article/202501/arch/04.png)

接下来点击`选择目标磁盘`，在下图所示的页面中勾选你需要制作成安装介质的随身碟，并点击`选定`按钮。

![05](/assets/article/202501/arch/05.png)

最后点击`现在烧录！`，等候片刻。

![06](/assets/article/202501/arch/06.png)

![07](/assets/article/202501/arch/07.png)

当出现以下提示时，随身碟中便成功写入了安装映像。

![08](/assets/article/202501/arch/08.png)

> 使用 balenaEtcher 制作的安装介质在 Windows 资源管理器中不可见，如需将该随身碟作为普通储存器使用，请使用磁盘管理工具 (如 DiskGenius) 重建分区表后方可正常格式化。


### 4️⃣ 调整 UEFI 设置

由于 Arch Linux 及其安装映像没有官方的 Secure Boot (安全启动) 支持，因此我们在安装系统时需要关闭 Secure Boot 才能使系统正常引导。

> Arch Linux 成功安装后可以通过 `sbctl` 工具来配置 Secure Boot，但并不是所有主板都支持。关于该内容本文不做详细说明，请自行了解。
{: .prompt-tip }

不同品牌的主板关闭 Secure Boot 的方式略有不同，但大体上都是以下几步：

进入 UEFI Setup -> 找到 Security 选项卡 -> 将 Secure Boot 开关调整为 Disabled 或 Off 状态

如有不同，请自行查询或联系厂商。

另外，某些 Legacy BIOS 与 UEFI 过渡阶段生产的主板可能会有 Legacy Support 和 CSM 支持，我们需要将主板的 Boot Mode 由 `legacy` 调整为 `UEFI only`，并关闭 CSM 支持。

至此，前期准备工作便完成了，我们可以插入载有安装介质的 USB 随身碟并在 Boot Manager 中选择从随身碟启动，开始着手系统的安装。

## 安装 Arch Linux

> 此处为了便于演示，笔者将在虚拟机环境内进行系统安装。在实体机中的安装过程与在虚拟机中安装相似。
{: .prompt-info }

> 接下来的步骤请**逐步**进行，不要改变顺序和添加步骤。每一步请谨慎的根据教程所示进行，任何一步出错 (包括但不限于输错命令) 都可能造成预期之外的结果。
{: .prompt-danger }

### 0️⃣ 从安装介质引导

插入前期准备步骤中制作好的载有安装介质的随身碟，从随身碟引导后应进入以下界面：

![09](/assets/article/202501/arch/09.png)

我们选择第一项 (`Arch Linux install medium (x86_64, UEFI)`) 后按 <kbd>Enter</kbd> 键进入安装环境，期间会经历数十秒下图所示的跑马效果：

![10](/assets/article/202501/arch/10.png)

引导成功后，会进入如下的界面：

![11](/assets/article/202501/arch/11.png)

这时我们已经成功引导进入安装环境，可以继续进行安装步骤了。

> 此时你会以 root 身份登入控制台，安装环境中预设的 Shell 为 `zsh`。
>
> 如果你的屏幕被输出占满了，可以通过 `clear` 命令清空屏幕。
{: .prompt-tip }

### 1️⃣ 禁用不需要的服务和功能

#### 1.Reflector 服务

以下介绍引用自 Arch Linux Wiki：

> [Reflector](https://xyne.dev/projects/reflector/) 是一个 Python 脚本。它可以从 [Arch Linux Mirror Status](https://archlinux.org/mirrors/status/) 页面获取最新的镜像列表，然后筛选出最新的镜像并按速度排序，最后将结果写入到 `/etc/pacman.d/mirrorlist`{: .filepath}. 文件。

> 在中国大陆，由于特殊的网络环境，该服务可能并不好用。并且该服务会覆盖我们自己添加的源信息，笔者推荐禁用该服务。
{: .prompt-tip }

我们可以通过以下命令在安装环境中禁用该服务：

```zsh
systemctl stop reflector
```

禁用后，我们可以通过以下命令检查该服务的状态：

```zsh
systemctl status reflector
```

![12](/assets/article/202501/arch/12.png)

按下 <kbd>Q</kbd> 键可结束输出，退回到 Shell。

确保该服务为 `failed` 或 `inactive (dead)` 状态，便可进入下一步骤。

> 如果网络条件较好，并且 Reflector 服务可用，可以输入下面的命令让 Reflector 服务自动帮我们添加源：
>
> ```zsh
> reflector --sort rate -c China --save /etc/pacman.d/mirrorlist
> ```
>
> 此处，`--sort rate` 表示以下载速度排序 (如果程序输出 WARNING: failed to rate ... 请去掉该选项)；
>
> `-c` 限制镜像源的地区，`-c China` 表示只使用中国大陆镜像源；
>
> `--save` 表示把结果写入某文件，在 Arch Linux 中，我们需要写入 `pacman` 的镜像列表中。关于该命令的其他选项，请自行了解。
>
> 对于中国大陆用户，建议手动添加源并禁用 Reflector 服务。
>
> {: .prompt-tip }

#### 2.蜂鸣器 (可选)

当使用 <kbd>Tab</kbd> 键无法补全时，或输入区没有字符时仍按下 <kbd>Backspace</kbd> 键时等情况，蜂鸣器会发出刺耳的声音。我们可以通过下面的方式禁用蜂鸣器内核模块：

```zsh
rmmod pcspkr
```

如果需要永久禁用蜂鸣器内核模块，需要创建并编辑`/etc/modprobe.d/blacklist.conf`{: .filepath}.文件：

```zsh
vim /etc/modprobe.d/blacklist.conf
```

并在其中加入以下的内容：

```Config
blacklist pcspkr
```

> 如果不会使用 `vim`，可以直接输入下面的命令来替代上面创建和编辑文件的步骤：
>
> ```zsh
> echo "blacklist pcspkr" >> /etc/modprobe.d/blacklist.conf
> ```
>
{: .prompt-tip }

这样下次启动系统时就不会加载蜂鸣器模块了。

### 2️⃣ 确认是否为  UEFI  模式

我们可以输入以下命令来确认是否处于 UEFI 模式：

```zsh
ls /sys/firmware/efi/efivars
```

如果输出了类似以下的数行长串文本 (efi 变量)，则说明处于 UEFI 模式下，可以继续安装。如果为其他情况请重回 **前期准备** 部分调整 UEFI 设置。

![13](/assets/article/202501/arch/13.png)

另外，我们可以通过以下命令来确认 UEFI 固件的位元：

```zsh
cat /sys/firmware/efi/fw_platform_size
```

如果返回的内容是 `64` 则说明当前 UEFI 固件为 64 位，如果为 `32` 则为 32 位 UEFI 固件。如果文件不存在，则可能系统不处于 UEFI 模式，请重回 **前期准备** 部分调整 UEFI 设置。

### 3️⃣ 连通网络

> Arch Linux 的安装依赖网络，请务必确保网络畅通。
{: .prompt-warning }

下面将介绍无线网络和有线网络的连接方式：

#### 1.无线网络

> 在进行下面的步骤前首先确保你的无线网卡在 UEFI 设置中的开关处于**打开**状态！
{: .prompt-warning }

此处可以使用 `iwtcl` 进行连接：

```zsh
iwctl # 进入交互式命令行
device list # 列出无线网卡设备名，此处假设无线网卡为 wlan0
station wlan0 scan # 扫描网络
station wlan0 get-networks # 列出所有 Wi-Fi 网络
station wlan0 connect [wifi-name] # 连接无线网络，这里无法输入中文。执行命令后再输入密码即可
exit # 连接成功，退出命令行
```

> \# 及后面的内容为注释，输入命令时不需要输入这一部分！
>
{: .prompt-tip }

如果无法连接，请参考以下的方法：

通过以下的命令，检查 Arch Linux 内核是否成功加载无线网卡驱动：

```zsh
lspci -k | grep Network
```

以笔者的笔电为例，此处输出：

```Output
0000:01:00.0 Network controller: Realtek Semiconductor Co., Ltd. RTL8821CE 802.11ac PCIe Wireless Network Adapter
```

证明系统成功加载了无线网卡驱动，如果没有输出，说明系统无法正常加载无线网卡驱动。

如果你的 UEFI 设置中没有无线网卡相关的开关，并且无法连接 Wi-Fi，可以参考下面的方法来开启 Wi-Fi。

首先输入以下命令，查看无线网络是否被禁用：

```zsh
rfkill list
```

输入此命令后，会输出当前网卡的状态，如果 `Wireless LAN` 栏目下的 `Soft blocked` 和 `Hard blocked` 其中有一项或两项为 `yes`，则说明无线网卡被禁用，可以尝试以下的命令来激活无线网卡：

```zsh
ip link set wlan0 up # 上面通过 iwtch 列出的无线网卡设备名，此处假设为 wlan0。
```

如果有类似 `Operation not possible due to RF-kill` 的错误，继续尝试以下的命令来解锁无线网卡：

```zsh
rfkill unblock wifi
```

> 部分无线网卡不兼容 Arch Linux，此种情况请使用有线连接继续系统安装。
{: .prompt-info }

#### 2.有线网络

正常情况下，如果你的电脑通过网线连接到了一个支持 DHCP 并且有互联网连接的路由器，只需等候几秒网络便会自动连接。如果电脑没有有线网络接口，可以使用带有有线网络接口的拓展坞。

#### 3.测试网络是否连通

使用 `ping` 命令，测试网络是否连通：

```zsh
ping -c4 www.baidu.com
```

> 此处通过 `-c` 选项指定了发送的请求数， `-c4` 选项意思是发送 4 个请求。如果不指定该选项，`ping` 将一直发送请求，直到用户按下 <kbd>Ctrl</kbd> + <kbd>C</kbd> 终止程序为止。
{: .prompt-tip }

如果网络已连通，则会有下图所示的输出：

![14](/assets/article/202501/arch/14.png)

此时便可继续进行安装步骤。如果提示超时等错误，请重新检查网络是否连通。

### 4️⃣ 更新系统时钟

此处将使用 `timedatectl` 来确保日期和时间准确，准确的时间对需要验证签名或其他有基于时间的操作的程序 (如 `pacman` 等) 非常重要。

输入下面的命令将系统时间与网络时间同步：

```zsh
timedatectl set-ntp true
```

之后我们可以通过以下命令查看当前系统日期和时间：

```zsh
timedatectl status
```

> 用 `date` 命令可以查看更简单的日期和时间 (输出只有一行)
> {: .prompt-tip }

Arch Linux 默认的时区为 UTC，比北京时间慢 8 小时，无需调整。

### 5️⃣ 添加 pacman 软件仓库镜像源

> Arch Linux Live CD 中预装了多种文本编辑器，比如 `vim` 和 `nano` 等，这些编辑器的使用方法各不相同，本文不做讲解，请自行了解。本文后续均使用 `vim` 编辑器修改或创建文本文件。
{: .prompt-tip }

我们使用 vim 编辑器编辑`/etc/pacman.d/mirrorlist`{: .filepath}. 文件：

```zsh
vim /etc/pacman.d/mirrorlist
```

打开文件后，我们向注释后面的第一行加入镜像源，在中国大陆地区推荐下面四个镜像源：

```Config
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch # 中国科学技术大学开源镜像站
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch # 清华大学开源软件镜像站
Server = https://repo.huaweicloud.com/archlinux/$repo/os/$arch # 华为开源镜像站
Server = http://mirror.lzu.edu.cn/archlinux/$repo/os/$arch # 兰州大学开源镜像站
```

![15](/assets/article/202501/arch/15.png)

添加后保存文件，至此镜像源已添加完毕，可以继续安装步骤了。

### 7️⃣ 创建、格式化、挂载分区

> 分区和格式化操作的命令**具有危险性**，在执行每一个命令之前，确保你足够了解你在做什么，否则请不要执行。
> 
> 在进行分区和格式化操作之前，请提前做好**数据备份**，避免数据丢失造成损失！
{: .prompt-danger }

> 本文以单独安装 Arch Linux 为例，多系统在操作分区时请格外小心，避免误操作导致其他系统的原有数据丢失。
{: .prompt-warning }

#### 1.创建分区

在开始分区操作之前，我们先输入以下命令查看现有分区的情况：

```zsh
lsblk
```

或者

```zsh
fdisk -l
```

在输入 `lsblk` 命令后，可能会输出类似下面的内容：

```Output
root@archiso ~ # lsblk
NAME           MAJ:MIN  RM    SIZE  RO  TYPE  MOUNTPOINT
loop0            7:0     0  688.2M   1  loop  /run/archiso/airootfs  # loop0 是注入到内存的Live CD环境，请忽略
sda              8:0     0    436G   0  disk  # sda 是主 SATA 硬盘
sdb              8:2     0    936G   0  disk  # sdb 是扩展 SATA 硬盘
└─sdb1           8:11    0    936G   0  part  
sdc              8:16    1   59.8G   0  disk  # sdc 在此处是随身碟
├─sdc1           8:17    1   59.7G   0  part
│   └ventoy    254:0     0  861.3M   1  dm    /run/archiso/bootmnt
└─sdc2           8:18    1     32M   0  part
nvme0n1         259:0    0    120G   0  disk  # nvme0n1 是主 NVMe 硬盘
├─nvme0n1p1     259:1    0     16M   0  part  Microsoft reserved partition
└─nvme0n1p2     259:2    0    119G   0  part  Microsoft basic data partition
sr0             11:0     1  755.3M   0  rom   /run/archiso/bootmnt  # sr0 是主光驱
```

这里会列出电脑上所有的存储设备和分区。可以通过 `type` 栏的内容来确定是设备还是分区。

此处建议通过每个硬盘的容量和类型 (SATA 或 NVMe) 来判断你需要安装系统的硬盘。本文假设需要分区和安装 Arch Linux 的硬盘为 `sda`。

> SATA 类型的硬盘一般情况下标识符为 `sdx`，NVMe 类型的硬盘一般情况下标识符为 `nvmexn1`。
{: .prompt-tip }

接下来我们使用相对简单的 `cfdisk` 工具对磁盘进行分区操作：

```zsh
cfdisk /dev/sda # 根据实际情况修改为你需要安装的硬盘标识符。
```

如果是没有创建过分区表的新硬盘，输入上面的命令后会进入如下界面，请选择 `gpt` 并按下 <kbd>Enter</kbd> 键。

![16](/assets/article/202501/arch/16.png)

随后，会进入下面这个界面 (`cfdisk` 主界面)：

![17](/assets/article/202501/arch/17.png)

在此界面中，我们可以通过键盘的方向键 <kbd>↑</kbd> 和 <kbd>↓</kbd> 在分区列表中移动光标，通过方向键 <kbd>←</kbd> 和 <kbd>←</kbd> 在下方的操作列表中移动光标。

接下来我们需要创建三个分区：主分区(`/`{: .filepath}.)、EFI 分区、Swap 分区。

> 如果选择 `ext4` 文件系统，并且硬盘足够大时，可以再创建一个分区用于`/home`{: .filepath}.目录，选择 `Btrfs` 文件系统或 `XFS` 文件系统则不需要，关于 `ext4` / `Btrfs` / `XFS` 等文件系统的更多信息，请自行了解。
{: .prompt-info }

> Swap 分区的大小建议大于运行内存的 60%，或是与运行内存大小相等。系统休眠会用到这个分区。
{: .prompt-tip }

>EFI 分区的大小一般设置为 `256 MB` 或 `512 MB` 即可。
>
>如果硬盘上已经安装了其他系统 (如 Windows)，则应当存在已格式化的 EFI 分区，在下面的分区操作中就不需要再次创建 EFI 分区，后面的格式化操作中也不需要再次格式化该 EFI 分区，因为该分区中可能存有其他系统的引导文件。
{: .prompt-tip }

首先，将光标置于分区列表的 `Free Space` 和操作列表的 `[New]` 上，然后按下键盘的 <kbd>Enter</kbd> 键。

接下来输入你想创建的分区的大小，单位可以是 `G`(`GiB`) / `M`(`MiB`) / `GB` / `MB` 等。

创建好 EFI 分区和 Swap 分区后，余下的空间都可以分给用于安装系统和储存用户数据的主分区。

默认新建的分区类型为 `Linux filesystem`，如下图所示，我们需要将这些分区改为我们所需要的类型。

![18](/assets/article/202501/arch/18.png)

我们可以在分区列表中选择需要更改类型的分区，将操作列表中的光标移动到 `[Type]`，并按下 <kbd>Enter</kbd> 键，进入如下列表：

![19](/assets/article/202501/arch/19.png)

我们需要将 EFI 分区的类型更改为 `EFI System`，将 Swap 分区的类型更改为 `Linux swap`。

主分区保持默认的 `Linux filesystem` 即可，无须修改。

操作完成后，在 `cfdisk` 主界面的操作列表中选择 `[Write]` ，按下 <kbd>Enter</kbd> 键后能看到操作列表被替换为如下提示，输入 `yes` 后再次按下 <kbd>Enter</kbd> 即可将刚才的分区操作写入到硬盘分区表上，如果分区有误，可以按下 <kbd>Esc</kbd> 返回主界面重新调整分区。

![20](/assets/article/202501/arch/20.png)

> 只有将分区操作写入后，分区表才会被更改。请仔细检查你的分区操作是否正确，是否有误操作的情况发生，分区表写入后原有数据会丢失，并有可能无法找回！
{: .prompt-danger }

分区操作完成后，在操作列表中选择 `[Quit]` 并按下 <kbd>Enter</kbd> 键即可退出 `cfdisk` 工具回到 Shell，使用 `lsblk` 工具重新检查分区情况：

![21](/assets/article/202501/arch/21.png)

或下面的命令：

```zsh
fdisk -l
```

![22](/assets/article/202501/arch/22.png)

#### 2.格式化分区

首先我们需要通过以下命令格式化 EFI 分区为 `FAT32` 文件系统：

```zsh
mkfs.vfat -F 32 /dev/sda1 # 这里需要将 sda1 替换为你创建的 EFI 分区的标识符。
```

接着格式化 Swap 分区：

```zsh
mkswap /dev/sda2 # 这里需要将 sda2 替换为你创建的 Swap 分区的标识符。
```

最后格式化主分区：

如果想要将主分区格式化为 `Btrfs` 文件系统，则输入以下命令：

```zsh
mkfs.btrfs /dev/sda3 # 这里需要将 sda3 替换为你创建的主分区的标识符。
```

![23](/assets/article/202501/arch/23.png)

如果想要将主分区格式化为 `ext4` 文件系统，则输入以下命令：

```zsh
mkfs.ext4 /dev/sda3 # 这里需要将 sda3 替换为你创建的主分区的标识符。
```

> 格式化时可以指定 `-L` 选项并加上想要设置的卷标来给分区设置卷标。
{: .prompt-info }

#### 3.创建 Btrfs 子卷 (可选)

在前文分区操作中有提到，选择 `Btrfs` 文件系统不需要创建单独分区给`/home`{: .filepath}.目录，我们将创建两个子卷分别对应 `/`{: .filepath}. 和 `/home`{: .filepath}.，如果选择 `ext4` 文件系统请跳过此部分。

首先需要通过以下命令将格式化后的主分区挂载到 `/mnt`{: .filepath}. 目录下：

```zsh
mount -t btrfs -o compress=zstd /dev/sda3 /mnt # 这里需要将 sda3 替换为你创建的主分区的标识符。
```

> 此处的 `-t` 选项用于指定文件系统的类型，`-o` 选项用于指定挂载参数，其中的 `compress=zstd` 为开启 `zstd` 压缩，并保持默认压缩等级，该选项是可选的。
{: .prompt-info }

> `Btrfs` 支持三种压缩算法：`lzo`、`zlib`、`zstd`，其中最先进的算法为 `zstd`。关于 `Btrfs` 压缩的相关内容，请自行了解。
{: .prompt-tip }

挂载成功后，可以输入下面的命令来检查挂载情况：

```zsh
df -h # -h 选项会将输出结果的单位替换为 G 和 M 这种更具可读性的单位。
```

![24](/assets/article/202501/arch/24.png)

确保挂载正确后，我们通过下面两行命令创建 `Btrfs` 子卷：

```zsh
btrfs subvolume create /mnt/@ # 创建 / 目录子卷
btrfs subvolume create /mnt/@home # 创建 /home 目录子卷
```

![25](/assets/article/202501/arch/25.png)

创建完成后，通过下面的命令检查子卷创建情况：

```zsh
btrfs subvolume list -p /mnt
```

如果输出类似下图，则子卷创建成功：

![26](/assets/article/202501/arch/26.png)

创建完子卷后，需要卸载我们刚才挂载的分区：

```zsh
umount /mnt
```

至此，创建子卷便已完成，可以挂载分区准备安装系统了。

#### 4.挂载分区

在挂载时，需要按照顺序，先从根目录开始挂载，可以使用如下命令挂载 `Btrfs` 子卷：

> 下面的命令需要将 `sda1` `sda2` `sda3` 分别替换为你的 EFI 分区、Swap 分区、主分区的标识符。
{: .prompt-info }

```zsh
swapon /dev/sda2 # 挂载 Swap 分区
mount -t btrfs -o subvol=/@,compress=zstd /dev/sda3 /mnt # 挂载 / 目录
mkdir /mnt/home # 创建 /home 目录
mount -t btrfs -o subvol=/@home,compress=zstd /dev/sda3 /mnt/home # 挂载 /home 目录
mkdir /mnt/efi # 创建 /efi 目录
mount /dev/sda1 /mnt/efi # 挂载 /efi 目录
```

之后输入下面的命令检查挂载情况：

```zsh
df -h
```

![27](/assets/article/202501/arch/27.png)

输入下面的命令检查 Swap 分区的挂载情况：

```zsh
free -h
```

![28](/assets/article/202501/arch/28.png)

如果你选择的是 `ext4` 或者 `xfs` 文件系统，挂载方式会有所不同：

> 下面的命令需要将 `sda1` `sda2` `sda3` 分别替换为你的 EFI 分区、Swap 分区、主分区的标识符。
{: .prompt-info }

```zsh
swapon /dev/sda2
mount /dev/sda3 /mnt
mkdir /mnt/efi
mount /dev/sda1 /mnt/efi
```

如果 `/home`{: .filepath}. 目录有单独分区 (针对 `ext4` 文件系统)，则使用下面的命令：

> 下面的命令需要将 `sda1` `sda2` `sda3` `sda4` 分别替换为你的 EFI 分区、Swap 分区、主分区、`/home`{: .filepath}. 分区的标识符。
{: .prompt-info }

```zsh
swapon /dev/sda2
mount /dev/sda3 /mnt
mkdir /mnt/home
mount /dev/sda4 /mnt/home
mkdir /mnt/efi
mount /dev/sda1 /mnt/efi
```

挂载完成后，便可以开始安装系统了。

### 8️⃣ 安装系统和软件包

> 在安装系统之前，请先通过以下命令检查镜像源是否正确配置：
>
> ```zsh
> cat /etc/pacman.d/mirrorlist
> ```
> 这一步需要网络连接，请确保你的网络畅通。
>
{: .prompt-warning }

#### 1.通过 pacstrap 脚本安装基础软件包：

```zsh
pacstrap /mnt base base-devel linux linux-firmware
```

> 如果使用 `Btrfs` 文件系统，需要额外安装 `btrfs-progs` 包：
>
> ```zsh
> pacstrap /mnt btrfs-progs
> ```
{: .prompt-tip }

此处所安装的软件包说明：
>
>`base`：最基本的系统工具。
>
>`base-devel`：包含了开发环境的工具，在 `AUR` 包的安装过程中是必须用到的。
>
>`linux`：包含了 Linux 内核。对新手而言，暂时不建议安装其他内核。
>
>`linux-firmware`：包含了 Linux 的驱动程序。比如网卡驱动、声卡驱动等。

如果你想安装其他的 Linux 内核，可以看如下的说明：
>
>`linux-zen`：一些内核黑客合作的结果，提供了最适合日常使用的内核，优化了部分性能。
>
>`linux-headers`：即 Linux 内核头文件，是一组包含各种头文件的集合，这些头文件为设备提供了本地编译驱动的能力，`zen` 内核也有提供 `linux-zen-headers` 内核。
>
>`linux-hardened`：注重安全的 Linux 内核，采用一系列加固补丁以缓解内核和用户空间漏洞。和 `linux` 相比，它启用了上游更多的内核加固功能。
>
>`linux-lts`：长期支持版本的 Linux 内核，通常比最新版本稳定，适合长期使用。
>
>此外还有其他内核可供选择，请自行了解。

如果提示 GPG 证书错误，你所使用的安装映像可能不是最新版，可以通过更新 `archlinux-keyring` 解决此问题：

```zsh
pacman -Sy archlinux-keyring
```

#### 2.通过 pacstrap 脚本安装其他必要软件包：

`pacstrap` 脚本除了安装基本软件包，也可以安装其他软件，这样就不用在`chroot`环境下手动安装软件了。

输入以下的命令可以安装一些常用或必要的软件包：

```zsh
pacstrap /mnt networkmanager vim sudo zsh zsh-completions
```

>这里我选择安装 `zsh`，如果你想选择 `bash`，请将 `zsh` 和 `zsh-completions` 两项替换为 `bash-completion`。

#### 3.通过 pacstrap 脚本安装微码：

通过以下命令安装对应芯片制造商的微码：

```zsh
pacstrap /mnt intel-ucode # Intel
pacstrap /mnt amd-ucode # AMD
```

#### 4.通过 pacstrap 脚本安装引导程序：

通过以下命令安装引导程序：

```zsh
pacstrap /mnt grub efibootmgr
```

> `grub`：一个用于加载和管理系统启动的程序，它是 Linux 发行版中最常见的引导程序。
>
> `efibootmgr`：被 `grub` 脚本用来将启动项写入 `NVRAM`。

> 如果你需要使用 `grub` 引导其他已安装的 Windows，你需要通过以下的命令安装 `os-prober` 来检测 Windows 并将其加入 `grub` 启动菜单。
>
> ```zsh
> pacstrap /mnt os-prober
> ```
{: .prompt-tip }

引导程序的配置将在后续的步骤中进行，此处只需要安装即可。

### 9️⃣ 生成 fstab 文件

> `fstab`{: .filepath}. 是 Linux 中重要的配置文件之一，用于定义和管理文件系统的挂载信息，其中记录了系统启动时需要挂载的设备和分区信息。
{: .prompt-info }

我们可以通过 `genfstab` 工具来根据当前的挂载情况自动生成 `fstab`{: .filepath}. 文件，省去了我们手动编辑的过程。

```zsh
genfstab -U /mnt > /mnt/etc/fstab
```

> 其中，`-U` 选项表示使用每个分区的 `UUID` 来标识分区。这是推荐的方式，因为每个设备的 `UUID` 是唯一的，即使硬件重新排列或者分区顺序发生变化，通过 `UUID` 依旧可以确保正确识别分区。
{: .prompt-tip }

在完成生成操作后，我们可以通过下面的命令来检查 `fstab`{: .filepath}. 文件是否正确：

```zsh
cat /mnt/etc/fstab
```

![29](/assets/article/202501/arch/29.png)

### 🔟 进入新系统

经过上面的步骤，我们在安装环境中的工作便已经完成，现在我们可以进入新系统了。

Arch Linux 为我们提供了相比原有的 `chroot` 命令更为简单的 `arch-chroot` 脚本，我们现在仅通过下面的一行命令就可以进入新系统：

```zsh
arch-chroot /mnt /bin/bash
```

我们可以注意到命令行发生了一些变化，如下图所示，此时我们便进入了新系统。

![30](/assets/article/202501/arch/30.png)

接下来，我们将在新系统内完成系统配置。

## 配置新系统

### 1️⃣ 设置时区、主机名、Locale

#### 1.时区

通过以下几个命令，我们可以获取网络时间并将时区设置为 `Shanghai`：

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime # 创建符号链接，设置时区为上海，此处可替换为其他地区。
hwclock --systohc # 将系统时间同步到硬件时间。
timedatectl set-timezone Asia/Shanghai # 设置本地时区。
timedatectl set-ntp true # 启用网络时间同步。
```

> 可以通过下面的命令查看系统中存在的所有时区：
>
> ```bash
> ls /usr/share/zoneinfo/
> ```
>
> 其中有很多子目录，比如要查看亚洲的所有时区，可以通过以下的命令：
>
> ```bash
> ls /usr/share/zoneinfo/Asia/
> ```
{: .prompt-tip }

#### 2.主机名

> 设置主机名不是必要步骤，但为了避免问题，笔者强烈建议设置主机名。
{: .prompt-info }

通过如下命令，即可设置主机名：

```bash
echo 'Arch' > /etc/hostname # 单引号内的内容是你要设置的主机名，请自行替换。
```

然后我们需要编辑 `hosts`{: .filepath}. 文件映射本机 IP 地址和主机名：

```bash
vim /etc/hosts
```

在其中加入下面的内容，其中每行均使用制表符 (<kbd>Tab</kbd>) 分隔并对齐，所有出现的 Arch 请替换为你设置的主机名：

```Config
127.0.0.1	localhost
::1	localhost
127.0.1.1	Arch.localdomain	Arch
```

#### 3.Locale

`Locale` 决定了软件使用的语言、书写习惯和字符集。

我们需要编辑 `/etc/locale.gen`{: .filepath}. 文件：

```bash
vim /etc/locale.gen
```

在其中分别找到下面的两行内容，并去掉这两行前面的 \# 符号保存：

```Config
#en_US.UTF-8 UTF-8
```

```Config
#zh_CN.UTF-8 UTF-8
```

> 如果使用的是 `vim` 编辑器，注释会以不同的颜色显示。去掉 \# 符号后文字颜色应发生变化。 
>
{: .prompt-tip }

通过下面的命令生成 `locale`{: .filepath}. 文件：

```bash
locale-gen
```

执行成功后，我们通过下面的命令向其中添加内容，设置 `LANG` 环境变量：

```bash
echo 'LANG=en_US.UTF-8' > /etc/locale.conf # 此处不推荐设置中文，会导致 tty 乱码。
```

![31](/assets/article/202501/arch/31.png)

至此，`locale`{: .filepath}. 文件已经成功生成。

### 2️⃣ 设置 root 密码和创建用户

#### 1.设置 root 密码

输入下面的命令，在按下 <kbd>Enter</kbd> 键后输入想要设置的 `root` 密码并再次按下 <kbd>Enter</kbd> 键：

```bash
passwd root
```

> 在输入密码时命令行不会有任何显示，这是 Linux 的安全机制，属于正常现象。
{: .prompt-info }

### 2.创建普通用户并给予 sudo 权限

输入下面的命令，我们可以创建一个名为 `arch` 的用户：

```bash
useradd -m -g users -G wheel arch # 此处的 arch 可以替换为你想要的用户名。
```

> 此处 `-m` 选项表示创建用户时同时创建用户主目录，`-g` 选项指定用户所属的基本组为 `users`，`-G` 选项指定用户所属的附加组为 `wheel`。
>
> `wheel` 组的所有成员都可以执行管理任务，为了方便，我们将通过后续步骤放开所有 `wheel` 组成员的 `sudo` 权限，如果我们需要让该用户具备使用 `sudo` 的权限，可以将该用户先加入到 `wheel` 组中。

输入下面的命令给我们新创建的用户更改密码，与设置 `root`密码的步骤是一样的：

```bash
passwd arch # 此处的 arch 可以替换为你想要的用户名。
```

通过下面的命令编辑 `sudoers`{: .filepath}. 文件，我们可以在其中添加可以使用 `sudo` 的用户：

```bash
visudo
```

打开文件后，我们在文件中找到下面的内容：

```Config
# %wheel ALL=(ALL:ALL) ALL
```

去掉这行前面的 \# 符号，保存。

![32](/assets/article/202501/arch/32.png)

经过这一步骤，所有 `wheel` 组中的用户都会拥有使用 `sudo` 的权限，如果你只想给某个用户 `sudo` 权限，可以自行了解方法。

### 3️⃣ 配置引导

在此前的步骤中，我们已经安装了引导程序，接下来我们将配置引导。

> 如果你此前没有安装引导程序，使用下面的命令来安装引导程序：
>
> ```bash
> pacman -S grub efibootmgr
> ```
>
> 如果你是逐步跟随本文安装的系统，请忽略这一步，直接配置引导。
{: .prompt-info }

> 此前我们在安装环境中，使用的是 `pacstrap` 脚本安装软件包。在进入我们安装的新系统后，我们需要用 `pacman` 安装新软件包，这是 Arch Linux 默认的软件包管理器。下面是该工具的部分使用方法：
>
> ```bash
> pacman -S 需要安装的包
> ```
>
> ```bash
> pacman -R 需要删除的包 # 这种方法只会删除需要删除的包，不会删除其依赖的包。
> ```
>
> ```bash
> pacman -U 需要安装的本地包或未包含在官方库中的包
> ```
>
> 该工具还有更多功能，请自行了解。
{: .prompt-tip }

首先，我们输入下面的命令来安装 `grub` 到 EFI 分区：

```bash
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
```

> 此处 `--target=x86_64-efi` 指定目标平台为 `x86_64-efi`，`--efi-directory=/efi` 指定 EFI 分区的路径为 `/efi`，`--bootloader-id=GRUB` 指定引导程序的ID为 GRUB。
{: .prompt-info }

安装完成后应该会有以下输出：

```Output
Installing for x86_64-efi platform.
Installation finished. No error reported.
```

此时 `grub` 已经被安装到 EFI 分区了，我们需要对其进行配置。

使用 `vim` 编辑器编辑 `/etc/default/grub`{: .filepath}. 文件：

```bash
vim /etc/default/grub
```

找到下面的内容：

```Config
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet"
```

将双引号内的内容由 `loglevel=3 quiet` 改为 `loglevel=5 nowatchdog`。

> `nowatchdog` 参数可以显著提高开关机速度，但是并不是所有的 CPU 都支持该参数。详细信息可以前往 Arch Linux Wiki 了解。
>
> `loglevel` 参数后面的数字越大，日志越详细，这里改为 5 是为了便于排错。
{: .prompt-tip }

> 如果需要引导其他系统 (Windows)，并且在此前已经安装了 `os-prober`，需要在该文件中找到下面的内容并去掉该行前面的 \# 符号：
>
> ```Config
> #GRUB_DISABLE_OS_PROBER=false
> ```
>
> 如果找不到该行，则在文件中加入下面这行内容：
>
> ```Config
> GRUB_DISABLE_OS_PROBER=false
> ```
{: .prompt-tip }

最后通过下面的命令生成 `grub` 配置文件：

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

![33](/assets/article/202501/arch/33.png)

至此，`grub` 的配置便完成了。

### 4️⃣ 重启系统

完成所有配置后，我们需要退出 `chroot` 环境：

```bash
exit # 退回到安装环境，不输入此行直接按 Ctrl + D 也可以。
```

之后关闭 `Swap`，卸载分区并重启系统：

```zsh
swapoff /dev/sda2 # 这里需要将 sda2 替换为你创建的 Swap 分区的标识符。
umount -R /mnt # 卸载分区。
reboot # 重启系统。
```

重新引导后，使用 `root` 用户登录：

![34](/assets/article/202501/arch/34.png)

### 5️⃣ 配置网络

通过下面的命令设置 `NetworkManager` 开机自启并立即启动：

```bash
systemctl enable --now NetworkManager
```

测试网络连通性：

```bash
ping -c4 www.baidu.com
```

![35](/assets/article/202501/arch/35.png)

如果使用无线连接，可以在启动 `NetworkManager` 后使用下面的命令：

```bash
nmcli dev wifi list # 显示附近的 Wi-Fi 网络
nmcli dev wifi connect "Wi-Fi名（SSID）" password "网络密码" # 连接指定的无线网络
```

`nmtui` 工具是一个有图形化界面的网络配置工具，关于该工具的使用，可以自行了解。

## 结尾

至此，只包含基础组件的 Arch Linux 便成功的安装在你的电脑上了，尽情享受你的 Arch Linux 吧！

图形化界面的安装和其他系统配置，将后续在其他文章中进行讲解。
