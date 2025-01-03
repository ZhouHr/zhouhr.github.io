在archlinux上面安装deb应用需要用到debtap来转换deb包为arch包

### 安装debtap

```
$ yay -S debtap
```

### 升级debtap

```
$ sudo debtap -u
```

### 使用debtap

在使用debtap过程中会需要输入包名, License. 包名建议直接软件具体名称, License随便选择比如: GPL, 最后会在命令执行目录生成一个 .pkg.tar.zst 后缀的软件包, 直接使用pacman安装此包.

```
$ sudo debtap xxxx-x.x.deb
==> Extracting package data...
==> Fixing possible directories structure differencies...
==> Generating .PKGINFO file...

:: Enter Packager name (can be left blank):
xxxx-x.x

:: Enter package license (can be left blank, you can enter multiple licenses comma separated):
GPL

*** Creation of .PKGINFO file in progress. It may take a few minutes, please wait...
...
...
==> Creating final package...
==> Package successfully created!
==> Removing leftover files...

sudo pacman -U xxxx-x.x.pkg.tar.zst 

```