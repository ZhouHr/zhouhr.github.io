### 问题

在更新系统后，出现wps加粗字体显示成以下模样：

![[wps加粗字体异常.png]]


### 解决办法

通过查询得知为freetype2版本升级造成，可通过自行降服版本来解决

```
freetype2-2.13.2-1  ->  freetype2-2.13.0-1
```

旧版本需要上官方网站手动下载执行

```
$ wget https://archive.archlinux.org/packages/f/freetype2/freetype2-2.13.0-1-x86_64.pkg.tar.zst

$ sudo pacman -U freetype2-2.13.0-1-x86_64.pkg.tar.zst
```


- [官网归档包下载](https://archive.archlinux.org/packages/f/freetype2/)
- [Wps很多字体的加粗都显示异常的问题](https://forum.manjaro.org/t/wps/144447)
- [Archlinux/Manjaro更新WPS后，加粗字体显示错误，显示成一团黑](https://zhuanlan.zhihu.com/p/657532417)