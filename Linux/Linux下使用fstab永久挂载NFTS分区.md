在Linux下, 使用/etc/fstab来存放文件系统的静态信息, 当系统启动的时候，系统会自动地从这个文件读取信息，并且会自动将此文件中指定的文件系统挂载到指定的目录.

### fstab文件示例

```
$ vi /etc/fstab

# <file system>        <dir>         <type>    <options>             <dump> <pass>
tmpfs                  /tmp          tmpfs     nodev,nosuid          0      0
/dev/sda1              /             ext4      defaults,noatime      0      1
/dev/sda2              none          swap      defaults              0      0
/dev/sda3              /home         ext4      defaults,noatime      0      2
```

### fstab字段定义

- file systems: 要挂载的分区或存储设备
- dir: file systems的挂载位置
- type: 要挂载设备或是分区的文件系统类型，支持许多种不同的文件系统: `ext2`, `ext3`, `ext4`, `reiserfs`, `xfs`, `jfs`, `smbfs`, `iso9660`, `vfat`, `ntfs`, `swap` 及 `auto`. 设置成auto类型，mount 命令会猜测使用的文件系统类型，对 CDROM 和 DVD 等移动设备是非常有用的
- options: 挂载时使用的参数，注意有些mount 参数是特定文件系统才有的。一些比较常用的参数有:
	> auto - 在启动时或键入了 mount -a 命令时自动挂载
	> noauto - 只在你的命令下被挂载
	> exec - 允许执行此分区的二进制文件
	> noexec - 不允许执行此文件系统上的二进制文件
	> ro - 以只读模式挂载文件系统
	> rw - 以读写模式挂载文件系统
	> user - 允许任意用户挂载此文件系统, 若无显示定义, 隐含启用 noexec, nosuid, nodev 参数
	> users - 允许所有 users 组中的用户挂载文件系统
	> nouser - 只能被 root 挂载
	> owner - 允许设备所有者挂载
	> sync - I/O 同步进行
	> async - I/O 异步进行
	> dev - 解析文件系统上的块特殊设备
	> nodev - 不解析文件系统上的块特殊设备
	> suid - 允许 suid 操作和设定 sgid 位. 这一参数通常用于一些特殊任务, 使一般用户运行程序时临时提升权限
	> nosuid - 禁止 suid 操作和设定 sgid 位
	> noatime - 不更新文件系统上 inode 访问记录, 可以提升性能(参见 atime 参数)
	> nodiratime - 不更新文件系统上的目录 inode 访问记录, 可以提升性能(参见 atime 参数)
	> relatime - 实时更新 inode access 记录. 只有在记录中的访问时间早于当前访问才会被更新.(与 noatime 相似, 但不会打断如 mutt 或其它程序探测文件在上次访问后是否被修改的进程.), 可以提升性能(参见 atime 参数)
	> flush - `vfat` 的选项，更频繁的刷新数据, 复制对话框或进度条在全部数据都写入后才消失
	> defaults - 使用文件系统的默认挂载参数, 例如 `ext4` 的默认参数为:`rw`, `suid`, `dev`, `exec`, `auto`, `nouser`, `async`
	> 
- dump: dump 工具通过它决定何时作备份. dump 会检查其内容, 并用数字来决定是否对这个文件系统进行备份. 允许的数字是 0 和 1 . 0 表示忽略, 1 则进行备份. 大部分的用户是没有安装 dump 的, 对他们而言应设为 0
- pass: fsck 读取 pass 的数值来决定需要检查的文件系统的检查顺序. 允许的数字是0, 1, 和2. 根目录应当获得最高的优先权 1, 其它所有需要被检查的设备设置为 2, 0 表示设备不会被 fsck 所检查

### 查询硬盘列表

```
$ sudo fdisk -l

Disk /dev/nvme1n1：931.51 GiB，1000204886016 字节，1953525168 个扇区
磁盘型号：WD Green SN350 1TB                      
单元：扇区 / 1 * 512 = 512 字节
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：gpt
磁盘标识符：2B017B6F-7785-4639-9D64-8C69763A0B5E

设备                 起点       末尾      扇区   大小 类型
/dev/nvme1n1p1         34      32767     32734    16M Microsoft 保留
/dev/nvme1n1p2      32768  519923711 519890944 247.9G Microsoft 基本数据
/dev/nvme1n1p3  519923712 1031923711 512000000 244.1G Microsoft 基本数据
/dev/nvme1n1p4 1031923712 1243119615 211195904 100.7G Microsoft 基本数据
/dev/nvme1n1p5 1243119616 1953525134 710405519 338.7G Linux 文件系统

```

### 编辑fstab文件挂载分区

1. 首先创建挂载点
```
$ mkdir -p /home/zhouhr/workspace
```

2. 编辑/etc/fstab文件, 按格式要求加入要挂载的分区数据, 然后重启系统
```
$ vi /etc/fstab

# <file system>        <dir>         <type>    <options>             <dump> <pass>
tmpfs                  /tmp          tmpfs     nodev,nosuid          0      0
/dev/sda1              /             ext4      defaults,noatime      0      1
/dev/sda2              none          swap      defaults              0      0
/dev/sda3              /home         ext4      defaults,noatime      0      2
/dev/nvme1n1p2         /home/zhouhr/workspace    nfts    defaults,noatime    0    0

```

### 引用

- [【纯干货】linux之fstab文件详解](https://zhuanlan.zhihu.com/p/250658106)
- [【Linux】如何将ntfs硬盘挂载到home目录下并具有读写权限_mount ntfs](https://blog.csdn.net/qq_41084756/article/details/129183007)
- [Manjaro开机挂载NTFS硬盘_fstab manjaro-CSDN博客](https://blog.csdn.net/weixin_43840399/article/details/93475915)
