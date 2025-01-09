---
  title: awdl & airDrop
  pubDate: 2025-01-08
  categories: ["杂七杂八"]
  description: ''
---

之前用macbook打游戏总是会时不时的自动断网，然后重连，搞得自己很红温。刚开始居然认为是蓝牙和wifi冲突了，被当时的自己蠢到了。
调查了一下发现是AWDL(Apple Wireless Direct Link)的问题，AWDL会时不时导致Wi-Fi的重连，也就是问题所在。

> Macbooks use a Wi-Fi interface called AWDL (Apple Wireless Direct Link) for features like AirDrop and AirPlay. Having AWDL on may cause your Wi-Fi connection to periodically reset.

这篇帖子写的非常详细：https://www.meter.com/mac-osx-awdl-psa, 不过Ventura 13.1就修复这个bug了.

但如果是13.1之前关的AMDL，更新系统之后似乎AMDL不会重启，要手动打开才行。

### 如何关AMDL
(关闭AMDL后airdrop会失效)

用这个命令把 AWDL 关掉就可以正常游戏了，系统会提示要输入密码：

```bash
sudo ifconfig awdl0 down
```

用下面这个脚本还可以一直关awdl。

```bash

#!/usr/bin/env bash

set -euo pipefail

while true; do
   if ifconfig awdl0 |grep -q "<UP"; then
       (set -x; ifconfig awdl0 down)
   fi
   sleep 1
done
```

### 如何重启
不过，我后来忘记了自己一直在运行这个脚本，结果发现 AirDrop 功能失效了……

于是，我又花了不少时间重启 AWDL：
```command
sudo ifconfig awdl0 up
```

执行完毕后，检查 AWDL 状态：
```command
ifconfig awdl0
```

在输出中找到 `status` 项。如果 `status` 仍显示为 `inactive`，可以尝试重新初始化 Mac 的网络堆栈：
```command
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
sudo ifconfig awdl0 up
```

如果还是不行，那就重启吧，哥们当时重启后就解决了。或许是mac比较聪明，看我一直关着awdl，他就默认不开了。
