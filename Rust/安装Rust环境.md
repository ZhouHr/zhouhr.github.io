rustup是Rust的安装程序，也是它的版本管理程序。强烈建议使用rustup来安装Rust。

### 安装
#### 在Linux可MacOS上面安装rustup

打开终端输入以下命令：
```
$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```
此命令将下载一个脚本并开始安装rustup工具，此工具将安装最新的Rust稳定版本，安装过程中需要输入管理员密码。

如果显示下面这行文字，表示Rust安装成功：
```
Rust is installed now. Great!
```

#### 安装C语言编译器（非必须）

##### MacOS平台：

```
$ xcode-select --install
```

##### Linux平台：

```
$ sudo pacman -S gcc glibc
```

### 检查安装是否成功

如果要检查是否正确安装了rust，命令行输入以下命令即可：
```
$ rustc -V
rustc 1.76.0 (07dca489a 2024-02-04)

$ cargo -V
cargo 1.76.0 (c84b36747 2024-01-18)

```
### 本地文档

安装Rust的同时会在本地安装一个文档服务以方便我们离线阅读，运行以下命令让浏览器打开本地文档：
```
$ rustup doc
```


### 配置国内镜像源

在 `$HOME/.cargo/config.toml`添加内容

#### 科大镜像
```
[source.crates-io]
replace-with = 'ustc'

[source.ustc]
registry = "https://mirrors.ustc.edu.cn/crates.io-index/"

# 稀疏索引，要求 cargo >= 1.68
[source.rsproxy-sparse]
registry = "sparse+https://mirrors.ustc.edu.cn/crates.io-index/"

```

#### 字节跳动
```
[source.crates-io]
replace-with = 'rsproxy'

[source.rsproxy]
registry = "https://rsproxy.cn/crates.io-index"

# 稀疏索引，要求 cargo >= 1.68
[source.rsproxy-sparse]
registry = "sparse+https://rsproxy.cn/index/"

[registries.rsproxy]
index = "https://rsproxy.cn/crates.io-index"

[net]
git-fetch-with-cli = true

```

### 更新

要更新Rust，命令行执行以下命令即可：

```
$ rustup update
```

### 卸载

要卸载Rust和rustup工具，命令行执行以下命令即可：
```
$ rustup self uninstall
```


### 引用

- [官网文档](https://www.rust-lang.org/tools/install)