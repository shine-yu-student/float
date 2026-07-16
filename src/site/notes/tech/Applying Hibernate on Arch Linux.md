---
{"dg-publish":true,"permalink":"/tech/applying-hibernate-on-arch-linux/","dg-note-properties":{}}
---

新的 Linux Stuff 来了。
今天我在电脑上给 initcpio 加了一个 resume 钩子，让它可以 hibernate。这么做的前提是我已经分了一个 4GiB 的 swap 分区加上一个 4GiB 的 zram swap。
然后运行出乎意料地顺利，只是恢复方法有些出人意料——直接关机，然后在开机加载完内存盘之后显示一个检测到 resume 设备，然后东西就被恢复回来了。
非常神奇，不过如果可以隐藏前面的步骤就更好了。
然后突然发现 `uwsm` 这个东西是可以装的，装上之后就成功进入了 `uwsm-managed` 的 Hyprland。
总之就是非常神奇。