[AppImage](https://appimage.org/)是一种可执行文件格式，类似于Windows的exe文件，macOS的app文件，不过AppImage是运行在Linux上的可执行文件，而且是可以运行在不同发行版本的Linux，如Ubuntu, Debian, openSUSE, RHEL, CentOS, Fedora, Arch Linux ...

## 如何运行

运行AppImage文件非常简单, 通过以下步骤就可以:
1. 下载AppImage文件
2. 找开终端
3. 设置AppImage文件为可执行权限: `chmod +x path/name.AppImage`
4. 直接运行: `./path/name.AppImage`

## 如何集成到桌面程序目录

总是通过终端来运行AppImage很麻烦, 我们可以通过创建Desktop Entry来集成到桌面程序目录里面

### Desktop Entry程序存放目录

- `/usr/share/applications/`
- `/usr/local/share/applications/`
- `~/.local/share/applications/`

### Desktop Entry内容

```
# vi ~/.local/share/applications/name.desktop

[Desktop Entry]

# The type as listed above
Type=Application

# The version of the desktop entry specification to which this file complies
Version=1.0

# The name of the application
Name=AppImageName

# A comment which can/will be used as a tooltip
Comment=Flash card based learning tool

# The executable of the application, possibly with arguments.
Exec=path/name.AppImage

# The name of the icon that will be used to display this entry
Icon=path/AppImage.png

# Describes whether this application needs to be run in a terminal or not
Terminal=false

# Describes the categories in which this entry should be shown
Categories=Education;Languages;Java;

```