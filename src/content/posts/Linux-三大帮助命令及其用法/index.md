---
title: Linux 三大帮助命令及其用法
published: 2023-08-30 21:55:12
tags: [常用命令,帮助命令]
category: Linux操作系统
draft: false
---

[详解 Linux 帮助命令—知乎](https://zhuanlan.zhihu.com/p/105096446)

### help

他只能用于内建命令（例如cd），不可用于外建命令（例如mv）

扩展：

​	内建命令是shlle程序的一部分，而外建命令是额外安装获得的

​	使用 `type <cmd> `判断外建与内建

`<cmd> --help`用于查询外建命令的帮助

### man

比help更见详细，像是一个字典，大部分命令支持

```shell
man <cmd>
```

```shell
man 1 man
```

其中数字表示节号，含义如下

- 1：用户命令
- 2：系统调用
- 3：C库函数
- 4：设备和特殊文件
- 5：文件格式和约定
- 6：游戏和演示
- 7：杂项
- 8：系统管理命令

### info

比man的内容更多，使用节点形式支撑页面框架

