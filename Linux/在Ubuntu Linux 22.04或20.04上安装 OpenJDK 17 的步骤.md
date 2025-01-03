Java 是一种多用途且广泛使用的编程语言，JAVA 的开源实现被称为 OpenJDK。它由开源 Java 社区积极维护和支持。今天，我们将学习在 Ubuntu Linux 上安装 OpenJDK 17 的命令，Ubuntu 是最广泛使用的 Linux 发行版之一。

## 前提条件

在你打算安装 **Java 17（OpenJDK 17）** 的 Ubuntu 桌面或服务器上，我们不需要任何特殊配置。我们只需要系统管理员权限（也称为 sudo 权限）以及一个正常的网络连接。所以，如果你都有，只需打开 Ubuntu 的命令终端，按照以下步骤操作：

## 步骤 1：更新 Ubuntu 软件包列表

在安装新软件包之前，有必要对 Linux 系统进行更新。这将确保系统拥有已安装软件包的最新版本，并刷新 APT 索引缓存，使其能够下载和安装最新的应用程序。

```bash
sudo apt update
```

## 步骤 2：在 Ubuntu 22.04 或 20.04 上安装 OpenJDK 17

与 Oracle 的 Java 不同，OpenJDK 完全由开源社区维护，因此可以直接通过 Ubuntu Linux 官方软件仓库获得。这意味着我们无需添加任何额外的内容到系统中。只需在终端中使用给定的命令，OpenJDK 17 版本将被安装在你的 Ubuntu 系统上。

```bash
sudo apt install openjdk-17-jdk
```

当系统要求输入密码时，请输入密码。然后键入 '**Y**' 并按 '**Enter**' 继续安装。  
![在 Ubuntu 上安装 OpenJDK 17](https://linuxstory.org/wp-content/uploads/2023/07/7.27.1.png)

## 步骤 3：确认 OpenJDK 已安装

虽然通过上面的命令，OpenJDK 17 已经安装在我们的系统上，但仍有人想要确认一下。你可以使用以下命令查看 Ubuntu 上当前安装的 Java 版本。

```bash
java -version
```

**你应该看到类似以下的输出：**

```bash
openjdk version "17.0.7" 2023-04-18 OpenJDK Runtime Environment (build 17.0.7+7-Ubuntu-0ubuntu122.04.2) OpenJDK 64-Bit Server VM (build 17.0.7+7-Ubuntu-0ubuntu122.04.2, mixed mode, sharing)
```

## 步骤 4：设置默认 Java 版本（可选）

如果你的系统上已经安装了多个 OpenJDK 版本，你可以使用 **update-alternatives** 命令将某个特定版本的 JAVA 设置为全局默认版本，适用于所有应用程序。该命令帮助用户管理多个 Java 安装。

要配置默认的 Java 版本，请使用以下命令：

```bash
sudo update-alternatives --config java
```

上述命令将显示所有已安装的 Java 版本列表。输入对应于 OpenJDK 条目的编号，即可将其设置为默认版本，然后按 '**Enter**'。  
![设置默认 Java 版本](https://linuxstory.org/wp-content/uploads/2023/07/7.27.2.png)

## 步骤 5：设置 JAVA\_HOME 环境变量（可选）

与前面的步骤一样，这一步也不是必须的，但某些应用程序或工具，特别是开发人员使用的工具，可能需要设置 **JAVA\_HOME** 环境变量。要将 JAVA\_HOME 变量设置为 OpenJDK 17，请按照以下步骤进行：

找到 OpenJDK 17 的安装路径。你可以使用 update-alternatives 命令来查找：

```bash
sudo update-alternatives --list java
```

复制对应于 OpenJDK 17 的路径。

使用 Nano 或其他你喜欢的文本编辑器打开 **.bashrc** 文件：

```bash
nano ~/.bashrc
```

在文件末尾添加以下行，但不要忘记将 **/path/to/your/openjdk-17** 替换为实际的路径。

```bash
export JAVA_HOME=/path/to/your/openjdk-17
```

**保存** 文件并退出文本编辑器（在 Nano 编辑器中按 **Ctrl + X**，然后输入 **Y**，最后按 **Enter**）。

要应用更改，可以关闭终端并重新打开，或者使用给定的命令。

```bash
source ~/.bashrc
```

### 结论

通过本教程，你将能够在 Ubuntu 系统上轻松安装 OpenJDK 17。如果你计划开发 Java 应用程序，还可以设置适当的环境变量，以优化开发流程。