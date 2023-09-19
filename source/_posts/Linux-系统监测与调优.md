---
title: Linux 系统监测与调优
abbrlink: 1206971b
date: 2023-09-04 22:49:11
tags:
---
### 进程与内存的查看

#### top/htop

pid<100 为系统的，与启动顺序有关，小优先高

user 用户 root为超管，系统创建

### 系统检测与调优

#### top 进程

/内存泄漏

#### htop 更hight进程

command 列出应用或命令

f4 筛选

#### netstat网络

1. 显示所有活动的网络连接：

   ```shell
   netstat -a
   ```

2. 仅显示TCP连接：

   ```shell
   netstat -at
   ```

3. 仅显示UDP连接：

   ```shell
   netstat -au
   ```

4. 显示监听中的端口：

   ```shell
   netstat -l
   ```

5. 显示与进程关联的网络连接：

   ```shell
   netstat -p
   ```

6. 显示路由表信息：

   ```shell
   netstat -r
   ```

7. 显示网络接口统计：

   ```shell
   netstat -i
   ```

8. 显示带有PID和进程名称的网络连接：

   ```shell
   netstat -ap
   ```

#### stress 压力测试

##### cpu

```shell
stress --cpu 2 --timeout 120
```

双核心 持续时间120s

##### mem

```shell
stress --vm 4 --vm-bytes 1G
```

4进程 每进程占用1G

#### fio 磁盘压力测试

```shell
fio --name=random-readwrite --ioengine=libaio --rw=randrw --bs=4k --direct=1 --size=1G --numjobs=4 --runtime=60s
```

> `--name`用于指定测试任务的名称，`--ioengine`设定I/O引擎，`--rw`定义读写操作类型（例如`randrw`表示随机读写），`--bs`指定块大小，`--direct`启用直接I/O，`--size`定义每个作业的数据大小，`--numjobs`指定作业线程数，`--runtime`设置测试运行时间

