# 简介

主菜单的扩展分为两部分：
1. Dolag子菜单
2. 修改现有菜单
其中Dolag子菜单的内容目前都是和此工具集的文档，仓库地址等相关的内容，对工程没有功能性，故不介绍

# 对现有菜单的修改

## 修改File菜单

### Save Incrementally

增加了增量保存的选项，会在$HIP目录下复制此工程并对工程名进行编号或增加编号，以增量保存。
增量保存后不会切换目前工程到保存后的工程。

### Save Backup

备份工程，和Houdini自带的备份功能一致。