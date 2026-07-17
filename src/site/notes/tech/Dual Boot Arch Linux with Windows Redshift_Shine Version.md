---
{"dg-publish":true,"permalink":"/tech/Dual Boot Arch Linux with Windows Redshift_Shine Version/","dg-note-properties":{}}
---

## 前言
我从学习编程开始就一直有一个愿望，那就是拥有一台 Linux 电脑。
然而，从小时候到现在，我用过的三台电脑都被预装了 Windows，分别是 7，10，和 11。然而，由于技术水平不到位，我一直不敢对父母已经配置好的分区乱动。而在两年前，我了解到了 WSL 技术。这让我可以在 Windows 中运行原生 Linux 程序。于是，在接下来的两年中，我借助它完成了对编程题目在本机上更准确的测评结果，并获得了更高的运行效率。
而直到接下来的某一天，我想要在 WSL 中运行 Linux 桌面环境。然而，不幸的是，WSL 对图形界面的支持并不好。基于 X11 Window System 实现的 WSLg 对 Wayland 混成器的支持只能用糟糕来形容，并且各种配置也复杂到了极点。但我并没有放弃，而是一次又一次地在网上查找这类问题的解决方法。而若干天之后，我忍不了了。
“我还是跑一个完整的虚拟机吧。”我这样想道。
接下来，我又开始尝试。从 Hyper-V，到 VirtualBox，再到 VMware Workstation。而切换的原因也很简单，对 GPU 的支持。Hyper-V 的显卡支持在禁用了 RemoteFX 后需要提取显卡配置文件，并且无法实现实体机和虚拟机之间的显卡资源共享，这显然不是我想要的。VirtualBox 做不到对 GPU 性能的充分利用。而最后，VMware Workstation 可以让虚拟机访问最多 8GiB 的显卡内存，而我目前使用的最新电脑的显卡内存为 16GiB。看起来，这已经很好了。
但有一件不幸的事。还记得我之前说过，我放弃 WSL 是因为 Linux 桌面环境的不良好兼容吗？我最喜欢的桌面环境，从 Kali Linux 的 Xfce，到 Ubuntu 的 GNOME，再到 KDE 的 Plasma。最后，是 Hyprland。严格来讲，X11 Window System 和 Wayland Compositor 是两个概念，一般来说，基于前者的是 Desktop Environment，而基于后者的只是 Window Manager。Hyprland 是一个基于 Wayland Protocol 的 Display Server。而 Hyprland，大量地使用了显卡运算机能。其默认终端模拟器为 `kitty`，而 `kitty` 在不指定 `LIBGL_ALWAYS_SOFTWARE=1` 参数的情况下无法直接在 VMware Workstation 中运行，即使开启 3D 加速也是如此。我使用 `terminator` 作为其替代品，但很不幸，不依赖 GPU 的 `terminator` 无法实现 `kitty` 中能够做出的炫酷效果（例如 `cursor trail`）。
同理，还是因为虚拟机对显卡支持的不完全，Hypr Ecosystem 中的一些组件无法在虚拟机中运行，如 `hyprlock`。安装它后，每次我调用 `hyprlock` 命令，桌面就会被替换成那个难绷的画伯特供 Hyprland 标志，就算执行上面的修复指令也无济于事。
实际上，压垮骆驼的最后一批稻草是，VMware SVGA Controller 不支持 60Hz 以上的高刷。经过多方求证，我最终确认，VMware Workstation 无法兼容我笔记本的 90Hz 刷新率。
经过这一系列折磨后，我彻底忍不了了。
我下定决心，要在实体机上安装 Linux。
我这样做并非是没有前提的。经过我一周（对，没错，从我认识 Hyprland 到下定决心装实体机只用了一周）的折腾，对 Arch Linux 维基的查阅，以及重装 Linux $20$ 遍以上（没错，这只是一周的数，而且包括 Kubuntu 两次，Ubuntu 两次，Arch Linux 若干次，还没算上我借用学校电脑装虚拟机的次数，如果将时间线拉长只会更多）我发现我的电脑刚好完美符合安装双系统的一切条件。
1. 这台电脑有足够的存储空间。这台电脑的总存储空间为 2TB，经过一年的使用后仍有大量的空间剩余，我大可以从中挤出 100GiB 来安装 Linux。
2. 这台电脑的集成显卡由 Intel 提供。Intel 提供的开源显卡驱动在 Arch Linux 的官方软件仓库中已经提供，不用大费周章地做复杂的驱动配置。
3. 这台电脑的 EFI 分区有 200MiB，而非预装 Windows 的电脑一般使用的 100MiB，足够将一个经过压缩的 `initramfs` 文件以及 `GRUB` 装进去。
4. 由于父母工作原因，我家中有大量闲置的小容量 U 盘，从中选出一个可以作为启动盘的 U 盘进行覆写并不困难。
5. 阿里云源很快，非常快。
接下来，请静下心来听一个故事。
## 正文
星期四。
结束一天的集训后，我回到家，向母亲问了一个问题。
“我们家里有一些不用的老 U 盘吗？”
母亲思考了一会，答道：“有很多啊。你要干什么？”
随后，我煞有其事地说：“我决定了。我要在我的电脑上安装 Linux。”
母亲沉默不语，回到她的房间，一番查找之后，拿出了一个 U 盘。
“这是你小学用过的 U 盘。已经有很多年没用了，你自己折腾去吧。”
我将 U 盘插进电脑。打开文件管理器，映入眼帘的只有两个文件。一个是学校合唱团的训练音频，另一个是我小学四年级时做的有关中药的 PPT 文件。真是令人怀念啊，那场疫情让我不得不长时间接触电脑，而我对电脑的熟悉速度明显快于我的父母。也许我对编程的兴趣就是在那时埋下伏笔的吧。那时我还是校合唱团成员，那时的我也不会想到，我会成为一个奥信队队队员吧。
结束短暂的回想后，我将那两个文件拷贝到电脑里，将它们从 U 盘中删除。系统中只剩下一个文件夹，`System Volume Information`。我瞥了一眼这个文件夹的修改日期，$2018$ 年，真是够老的啊。
随后，我下载了 Rufus，运行，下拉菜单中只有那个 U 盘。我当然知道写入过程会对 U 盘进行格式化，也提前将所有文件拷贝到了电脑上。但是，按下“开始”按钮后弹出的窗口中的第二个“开始”，我却迟迟不敢按下。我是不是还漏了什么东西？我检查了一下文件夹设置，隐藏文件，显示，系统文件，显示。那个仅存的文件夹的大小是 $64$ KiB，打开 DiskGenius 查看 U 盘占用，$128$ KiB。我确信我不会漏掉任何东西。随后，在经过数次深呼吸后，我按下了第二个“开始”。随着 Rufus 底部的进度条第一次来到右侧，U 盘已经被格式化。经过一段时间后，写入完成。我将 U 盘从电脑中拔出，看看时间，已经是晚上 $11$ 点了。于是我合上电脑，去洗澡，之后就睡觉了。

---
星期五。
中午，我的一个暂时退役的同班同学来到了机房。我把我要在实体机上安装 Arch Linux 的事情告诉了他。听见此事后，他并没有显露出任何程度上的惊讶的神情，而是语重心长地劝阻我道：“我劝你不要这么干。我有个同学和你一样用华为电脑，他装上 Linux 之后无法使用键盘，无奈，只能重新把 Windows 装回去。”听到后，我暗吃一惊。我确实知道国内各大笔记本厂商普遍不支持 Linux，但如此近在眼前的 Linux 崩坏案例我还是第一次听说。但这还是阻挡不了我。因为，Windows 糟糕的运行性能已经让我忍不了了。我还是决定尝试一把。因为，很快，我就没时间搞这些了，我马上就要投入到艰苦的文化课学习中了，因为我还要准备中考。
晚上，我回到家，关闭 Windows 的快速启动和休眠功能，将 U 盘插入电脑中，来到设置，进行高级启动。选中 EFI USB Device 启动项后，我只看到一个提示框。“启动失败”。怎么回事？哦，原来是我忘记关闭安全启动了。经过狂按 F2 进入 BIOS 关闭安全启动后，我重复上面的操作，看到 systemd-boot 的界面出现在眼前时，我知道我已经成功迈出了第一步——至少比那些启动就花屏的好很多。选中 Arch Linux 安装镜像后，我在虚拟机上见过无数次的画面终于在我的实体机上面再次显示出来。接下来，我看见了左边的 `root@archiso`。我可以输入命令了。我打开平板，进入 [Arch Linux 中文维基](https://wiki.archlinuxcn.org/)，进入安装指南页面。我开始按照步骤安装。

---
第一步，我运行了如下命令。
```bash
cat /sys/firmware/efi/fw_platform_size
```
看到返回结果为 $64$ 后，我放心地进行下一步。
接下来，我使用 `iwctl` 连接 Wi-Fi。这个程序非常易用，我很快就成功连上了家里的 Wi-Fi。
连接完成后，我运行了如下命令：
```bash
ping baidu.com
```
看到每隔一秒弹出一行的 `ping` 记录，我安下心来。
接下来，
```bash
timedatectl set-timezone Asia/Shanghai
```
设置完成后，看到
```bash
               Local time: 一 2025-02-21 21:48:56 CST
           Universal time: 一 2025-02-21 13:48:56 UTC
                 RTC time: 一 2025-02-21 13:48:57
                Time zone: Asia/Shanghai (CST, +0800)
System clock synchronized: no
              NTP service: active
          RTC in local TZ: no
```
之后，我知道，时间已经准备好了。
下一步，在使用 `cfdisk` 配置分区时，我曾三次按下取消键。我害怕，害怕我真的按下了确认键，电脑是不是就会坏掉。于是，在分好区后，我立即重启电脑，看到 Windows 正常启动后，我松了一口气。
随后我重新启动 `archiso`。我尝试将我的对应分区 `mount` 到 `/mnt` 目录下，但是并未成功，说是和 `squashfs` 有关。我吓坏了，难道是刚刚分区失败了？于是我认真地查看文档，发现刚刚 `cfdisk` 只是分区，还没格式化。然后，
```bash
mkfs.ext4 /dev/nvme0n1p10
```
之后，我再次 `mount`，成功了。
接下来，挂载启动分区的过程也十分顺利。
随后，
```bash
rm /etc/pacman.d/mirrorlist
vim /etc/pacman.d/mirrorlist
```
随后在里面写上：
```bash
Server = https://mirrors.aliyun.com/archlinux/$repo/os/$arch
```
最后：
```bash
pacman -Sy
```
下载速度就完成了提升。
接着，是最关键的一步：下载内核，基础软件包，**终端文本编辑器**，和**处理器微码**。
```bash
pacstrap -K /mnt base linux linux-firmware intel-ucode vim
```
接着，似乎一切顺利，只是在一处地方出错：
```bash
zstd: error 70: Write error: cannot write block : No space left on device
```
我已经想到这是 `initramfs` 的错误。但是我还是暂时跳过了这部，进行接下来的软件安装。
运行
```bash
genfstab -U /mnt > /mnt/etc/fstab
```
后，我查看 `/mnt/etc/fstab` 文件，看到了 EFI 分区，swap 分区，以及根分区。没有出错。接下来，
```bash
arch-chroot /mnt
```
现在，我已经处在我新安装好的系统中了。
接下来，
```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai/etc/localtime
```
之后，成功设置时区。
```bash
hwclock --systohc
```
启用时间同步。
在 `/etc/locale.gen` 中删除注释
```bash
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
```
后，运行
```bash
locale-gen
```
后，完成了本地化生成。
接下来，我开始着手解决 `mkinitcpio` 的问题。
首先，根据 wiki 的建议，我向 `/etc/mkinitcpio.conf` 写入了以下内容：
```bash
COMPRESSION="zstd"
COMPRESSION_OPTIONS=(-v -5 --long)
```
随后重新运行 `mkinitcpio -P`。
然而，仍然出现空间不足的报错。
接下来，再次更改压缩方式：
```bash
COMPRESSION="xz"
COMPRESSION_OPTIONS=(-9e)
MODULES_DECOMPRESS="yes"
```
仍然空间不够。此时，我突然发现导致空间不足的总是 `/boot/initramfs-linux-fallback.img`。在了解到不生成该文件在**大部分情况下**不影响系统使用后，我直接
```bash
rm /boot/initramfs-linux-fallback.img
```
之后，转到 `/etc/mkinitcpio.d/linux.preset`，将
```bash
#default_image="/boot/initramfs-linux-fallback.img"
```
注释掉，再次运行 `mkinitcpio -P`。
这次，空间终于够了。
随后，我完成密码设置，进行最后一步——GRUB 配置。
运行
```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```
后，没有异常。我退出 `chroot` 环境，解除 `mount`，`shutdown 0`，拔出 U 盘。接下来，是最惊心动魄的时刻。
我颤抖地按下了电脑的电源键。
在 HUAWEI 标志消失后，GRUB 的默认页面显示了出来。
切换到 Windows Boot Manager，Windows 可以正常使用。
再次重新启动，这次选择 Arch Linux，按下回车键，我成功进入了命令行登录界面。键盘没有问题。
随后，我运行
```bash
pacman -Syu
```
想要更新系统。然后我尴尬地发现，我网呢？然后，我再次发现一件尴尬的事情。
**我安装系统的时候忘记下载 `NetworkManager` 了**。
然后，我急忙重启到 Windows，再次使用高级启动。重启后，我发现，woc，为什么回到了 GRUB？此时我才想起来，GRUB 在 Windows Boot Manager 之上。于是，我尝试重启到 Windows，高级重启的界面显示出来了。
随后，我进行挂载，随后立即 `arch-chroot`，安装 `NetworkManager`。再次重启电脑，使用 `nmtui`，我终于成功连接上了自家 Wi-Fi。
下一步是什么？Hyprland，启动！
运行如下指令：
```bash
pacman -S sddm hyprland kitty
```
就可以下载最基本的 Hyprland 组件。**注意**，这里的 `kitty` 我是手动下的，如果只下载 Hyprland 而之后进入桌面后 `SUPER+Q` 没有反应，那么要重新安装一下 `kitty`。
**另外**，Arch Linux 不会自动 `enable` 你通过 `pacman` 下载的软件包内的服务。你需要手动添加，即
```bash
systemctl enable sddm
```
然后，我显然不能使用 root 进行日常使用，我需要创建一个用户。
通过运行
```bash
useradd -m rshine
```
我创建了一个名为 `rshine` 的普通用户。
接下来，运行 `visudo` 命令，将
```bash
%wheel ALL=(ALL:ALL) ALL
```
一行取消注释，再运行
```bash
usermod -aG wheel rshine
```
让我的用户可以使用 `sudo` 命令。
用户已经配置完成，接下来是显卡驱动。
我的电脑使用的是 Intel 的集成显卡，所以我需要下载 Intel 提供的显卡驱动。
运行
```bash
pacman -S mesa vulkan-intel intel-media-driver
```
Intel 的显卡驱动就被安装好了。
啊？你说 $32$ 位程序的驱动？拜托，这里是 Linux，谁还用 $32$ 位程序啊？
接下来，由于我用的是笔记本，所以我还需要调整 ACPI 事件。
在 `/etc/systemd/logind.conf` 中取消
```bash
HandlePowerKey=poweroff
HandlePowerKeyLongPress=ignore
HandleRebootKey=reboot
HandleRebootKeyLongPress=poweroff
HandleSuspendKey=suspend
HandleSuspendKeyLongPress=hibernate
HandleHibernateKey=hibernate
HandleHibernateKeyLongPress=ignore
HandleLidSwitch=suspend
HandleLidSwitchExternalPower=suspend
HandleLidSwitchDocked=ignore
```
的注释后，笔记本合盖就会休眠了。
下一步是声卡。运行
```bash
pacman -S pipewire pavucontrol
```
后，就可以使用声卡了。吗？
最后，我重启了电脑。
sddm 经典界面出现在了我的面前，输入密码，Hyprland 的默认界面就出现在了我的面前。按下 `SUPER+Q`，`kitty` 出现。没错。`kitty` 终于可以在不加参数的情况下正常运行了！
然后，我从某个代码托管网站上下载了我的 dotfile，完成了基础配置。
接下来是日常软件。首先编辑 `/etc/pacman.conf`，增加
```bash
[archlinuxcn]
Server = https://mirrors.aliyun.com/archlinuxcn/$arch
```
后，运行
```bash
sudo pacman -Syu
```
就可以使用 ArchLinuxCN 源了。
接下来，
```
sudo pacman -S yay
```
就可以使用 AUR 了。
然后下载软件。
```
yay -S microsoft-edge-stable-bin visual-studio-code-insiders-bin ttf-firacode ttf-harmonyos-sans obsidian linuxqq
```
上面就是我在 Linux 上最经常用到的东西。真的要装挺久的。
记得给每一个基于 electron 的程序增加 OzonePlatform 和 WaylandIME 参数。
接下来安装 fcitx5：
```bash
sudo pacman -S fcitx5 fcitx5-chinese-addons fcitx5-configtool
```
然后用 `configtool` 添加一下**拼音**就可以使用了。
完成所有基本配置之后，运行
```bash
sudo pacman -S fastfetch
```
来查看系统情况。最后，我得到了这个：
```c++
                  -`                     rshine@AKerMachine
                 .o+`                    ------------------
                `ooo/                    OS: Arch Linux x86_64
               `+oooo:                   Host: MRGFG-XX (M1010)
              `+oooooo:                  Kernel: Linux 6.13.4-arch1-1
              -+oooooo+:                 Uptime: 10 mins
            `/:-:++oooo+:                Packages: 679 (pacman)
           `/++++/+++++++:               Shell: zsh 5.9
          `/++++++++++++++:              Display (CSO1415): 3120x2080 @ 90 Hz (as 1560x1040) in 14" [Built-in]
         `/+++ooooooooooooo/`            WM: Hyprland 0.47.2 (Wayland)
        ./ooosssso++osssssso+`           Cursor: Adwaita
       .oossssso-````/ossssss+`          Terminal: kitty 0.39.1
      -osssssso.      :ssssssso.         Terminal Font: FiraCode-Light (11pt)
     :osssssss/        osssso+++.        CPU: 13th Gen Intel(R) Core(TM) i7-1360P (16) @ 5.00 GHz
    /ossssssss/        +ssssooo/-        GPU: Intel Iris Xe Graphics @ 1.50 GHz [Integrated]
  `/ossssso+/:-        -:/+osssso+-      Memory: 2.65 GiB / 31.07 GiB (9%)
 `+sso+:-`                 `.-/+oso:     Swap: 0 B / 8.00 GiB (0%)
`++:.                           `-/+/    Disk (/): 10.75 GiB / 97.87 GiB (11%) - ext4
.`                                 `/    Local IP (wlp0s20f3): 192.168.3.141/24
                                         Battery (HB5781P1EEW-31T): 66% [Discharging]
                                         Locale: zh_CN.UTF-8
```
然后，我发现了一件不可思议的事情：我的电脑在 Windows 上显示的 CPU 频率是 2.2GHz。但是，来到 Linux 后，变成了 5GHz！！！而我没有对电脑的 CPU 进行任何额外设置！
总之，我现在有了一台能用的 Linux 电脑。
如果还有什么有趣的 Linux Stuff 我可能之后会再写一篇。