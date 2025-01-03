
### 一般处理

```
sudo pacman -Syu
```

### 重新生成签名

```
sudo pacman-key --init
sudo pacman-key --populate
sudo pacman-key --populate archlinuxcn
sudo pacman-key --refresh-keys
```

#### archlinuxcn签名信任解决办法

[[archlinuxcn-keyring签名信任解决办法]]

### 引用

- [ArchLinux升级时签名错误的处理方法。长时间不更新就可能遇到这个问题。](https://www.cnblogs.com/codeaaa/p/6844331.html)