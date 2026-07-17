---
{"dg-publish":true,"permalink":"/tech/在 Arch Linux 使用蓝牙连接华为鼠标/","dg-note-properties":{}}
---

非常简单。如果你也想自己摸索出这个步骤，你只需要认真阅读鼠标包装盒。
型号：HUAWEI Mouse WYN
先决条件：
```bash
sudo pacman -S bluez bluez-utils blueman
```
1. 长按鼠标背部按钮使鼠标进入配对状态。
2. 打开 `blueman`，按下左上角的查找键。
3. 在找到的华为鼠标栏目上右键，信任设备并连接。

恭喜你，你拥有了一个可以在 Arch Linux 上用蓝牙方式使用的华为鼠标。