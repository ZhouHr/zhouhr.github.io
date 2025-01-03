### 安装数据库
```
$ sudo pacman -S mysql
正在解析依赖关系...
正在查找软件包冲突...

软件包 (4) jemalloc-1:5.3.0-3  libmysqlclient-8.2.0-2  mysql-clients-8.2.0-2  mysql-8.2.0-2

下载大小：       25.40 MiB
全部安装大小：  251.54 MiB

:: 进行安装吗？ [Y/n] Y

:: 正在获取软件包......

...

(2/4) Reloading system manager configuration...
(3/4) Creating temporary files...
(4/4) Arming ConditionNeedsUpdate...

```

### 初始化数据库

输出会包含root密码
```
$ sudo mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql
2024-03-12T09:37:46.626701Z 0 [System] [MY-015017] [Server] MySQL Server Initialization - start.
2024-03-12T09:37:46.628492Z 0 [Warning] [MY-010915] [Server] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2024-03-12T09:37:46.628566Z 0 [System] [MY-013169] [Server] /usr/bin/mysqld (mysqld 8.2.0) initializing of server in progress as process 14763
2024-03-12T09:37:46.635499Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2024-03-12T09:37:46.835369Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2024-03-12T09:37:47.603653Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: NiIXa(9sPIwd
2024-03-12T09:37:49.785066Z 0 [System] [MY-015018] [Server] MySQL Server Initialization - end.

```

### 启动数据库
```
sudo systemctl start mysqld
```

### 数据库配置向导

本步可以修改root密码, 设置安全规则
```
sudo mysql_secure_installation
```

### 开机启动
```
$ systemctl enable mysqld
Created symlink /etc/systemd/system/multi-user.target.wants/mysqld.service → /usr/lib/systemd/system/mysqld.service.
```


### 引用

- [manjaro linux 安装MYSQL8.0过程记录](https://cloud.tencent.com/developer/article/1559532)