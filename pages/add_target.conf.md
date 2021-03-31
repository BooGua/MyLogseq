---
title: add_target.conf
---

## single node/target:
## 
```conf
node=(192.108.34.23)
target_type=(meta)
devices=("/dev/vdb")
```
## multi node/target:
## 
```conf
node=(192.108.34.23 192.108.34.24)
target_type=(meta storage)
devices=("/dev/vdb" "/dev/vdc")
```
