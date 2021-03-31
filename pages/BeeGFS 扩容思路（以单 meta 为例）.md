---
title: BeeGFS 扩容思路（以单 meta 为例）
---

## 检查是否为新节点，将结果保存到 `isNewNode` 中。
### 进行 `precheck.sh`，包括（🔨 可能需要自己写。）：
#### rpm: expect, bc.
#### 配置文件是否存在：`hosts.conf`, `auto.conf`. （🔨 不确定扩容之后是否应该添加到这两个文件中。）=> 此处需要我们自己的配置文件 [[add_target.conf]].
#### kernel version.
### 进行 `config_network.sh`，其中有 Meta Network Checking 和 Storage Network Checking。
### 进行 `install_pafs.sh`，包含：
#### 发送 yum repo.
#### yum makecache.
#### install pafs.
#### (不需要安装 drbd.)
### 配置 raid：
#### 是否必须有两个 NVME（或 SSD）组成一个 Raid 1？（⌛ 应该是，需向帅哥求证。目前假定必须严格如此。）
#### 手动指定两块盘。（⌛ 问帅哥是否需要自动。）
#### 检查磁盘类型是否为 NVME 或 SSD，只有检查合格才能继续，否则退出。
##### 如果旧的是 NVME，新的是 SSD，这种情况是否允许？
#### 参考 `Raid 部署` 对 raid 进行设置。
##### ⌛ 难道 `Raid 部署`最后没有做 Raid 吗？为什么还要在 `_config_disk.sh` 中为 meta 和 storage 做 raid？=> 并没有找到代码，应该是没有。
### 进行 `config_disk.sh`，该脚本没有仔细认真学习，先快速浏览。
#### check_storage_type(){...}，检查 是否有正确个数的 nvme 块盘。
#### 获取系统盘信息，查询时以排除。
#### 检查磁盘大小等信息。
#### 使用 `systemctl *.mount` 进行挂载。
### ~~DRBD 和 PCS 只在 mgmt 中进行，与扩容无关。~~
### 前面的步骤进行完后，目的是运行类似 `/opt/beegfs/sbin/beegfs-setup-meta -p /beegfs_meta/ -s 2 -m 192.168.194.147` 的代码。
#### 新节点，添加一个 meta，需要获取：
##### `-s` 后的 meta ID 号，该 ID 号应该与其他 meta 都不相同，应获得其他 meta 结点的 meta ID 号。
##### `-S` 后的说明字符串。
##### `-m` 后的 mgmt IP 地址为 “VIP”，你需要配置 hosts 文件使其生效。
#### 修改 `/etc/pafs/inst1.d/pafs-meta.conf` 配置文件，指定 log 位置，指定网卡配置。
#### 初始化，执行 `/opt/beegfs/sbin/beegfs-setup-meta` 命令。
### systemctl enable meta 服务。判断状态，是否成功。
### 优化系统缓存设置，执行。
### 检查是否将一些启动写到了 `/etc/rc.d/rc.local` 中。如果未写入，则写入。
