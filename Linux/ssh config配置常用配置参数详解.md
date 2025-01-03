### 什么是 ssh_config?

一些工程师可能会惊讶于 ssh 客户端行为有多少是可以通过配置文件来配置的。如果没有配置文件，为 ssh 指定命令行参数很快就会变得很麻烦。

```shell
ssh -i /users/virag/keys/us-west/ed25519 -p 1024 -l virag \ myserver.aws-west.example.com
```

这句命令太长了，更不用说一天打多次了。如果你要管理多台服务器和虚拟机，创建一个自定义的 `~/.ssh/ssh_config` 是常用 `ssh` 命令的好方法。

我们可以通过编辑 `ssh_config`，将上面的例子缩短为 `ssh myserver`。

```
Host myserver
	Hostname 127.0.0.1
	User zhouhr
	Port 22
	IdentityFile path/id_rsa
```

优雅而简单。
### ssh_config 如何工作

ssh 客户端从三个地方读取配置，顺序如下:

1.  系统范围内的 `/etc/ssh/ssh_config`
2.  在 `~/.ssh/ssh_config` 中的用户特定配置。
3.  直接提供给 ssh 的命令行参数

这意味着命令行参数可以覆盖用户特定的配置，可以覆盖全局配置

当连接参数被重复使用时，通常在 `ssh_config` 中定义这些参数比较容易，它们会在连接时自动应用。虽然它们通常是在用户第一次运行 ssh 时创建的，但目录和文件可以通过以下方式手动创建。

回到上面的例子，你可能会注意到 `ssh_config` 是以主机头开始的段落来组织的。

```
Host [alias]
	Option1 [Value]
	Option2 [Value]
	Option3 [Value]
```

虽然技术上没有必要，但这种缩进的格式很容易被人类阅读。然而，ssh 客户端并不关心这种格式化，相反，它将通过将命令行中输入的 ssh 参数与所有主机头匹配来获取配置参数。通配符也可以作为主机头的一部分。考虑一下:

```
Host myserver2
	Hostname myserver2.aws-west.example.com
Host myserver*
	Hostname myserver1.aws-west.example.com
	User virag
	Port 1024
```

使用 `myserver1` 的别名，我们可以从第二节中得到我们所期望的东西。

```
Hostname myserver1.aws-west.example.com
User virag
Port 1024
```

但 `myserver2` 也有类似的选项列表。

```
Hostname myserver2.aws-west.example.com
User virag
Port 1024
```

ssh 客户端通过模式匹配获取这些信息，并在向下顺序读取文件时锁定值。因为 myserver2 同时匹配了 `myserver2` 和 `myserver*`，所以它会先从 myserver2 中获取 Hostname 值。然后，当到了第二种模式匹配时，就会使用 User 和 Port 的值，但 Hostname 字段已经被填满。让我再重复一遍，ssh 接受每个选项的第一个值。

### 常见的 SSH 配置参数

在 ` ssh_config` 中，有近 100 个 ssh_config 选项。下面是一些常用选项。

- `Port` - 远程 ssh 守护进程运行的端口。如果守护进程运行在默认的 22 号端口上，则不需要定义这个选项。在不同的端口上运行 ssh 守护进程被认为是一个很好的做法，因为这样可以减少僵尸探测的数量。
    
- `Hostname` - 用于建立连接的真实主机名，如 DNS 或 IP 地址。这对缩短主机名很有用。例如，你可以让一个方便的 `ssh mongo` 带你到 `mongo-12.staging.example.com`。
    
- `ProxyJump` - 这个选项将通过连接的服务器进行隧道简化为一个标志，`-J`，用一个别名来命名中间主机（本地客户端和最终目的地之间的主机）。这只适用于较新的客户端（[OpenSSH 7.3+](https://www.openssh.com/txt/release-7.3)）。下面我将详细介绍这个。
    
- `ForwardAgent` & `AddKeysToAgent` - 在主机之间跳转（当你在另一个 `ssh` 会话中再次键入 `ssh` 时）需要重复验证。要做到这一点，`ssh` 凭证必须存储在中间服务器上，但这不是一个安全的做法。这两个选项允许另一个通常被称为 `ssh-agent` 的进程自动将你的本地 `ssh` 凭证加载到内存中，并通过一个安全转发的 UNIX 套接字将其提供给中间机器的 ssh 客户端。`ForwardAgent` 可以实现这种转发行为，而 `AddKeysToAgent` 则可以自动将密钥加载到内存中。我将在下面提供更多细节。
    
- `IdentityFile` - 这个选项指定了 ssh 客户端应该尝试验证的密钥的路径。这并不妨碍 ssh 客户端尝试 `~/.ssh` 或 `ssh-agent` 中的密钥。常用于由于某种原因，密钥没有存储在默认位置的情况下。
    
- `IdentitiesOnly` - 通常和 `IdentityFile` 一起使用，这个选项会告诉 ssh 客户端到底要提交哪个密钥，并放弃 `~/.ssh` 或 `ssh-agent` 中的任何密钥。因为如果尝试了太多无效的密钥，ssh 会抛出一个认证错误，这个选项可以帮助客户端精确地识别要提交的密钥。即使在 `ssh_config` 中启用了 `IdentitiesOnly`，任何在命令行输入的身份信息也会被尝试。
    
- `CertificateFile` - 考虑到密钥在很大程度上已经过时了，这个选项可以和 `IdentityFile` 一起使用来指定要提交的证书。这并不总是必要的。当证书颁发机构签署一个密钥来创建证书时，`-cert.pub` 将自动附加到密钥的文件名中。在加载密钥之前，ssh 客户端将首先尝试使用预期的命名惯例从提供的文件名中加载证书。然而，如果密钥和证书文件名不遵循这种模式，那么必须使用 `CertificateFile`，否则将无法找到证书。[阅读更多关于为什么你应该使用证书](https://gravitational.com/blog/how-to-ssh-properly/)。
    
- `SetEnv` & `SendEnv` - 这些选项允许 ssh 客户端向指定的主机发送本地环境变量。主机服务器必须通过在 `/etc/ssh/sshd_config` 中将 `AcceptEnv` 设置为 `Yes` 来接受这些环境变量。
    
- `ServerAliveInterval` & `ServerAliveCountMax` -如果 ssh 客户端在指定的时间间隔内没有收到任何数据，它将请求主机服务器做出响应。这可以防止负载均衡器和服务器因不活动而放弃连接。
    
- `HostKeyAlias` - ssh 客户端会被指示使用 `~/.ssh/known_hosts` 中的密钥别名，而不是 HostName。这对于具有动态变化的 IP 地址的主机或在一台主机上运行的多个服务器来说非常有用。
    
- `PreferredAuthentication` - 这个选项决定了验证方法的尝试顺序。默认值是 `gssapi-with-mic`, `hostbased`, `publickey`, `keyboard-interactive` 和 `password`。
    

### 引用

- [SSH configuration  ssh_config](https://goteleport.com/blog/ssh-config/)