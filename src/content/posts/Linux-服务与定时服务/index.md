---
title: Linux 服务与定时与定时服务
published: 2023-09-01 11:10:51
tags: [systemd,systemctl,Service]
category: Linux操作系统
draft: false
---
讲解 service 与 timer 之前，首先了解 `systemd`

## systemd 

系统管理守护进程

systemd是一个守护进程，负责在Linux系统中管理和控制各种服务和进程。

当第一启动时，systemd将会作为第一个进程 PID 1，一个初始化进程 init

systemd的管理方法是 **unit**

unit 共12种

> Service Unit 系统服务
>
> Target Unit 多Unit的组
>
> Device Unit 硬件设备
>
> Mount Unit 文件系统挂载点
>
> Automount Unit 自动挂载点
>
> Patch Unit 文件或路径
>
> Scope Unit 非Systemd启动的外部进程
>
> Slice Unit 进程组
>
> Snapshot Unit Systemd快照
>
> Socket Unit 进程间通信Socket
>
> Swap Unit swap文件
>
> Timer Unit 定时器
>

[【Linux】守护进程的定义，作用，创建流程 - 西*风 - 博客园 (cnblogs.com)](https://www.cnblogs.com/yinbiao/p/11203225.html)

​	定义：

> 守护进程是后台运行的程序，在系统启动时启动，一般运行到系统关闭，主要作用执行某些任务例如监控系统资源、等待时间发生等

​	特点：

> ​		启动时不需要会话，也就是用户登录
>
> ​		持久化运行，随系统启动
>
> ​		无需人工干预，无需用户输入
>

​	作用

> 由于由较长的生命周期，长用于处理某些任务或者等待某事件的发生
>
> 大多数服务由守护进程实现
>
> 关闭终端，相关进程终止，但是守护进程无此限制



## systemd 开机启动与关机停止流程

### 启动

1. 加载内核：在系统启动时，第一个运行的程序是boot loader，它会加载内核并将其放在内存中。
2. 挂载文件系统：内核运行后，会根据boot loader提供的信息挂载root文件系统，并将其设为只读状态。这样就可以保证root文件系统不被意外修改或破坏。
3. system进程启动：systemd是由system进程启动的，它是管理Linux系统的守护进程。
4. 加载systemd配置：systemd会读取它的配置文件system.conf，该文件定义了systemd使用的全局参数。
5. 加载排队参数：systemd使用cgroup来控制系统进程，它会在第4步中读取的配置文件中加载排队参数，并根据这些参数来设置进程的优先级和最大运行时间等。
6. 加载unit文件：systemd会读取系统定义的unit文件，这些文件用于描述系统各种服务、套接字、设备、挂载点等资源。systemd会根据unit文件按照依赖关系逐个启动这些服务，确保它们可以正确地运行。
7. 运行预启动任务：在启动系统服务之前，systemd会执行一系列预启动任务，以确保系统资源和网络状态可用。这些预启动任务包括文件系统收集、网络配置和日志记录等。
8. 启动服务：按照依赖关系和自动依赖发现，systemd会逐项启动系统中定义的服务。在启动每个服务之前，它会检查该服务所依赖的服务是否已启动并正常运行。如果依赖项没有启动或者异常，systemd将不会启动服务，直到依赖项已经完全启动。
9. 服务记录：在启动服务时，systemd会记录一个服务的状态，包括其启动时间、PID、运行状态等，以便随时检查服务的运行状态。

### 停止

1. 停止正在运行的服务：在停止系统之前，需要先停止正在运行的所有服务。可以使用systemctl stop命令或者systemctl halt命令停止运行中的服务。

2. 卸载文件系统：系统中正在使用的文件系统需要先被卸载，可以使用umount命令卸载挂载的文件系统。

3. 终止systemd守护进程：停止所有的系统服务之后，可以使用systemctl poweroff、systemctl halt或者systemctl poweroff命令来停止systemd守护进程，关闭系统电源并最终结束系统进程。

   

## systemctl 服务管理命令

```shell
systemctl  [start | stop | restart | reload | status] 服务名
```

1、启动和停止

```bash
sudo systemctl start servicename
```

```bash
sudo systemctl stop servicename
```

2、重启服务

```bash
sudo systemctl restart servicename
```

3、暂停与继续

```shell
sudo systemctl suspend servicename
```

```shell
sudo systemctl resume serivcename
```

4、查看状态

```bash
sudo systemctl restart servicename
```

5、设置开机自启动、关闭开机自启动

```bash
sudo systemctl status srvicename
```

## systemctl 常用命令分组

### 管理 systemd 服务

- systemctl start ：启动一个服务。
- systemctl stop ：停止一个服务。
- systemctl restart ：重启一个服务。
- systemctl reload ：重新加载一个服务的配置。
- systemctl enable ：设置一个服务开机启动。
- systemctl disable ：禁止一个服务开机启动。
- systemctl status ：查看一个服务的运行状态。

### 查看系统状态

- systemctl list-units：列出所有系统单元（包括服务、套接字等）。
- systemctl list-units --type=service：列出所有服务。
- systemctl list-units --state=running：列出所有正在运行的服务。
- systemctl list-units --failed：列出所有失败的服务。
- systemctl show ：显示一个服务的详细信息。

### 管理机器

- systemctl poweroff：关机。
- systemctl reboot：重启。
- systemctl suspend：挂起。
- systemctl hibernate：休眠。

### 系统管理

- systemctl daemon-reload：重新加载 systemd 守护进程。
- systemctl is-active ：判断一个服务是否正在运行。
- systemctl is-enabled ：判断一个服务是否开机启动。
- systemctl is-failed ：判断一个服务是否运行失败

### 管理日志

- journalctl：查看日志信息。
- journalctl -u ：查看指定服务的日志信息。
- journalctl -f：实时跟踪日志信息。
- journalctl --since "2019-11-01"：查看指定日期后的日志信息。
- journalctl --disk-usage：查看磁盘空间使用情况。
- journalctl --vacuum-time=7d：删除一个星期前的日志信息。

### 管理时间

- timedatectl：显示当前时间及时区信息。
- timedatectl set-timezone ：设置时区。
- timedatectl set-time ：设置时间。

### 管理网络

- systemctl restart network：重启网络服务。
- systemctl stop firewalld：停止防火墙服务。
- systemctl enable NetworkManager：设置 NetworkManager 开机自启动。

### 管理登录

- systemctl isolate multi-user.target：进入多用户模式。
- systemctl isolate graphical.target：进入图形模式。
- systemctl restart display-manager：重启显示管理器。

### 分析启动时间

- systemd-analyze：显示系统启动时间。

- systemd-analyze blame：显示每个服务的启动时间。

- systemd-analyze critical-chain：显示所有关键路径上的服务，即启动时间最长的服务，以及这些服务的启动时间。

- systemd-analyze plot > output.svg：生成启动时间的可视化图表，保存为 SVG 格式。

  

## 编写Service Unit

位置在 `/etc/systemd/system/` 目录或 `/lib/systemd/system/` 目录中

 `.service` 扩展名结尾，并包含服务的名称、描述、启动命令、依赖关系等信息

配置文件为INI格式，包含三个部分[Unit]、 [Server] 、 [Install]

###  `.service`基本内容

#### 1、[Unit]

用于描述启动位置，启动所需的服务，默认启动项，性能等级等

```bash
[Unit]
Description=Basic Systemd Service
After=network.target
```

> - Description：服务的描述信息。
> - After：该服务需要在哪些其他服务之后启动。
>

#### 2、[Service] 必选

它包含了有关服务本身的信息，例如需要使用的命令、用户和组信息、环境变量、工作目录、启动类型和重启规则等。下面是一个[Service]节的例子：

```bash
[Service]
ExecStart=/usr/bin/myservice --options
User=myuser
Group=mygroup
Environment=MY_VAR=myvalue
Restart=always
```

> - ExecStart：一个要运行的命令行（或其他动作）。
> - User：登陆到该服务的用户。
> - Group：登陆到该服务的用户组。
> - Environment：设置环境变量。
> - Restart：在服务失败或退出时如何启动服务。
>

#### 3、[Install]

它包含了对服务进行安装的信息，例如服务应该安装在哪个运行级别并且在启动、停止或重新启动时是否应该自动运行。下面是一个[Install]节的例子：

```shell
[Install]
WantedBy=multi-user.target
```

> - WantedBy：定义希望将服务安装到哪个目标单元。
>
> `multi-user.target` 是一个通常用于多用户模式的目标单元，在该目标单元中，系统完成了基本的启动过程，并准备接受用户登录
>
> `graphical.target`（图形用户界面模式）
>
> `network.target`（网络服务准备完成）
>
> `sysinit.target`：系统初始化目标单元，表示系统正在进行初始化过程。
>
> `rescue.target`：紧急救援目标单元，用于系统故障修复和救援。
>
> `poweroff.target`：关机目标单元，表示系统正在关机过程中。
>
> `reboot.target`：重启目标单元，表示系统正在重新启动过程中。
>
> `suspend.target`：挂起目标单元，表示系统正在挂起（睡眠）过程中。
>
> 指定多个
>
> or，当两个其中一个
>
> ```shell
> [Unit]
> WantedBy=target1.target target2.target
> ```
>
> and，两个都满足，强依赖
>
> ```shell
> [Unit]
> RequiredBy=target1.target target2.target
> ```
>
### 编写服务文件

sysinfo_email.service

```shell
[Unit]
Description=sysinfo_email

[Service]
ExecStart=/bin/bash $HOME/test.sh
```

5、定时任务

​	Timer 单元

https://www.ruanyifeng.com/blog/2018/03/systemd-timer.html

6、创建一个服务

文件位置/etc/systemd/system/，文件名称sysinfo_email.service

```shell
[Unit]
Description=sysinfo_email

[Service]
ExecStart=/bin/bash $HOME/test.sh
```

7、创建定时执行sysinfo_email.timer

```shell
[Unit]
Description=sysinfo_email timer

[Timer]
OnBootSec=1min
OnUnitActiveSec=1min
Unit=sysinfo_email.service

[Install]
WantedBy=timers.target
```

## 编写Timer Unit

位置在 `/etc/systemd/system/` 目录或 `/lib/systemd/system/` 目录中

 `.timer` 扩展名结尾，并包含服务的名称、描述、启动命令、依赖关系等信息

配置文件为INI格式，包含三个部分[Unit]、 [Timer] 、 [Install]

###  `.timer`基本内容

#### 1、[Unit]

​	与Server  Unit一致

#### 2、[Timer] 必选

它包含了有关服务本身的信息，例如需要使用的命令、用户和组信息、环境变量、工作目录、启动类型和重启规则等。下面是一个[Service]节的例子：

```bash
[Timer]
OnBootSec=1min
```

> - `OnCalendar`：
>   - `OnCalendar` 选项用于定义定时器的触发时间规则。
>   - 你可以使用类似 cron 的时间规范来指定具体的触发时间。
>   - 时间规范由空格分隔的多个字段组成，每个字段表示不同的时间单位（如分钟、小时、日期等）。
>   - 例如，`OnCalendar=*-*-* 00:00:00` 表示每天的午夜触发定时器，`OnCalendar=0 12 * * 1-5` 表示每周一至周五的中午 12 点触发定时器。
>   - 更详细的时间规范语法可以在 Systemd 的文档（`man systemd.time`）中找到。
> - `OnActiveSec`：
>   - `OnActiveSec` 选项定义了定时器激活后的一段时间后触发任务。
>   - 时间值可以使用时间单位（如秒、分钟、小时等）进行描述，例如 `OnActiveSec=10min` 表示定时器激活后的 10 分钟触发任务。
> - `OnBootSec`：
>   - `OnBootSec` 选项定义了系统启动后的一段时间后触发任务。
>   - 时间值可以使用时间单位进行描述，例如 `OnBootSec=1min` 表示系统启动后的 1 分钟触发任务。
> - `RandomizedDelaySec`：
>   - `RandomizedDelaySec` 选项指定一个随机延迟时间，以防止多个定时器同时触发导致的负载过载。
>   - 时间值可以使用时间单位进行描述，例如 `RandomizedDelaySec=30s` 表示在触发时间上增加 30 秒的随机延迟。
> - `Persistent`：
>   - `Persistent` 选项用于指定定时器的持久性。
>   - 如果设置为 `true`，当定时器触发时间到达时，即使系统在关机期间也会触发任务。
>   - 如果设置为 `false`（默认值），则在系统关机期间错过的触发时间将被忽略，直到下一次触发时间到达。
>

#### 3、[Install]

与Server  Unit一致

#### 4、[Server] 可选

```shell
[Service]
Type=oneshot
ExecStart=/path/to/my-script.sh
```

> - `Type=oneshot` 表示服务单元是一次性任务，它将在触发时运行一次，然后退出。
>
> - `ExecStart` 指定了要执行的命令或脚本的路径。你需要将 `/path/to/my-script.sh` 替换为实际的任务执行命令或脚本的路径
>

### 如何触发任务：

使用上面的4、[Server] ，直接触发脚本

或者

编写一个Server Unit，将触发指向`.service`

例如

```shell
[Unit]
Description=sysinfo_email timer
 
[Timer]
OnBootSec=3min
OnUnitActiveSec=3min
Unit=sysinfo_email.service
 
[Install]
WantedBy=timers.target
```

> 使用Unit=sysinfo_email.service 指定了与定时器关联的服务单元，即 `sysinfo_email.service`。，三分钟后触发sysinfo_email.service

### 编写服务文件

## service 服务管理命令（旧的）

[Linux之service命令基本使用_linux service_兮动人的博客-CSDN博客](https://blog.csdn.net/qq_41684621/article/details/117198880)

```shell
service 服务名 [start | stop | restart | reload | status]
```

由service管理的服务在`/etc/init.d`

