---
title: BeeGFS 扩容思路（以单 meta 为例）
---

## 检查是否为新节点，将结果保存到 `isNewNode` 中。
### 如果是新节点 (`isNewNode == true`)，需要做一系列 check：
#### {{embed [[扩容检查]]}}
### 如果是旧节点：
#### 如果运行良好，不需要各种 check。
####
## 前面的步骤进行完后，目的是运行类似 `/opt/beegfs/sbin/beegfs-setup-meta -p /beegfs_meta/ -s 2 -m 192.168.194.147` 的代码。
### 新节点，添加一个 meta，需要获取：
#### `-s` 后的 meta ID 号，该 ID 号应该与其他 meta 都不相同，应获得其他 meta 结点的 meta ID 号。
#### `-S` 后的说明字符串。
#### `-m` 后的 mgmt IP 地址为 “VIP”，你需要配置 hosts 文件使其生效。
### 修改 `/etc/pafs/inst1.d/pafs-meta.conf` 配置文件，指定 log 位置，指定网卡配置。
### 初始化，执行 `/opt/beegfs/sbin/beegfs-setup-meta` 命令。
## systemctl enable meta 服务。判断状态，是否成功。
## 优化系统缓存设置，执行。
## 检查是否将一些启动写到了 `/etc/rc.d/rc.local` 中。如果未写入，则写入。
