由于工作系统默认安装的Qemu是最新的Qemu8，且没有安装用户态的Qemu命令，需要自己增加安装，直接使用下面命令即可安装：
```
sudo pacman -S qemu-emulators-full

...
```

安装完后就可以使用 `qemu-riscv64` 命令。
