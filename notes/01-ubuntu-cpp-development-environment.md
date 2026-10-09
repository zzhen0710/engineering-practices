# Ubuntu C++ 开发环境搭建实录

> 目标：从一台刚安装完成的 Ubuntu 虚拟机开始，逐步搭建可用于 C++、Linux、网络编程和系统编程的开发环境。
>
> 记录方式：以实际执行的命令为主线，说明「执行了什么 → 看到了什么 → 说明什么 → 为什么进行下一步」。

---

# 1. 确认 Ubuntu 系统环境

刚进入新的 Ubuntu 虚拟机时，首先确认当前用户、主机名、系统版本和 CPU 架构。

```bash
whoami
# 查看当前登录用户

hostname
# 查看当前主机名

hostnamectl
# 查看主机名、操作系统、内核、体系结构等综合信息

uname -m
# uname = Unix name
# -m = machine，查看机器体系结构
```

得到的信息大致为：

```text
用户：zhen
主机名：ubuntu-dev
系统：Ubuntu 26.04.1 LTS
架构：x86_64
```

其中：

- `zhen`：当前 Linux 用户
- `ubuntu-dev`：这台虚拟机的 hostname
- `x86_64`：64 位 x86 架构

查看当前用户的家目录：

```bash
echo $HOME
```

得到：

```text
/home/zhen
```

因此以后看到：

```bash
~
```

对于当前用户来说就是：

```text
/home/zhen
```

---

# 2. 更新 Ubuntu 软件包索引

安装开发工具前，先更新本机的软件包索引。

```bash
sudo apt update
```

其中：

```text
sudo
≈ superuser do
以管理员权限执行命令
```

`apt` 是 Ubuntu / Debian 系常用的软件包管理工具。

这里的：

```text
update
```

并不是直接升级软件，而是：

> 从软件仓库重新获取“目前有哪些软件、有哪些版本”的信息。

因此可以简单理解为：

```text
apt update
→ 更新软件目录
```

执行过程中还能看到：

```text
esm.ubuntu.com/apps
esm.ubuntu.com/infra
```

说明之前启用的 Ubuntu Pro / ESM 已经正常工作。

其中：

```text
ESM
= Expanded Security Maintenance
= 扩展安全维护
```

Ubuntu Pro 主要提供额外的安全维护服务，并不是所谓“更专业的开发版 Ubuntu”。

---

# 3. 升级已有软件并清理系统

更新软件索引后执行：

```bash
sudo apt upgrade
# 根据最新的软件包索引升级当前已经安装的软件
```

然后：

```bash
sudo apt autoremove
# 删除以前作为依赖安装、现在已经不再需要的软件包
```

最后：

```bash
sudo apt clean
# 清理 apt 下载的软件包缓存
```

这一组常用命令可以记成：

```bash
sudo apt update
# 更新软件包索引

sudo apt upgrade
# 升级现有软件

sudo apt autoremove
# 删除无用依赖

sudo apt clean
# 清理 apt 缓存
```

---

# 4. 安装 C/C++ 基础工具链

执行：

```bash
sudo apt install build-essential
```

`build-essential` 不是某一个具体程序，而是一组 Ubuntu 基础编译开发工具。

其中主要包含：

```text
gcc
g++
make
C/C++ 标准开发头文件
其他基础构建工具
```

安装后验证：

```bash
gcc --version
g++ --version
make --version
```

当前环境得到：

```text
GCC 15.2.0
G++ 15.2.0
GNU Make 4.4.1
```

其中：

```text
GCC
= GNU Compiler Collection
```

`gcc` 通常作为 C 编译驱动使用。

`g++` 是 GCC 中主要用于 C++ 的编译驱动。

最简单的 C++ 编译过程：

```text
main.cpp
   ↓
  g++
   ↓
编译 + 链接
   ↓
可执行程序
```

`make` 则负责根据 `Makefile` 中描述的依赖关系，组织整个项目的构建过程。

---

# 5. 安装常用 Linux 开发工具

继续执行：

```bash
sudo apt install git curl wget tree cmake
```

安装后验证：

```bash
git --version
cmake --version
```

当前得到：

```text
git 2.53.0
cmake 4.2.3
```

各工具作用：

```bash
git
# 版本控制

curl
# 通过 URL 请求、发送或获取数据

wget
# 下载文件

tree
# 以树状结构显示目录

cmake
# 根据项目配置生成构建系统
```

例如：

```bash
tree
```

可以看到：

```text
project/
├── CMakeLists.txt
├── include/
└── src/
```

相比单纯的：

```bash
ls
```

`tree` 更适合观察一个项目的整体目录结构。

---

# 6. 安装调试和系统排错工具

执行：

```bash
sudo apt install gdb strace lsof
```

验证 GDB：

```bash
gdb --version
```

当前环境：

```text
GNU gdb 17.1
```

三者分别用于：

```text
GDB
= GNU Debugger
源码级程序调试器

strace
≈ system call trace
跟踪进程执行的系统调用

lsof
= list open files
查看进程当前打开的文件、socket 等资源
```

以后典型的使用场景：

```text
程序为什么崩溃？
→ gdb

程序为什么一直卡着？
到底在执行哪些系统调用？
→ strace

哪个进程占用了某个端口或文件？
→ lsof
```

---

# 7. 配置 Git

Git 安装完成后，设置 Git 作者信息：

```bash
git config --global user.name "用户名"

git config --global user.email "邮箱"
```

这里：

```text
--global
```

表示：

> 对当前 Linux 用户的所有 Git 仓库生效。

然后设置新仓库默认分支：

```bash
git config --global init.defaultBranch main
```

这样以后执行：

```bash
git init
```

默认生成：

```text
main
```

而不是旧式的：

```text
master
```

检查当前全局配置：

```bash
git config --global --list
```

---

# 8. 生成 Ubuntu → GitHub 的 SSH 密钥

为了以后从 Ubuntu 通过 SSH 访问 GitHub，需要先在 Ubuntu 中生成一套 SSH 密钥。

首先可以检查当前用户是否已经存在 SSH 密钥：

```
ls -la ~/.ssh
# ls = list，列出目录内容
# -l：显示详细信息
# -a = all，同时显示隐藏文件
# ~/.ssh：当前用户的 SSH 配置目录
```

如果不存在：

```
id_ed25519
id_ed25519.pub
```

则生成新的 Ed25519 密钥：

```
ssh-keygen -t ed25519 -C "GitHub邮箱"
# ssh-keygen：SSH 密钥生成工具
# -t = type，指定密钥算法类型
# ed25519：使用 Ed25519 算法
# -C = comment，为公钥添加备注
```

其中：

```
ssh-keygen
→ SSH 密钥生成工具

-t ed25519
→ 指定生成 Ed25519 类型的密钥

-C "GitHub邮箱"
→ 给公钥添加备注
→ 主要用于以后识别这把密钥
```

执行后，首先会询问密钥保存位置：

```
Enter file in which to save the key (/home/zhen/.ssh/id_ed25519):
```

括号中的：

```
/home/zhen/.ssh/id_ed25519
```

就是默认保存位置。

如果没有特殊需求，**直接按 Enter** 即可使用默认位置。

于是私钥会保存为：

```
/home/zhen/.ssh/id_ed25519
```

也就是：

```
~/.ssh/id_ed25519
```

对应的公钥则自动保存为：

```
~/.ssh/id_ed25519.pub
```

这里：

```
~
→ 当前用户的 Home Directory（家目录）

当前用户为 zhen：

~
= /home/zhen
```

因此：

```
~/.ssh/id_ed25519
```

实际就是：

```
/home/zhen/.ssh/id_ed25519
```

接下来 `ssh-keygen` 还会询问：

```
Enter passphrase (empty for no passphrase):
```

这里的 `passphrase` 是：

> **给私钥本身增加的一层密码保护。**

如果设置了 passphrase，即使别人获得了私钥文件，通常仍需要知道这个 passphrase 才能使用它。

如果不希望每次使用密钥时额外输入 passphrase，也可以直接按 Enter 留空。

随后还会要求再次确认：

```
Enter same passphrase again:
```

如果前面留空，这里同样直接按 Enter。

完成后会看到类似：

```
Your identification has been saved in /home/zhen/.ssh/id_ed25519
Your public key has been saved in /home/zhen/.ssh/id_ed25519.pub
```

这表示密钥对已经生成成功。

然后查看：

```
ls -l ~/.ssh
# 查看 ~/.ssh 目录中的 SSH 相关文件
```

此时应该能够看到：

```
id_ed25519
id_ed25519.pub
```

其中：

```
id_ed25519
→ Private Key
→ 私钥
```

私钥只应该保存在 Ubuntu 本机：

> **绝不能上传到 GitHub、发送给别人或公开。**

而：

```
id_ed25519.pub
→ Public Key
→ 公钥
```

公钥可以提供给 GitHub。

因此这一对密钥的关系是：

```
Ubuntu
~/.ssh/
│
├── id_ed25519
│   └── 私钥
│       留在 Ubuntu
│
└── id_ed25519.pub
    └── 公钥
        可以上传到 GitHub
```

接下来可以查看公钥内容：

```
cat ~/.ssh/id_ed25519.pub
# cat：输出文件内容
# 这里只读取 .pub 公钥文件
```

会得到一整行类似：

```
ssh-ed25519 AAAA... GitHub邮箱
```

复制这一整行公钥，并在 GitHub 中添加为 SSH Key。

最终形成：

```
Ubuntu
│
├── ~/.ssh/id_ed25519
│       │
│       └── 私钥，只保留在 Ubuntu
│
└── ~/.ssh/id_ed25519.pub
        │
        │ 将公钥内容上传
        ↓
      GitHub
        │
        └── 保存 Ubuntu 的公钥
```

之后 Ubuntu 通过 SSH 连接 GitHub 时：

```
Ubuntu
持有私钥
    │
    │ SSH authentication
    ↓
GitHub
保存对应公钥
    │
    ↓
验证 Ubuntu 是否持有对应私钥
```

需要注意，如果最开始执行：

```
ls -la ~/.ssh
```

就已经发现存在：

```
id_ed25519
id_ed25519.pub
```

则不要直接重新生成同名密钥。

因为再次执行：

```
ssh-keygen -t ed25519 -C "GitHub邮箱"
```

并选择默认位置时，会发现目标文件已经存在，并询问：

```
/home/zhen/.ssh/id_ed25519 already exists.
Overwrite (y/n)?
```

如果选择覆盖，原来的密钥会被替换，原本依赖旧公钥建立的 SSH 认证关系可能因此失效。

所以应该先确认旧密钥是否仍在使用，再决定是：

```
继续使用原有密钥
```

还是：

```
生成另一套使用不同文件名的密钥
```

本次配置中使用默认位置，因此最终用于 Ubuntu → GitHub 的密钥为：

```
私钥：
~/.ssh/id_ed25519

公钥：
~/.ssh/id_ed25519.pub
```

可以简单记住：

> `**ssh-keygen**` **先问“密钥存哪里”，再问“私钥要不要加密码”；直接按 Enter 才表示接受默认位置** `**/home/zhen/.ssh/id_ed25519**`**。**

---

# 9. 测试 Ubuntu → GitHub SSH

将公钥添加到 GitHub 后执行：

```bash
ssh -T git@github.com
```

其中：

```text
SSH
= Secure Shell
```

参数：

```text
-T
不申请交互式伪终端
```

这里主要用于测试：

> Ubuntu 能否成功使用 SSH 身份认证连接 GitHub。

第一次连接时会出现 GitHub 主机指纹，并询问：

```text
Are you sure you want to continue connecting?
```

输入：

```text
yes
```

GitHub 的主机公钥信息会保存到：

```text
~/.ssh/known_hosts
```

这里实际上存在双向确认：

```text
GitHub 保存 Ubuntu 的公钥
→ GitHub 判断“你是谁”

Ubuntu 保存 GitHub 的 host key
→ Ubuntu 判断“对面是不是 GitHub”
```

因此：

```text
id_ed25519 / id_ed25519.pub
```

解决的是：

> 服务器认证客户端。

而：

```text
known_hosts
```

主要解决的是：

> 客户端认证服务器。

---

# 10. 安装 OpenSSH Server

前面建立的是：

```text
Ubuntu → GitHub
```

现在还希望能够：

```text
Windows → Ubuntu
```

因此 Ubuntu 自己需要提供 SSH 服务。

执行：

```bash
sudo apt install openssh-server
```

其中：

```text
OpenSSH
= Open Secure Shell
```

安装完成后检查：

```bash
systemctl status ssh
```

却发现：

```text
ssh.service
inactive
```

看起来像 SSH 没启动。

因此继续检查实际监听机制。

---

# 11. 检查 SSH Socket Activation

执行：

```bash
systemctl status ssh.socket
```

发现：

```text
active (listening)
```

说明当前 Ubuntu 使用了 systemd 的：

```text
socket activation
```

进一步查看 TCP 监听端口：

```bash
ss -lnt
```

拆解：

```text
ss
= socket statistics
查看 socket 状态

-l
= listening
只显示监听状态

-n
= numeric
直接显示数字 IP 和端口

-t
= TCP
只查看 TCP socket
```

看到：

```text
:22
```

说明 TCP 22 端口正在监听。

SSH 默认使用：

```text
TCP 22
```

当前机制：

```text
ssh.socket
    ↓
监听 TCP 22
    ↓
收到新的 SSH 请求
    ↓
systemd 启动对应服务
```

所以：

```text
ssh.service inactive
```

并不意味着：

```text
SSH 不可用
```

而是因为：

> 当前系统通过 socket activation 按需启动 SSH 服务。

---

# 12. 查看 Ubuntu 网络接口和路由

前面已经在 VMware 中为 Ubuntu 虚拟机配置了两块虚拟网卡：

```
网络适配器 1
→ NAT（Network Address Translation，网络地址转换）

网络适配器 2
→ Host-Only（仅主机模式）
```

这样配置的目的，是将两类网络通信分开：

```
NAT
→ 负责 Ubuntu 访问 Internet

Host-Only
→ 负责 Windows ↔ Ubuntu 之间的本地开发通信
```

但 VMware 中配置的是“虚拟网卡采用什么网络模式”，进入 Ubuntu 后，我们还需要确认这些虚拟网卡分别对应 Linux 中的哪个网络接口。

## 12.1 查看网络接口和 IP 地址

首先执行：

```
ip addr
# ip：Linux 网络配置与查看工具
# addr = address，查看网络接口及其 IP 地址
```

当前主要存在两块网络接口：

```
ens33
192.168.153.132

ens34
192.168.226.129
```

因此现在知道 Ubuntu 中存在：

```
ens33 → 192.168.153.132
ens34 → 192.168.226.129
```

但是需要注意：

> **仅通过** `**ip addr**` **只能知道网卡名称和 IP 地址，不能直接判断哪块网卡是 NAT、哪块网卡是 Host-Only。**

要确定它们各自的作用，还需要继续查看路由。

## 12.2 查看路由表，确定默认出网接口

执行：

```
ip route
# route = 路由
# 查看 Linux 当前的路由规则，即数据包应该通过哪个网络接口发送
```

其中可以看到：

```
default via 192.168.153.2 dev ens33
```

将这条路由拆开：

```
default
→ 默认路由
→ 当目标没有其他更具体的路由规则匹配时，使用这条规则

via 192.168.153.2
→ via = 经由
→ 将数据包交给下一跳 192.168.153.2

dev ens33
→ dev = device
→ 从 ens33 网络接口发送
```

所以：

```
default via 192.168.153.2 dev ens33
```

整体表示：

> 当 Ubuntu 要访问一个没有其他更具体路由规则匹配的目标时，数据包默认从 `ens33` 发出，并交给网关 `192.168.153.2`。

Internet 上的绝大多数目标都不属于当前本地网络，因此 Ubuntu 访问 Internet 时会使用这条默认路由。

于是可以确认：

```
ens33
→ 承担 Ubuntu 的默认出网流量
→ 与 VMware 中配置的 NAT 网卡相对应
```

即：

```
ens33：192.168.153.132
→ VMware NAT
→ Ubuntu 访问 Internet
```

## 12.3 区分本机 IP 与默认网关 IP

这里存在两个非常容易混淆的地址：

```
192.168.153.132
→ ens33 自己的 IP 地址

192.168.153.2
→ NAT 网络中的默认网关地址
```

因此：

```
default via 192.168.153.2 dev ens33
```

**并不是说** `**ens33**` **的 IP 地址是** `**192.168.153.2**`**。**

`ens33` 自己的地址仍然是：

```
192.168.153.132
```

而：

```
192.168.153.2
```

是 Ubuntu 要离开当前网络时，把数据包交给的**下一跳网关**。

可以简单记成：

> `**.132**` **是本机，**`**.2**` **是网关。**

Ubuntu 访问 Internet 时的数据流大致为：

```
Ubuntu VM
ens33：192.168.153.132
        │
        │ 从 ens33 发出
        ↓
VMware NAT 默认网关
192.168.153.2
        │
        │ NAT 转发
        ↓
     Internet
```

其中：

```
NAT
= Network Address Translation
= 网络地址转换
```

在当前 VMware 环境中，NAT 使虚拟机能够使用自己的私有 IP，通过 VMware 的 NAT 网络借助宿主机网络访问外部 Internet。

## 12.4 确认 Host-Only 网卡

另一块网络接口为：

```
ens34
192.168.226.129
```

它没有承担默认路由，而位于另一个网络：

```
192.168.226.0/24
```

结合之前 VMware 中配置的第二块 **Host-Only** 虚拟网卡，可以初步对应：

```
VMware Host-Only 网卡
        ↕
Ubuntu ens34
192.168.226.129
```

随后再通过 Windows 实际进行连通性验证。

在 Windows 中执行：

```
ping 192.168.226.129
# ping：测试目标主机是否能够通过 IP 网络到达
```

能够正常收到 Ubuntu 的响应。

说明：

```
Windows
   ↕
192.168.226.129
   ↕
Ubuntu ens34
```

之间可以直接通信。

随后又执行：

```
ssh zhen@192.168.226.129
# ssh = Secure Shell
# 通过 SSH 远程登录 Ubuntu
```

同样能够成功连接 Ubuntu。

Windows 在该 Host-Only 网络中的地址为：

```
192.168.226.1
```

因此这条通信链路为：

```
Windows
192.168.226.1
        │
        │ VMware Host-Only
        ↕
Ubuntu ens34
192.168.226.129
```

由此确认：

```
ens34：192.168.226.129
→ VMware Host-Only
→ Windows ↔ Ubuntu 本地开发通信
```

## 12.5 完整判断过程

所以我们并不是通过 `ip addr` 看到两个 IP 后，直接猜测哪块网卡属于 NAT。

实际判断过程是：

```
VMware 虚拟机设置
        │
        ├── 配置一块 NAT 网卡
        │
        └── 配置一块 Host-Only 网卡
        ↓
ip addr
        ↓
发现：
ens33 = 192.168.153.132
ens34 = 192.168.226.129
        ↓
ip route
        ↓
发现：
default via 192.168.153.2 dev ens33
        ↓
确认 ens33 承担默认出网流量
        ↓
结合 VMware 配置
        ↓
确认 ens33 对应 NAT
        ↓
Windows ping 192.168.226.129 成功
        ↓
Windows SSH 192.168.226.129 成功
        ↓
确认 ens34 对应 Host-Only
```

因此最终得到：

```
ens33
192.168.153.132
→ NAT（Network Address Translation，网络地址转换）
→ 默认网关 192.168.153.2
→ 负责 Ubuntu 访问 Internet

ens34
192.168.226.129
→ Host-Only（仅主机模式）
→ Windows 侧地址 192.168.226.1
→ 负责 Windows ↔ Ubuntu 本地开发通信
```

## 12.6 最终网络结构

整个虚拟机网络结构可以表示为：

```
                    Internet
                       ↑
                       │
                 NAT 地址转换
                       ↑
                       │
                192.168.153.2
               VMware NAT 网关
                       ↑
                       │
                     ens33
                192.168.153.132
                       │
                  ┌─────────┐
                  │ Ubuntu  │
                  │   VM    │
                  └─────────┘
                       │
                     ens34
                192.168.226.129
                       │
                       │
              VMware Host-Only
                       │
                       ↕
                192.168.226.1
                    Windows
```

两块网卡进行了明确的职责分离：

```
ens33
192.168.153.132
→ NAT
→ 192.168.153.2 默认网关
→ Internet
→ 解决 Ubuntu 上网

ens34
192.168.226.129
→ Host-Only
→ Windows 192.168.226.1
→ Windows ↔ Ubuntu
→ 解决宿主机与虚拟机之间的开发通信
```

最终可以简单记忆为：

> `**ens33**` **管出网，**`**ens34**` **管主机通信。**
>
> `**192.168.153.132**` **是 Ubuntu 的 NAT 网卡地址，**`**192.168.153.2**` **是 NAT 默认网关，两者不要混淆。**

同时，这里的判断依据也可以概括为：

> **VMware 设置负责确定“配置了什么网络模式”，**`**ip addr**` **负责确定“Linux 中有哪些网卡和 IP”，**`**ip route**` **负责确定“数据默认从哪里走”，最后再通过 Windows 的** `**ping**` **和** `**ssh**` **实际验证 Host-Only 通信。**

---

# 13. Windows 测试 Ubuntu 网络连通性

回到 Windows PowerShell：

```powershell
ping 192.168.226.129
```

其中目标：

```text
192.168.226.129
```

就是 Ubuntu 的 Host-Only 地址。

得到类似：

```text
time<1ms
0% loss
```

说明：

```text
Windows
   ↓
Host-Only Network
   ↓
Ubuntu
```

网络已经连通。

但需要注意：

> `ping` 成功只说明 IP 网络可达，不代表 SSH 服务一定正常。

所以还需要真正测试 TCP 22 上的 SSH。

---

# 14. Windows 第一次 SSH 登录 Ubuntu

在 Windows PowerShell：

```powershell
ssh zhen@192.168.226.129
```

格式：

```text
ssh 用户名@目标主机
```

这里：

```text
zhen
```

是 Ubuntu 用户。

```text
192.168.226.129
```

是 Ubuntu Host-Only IP。

登录成功后看到：

```text
zhen@ubuntu-dev:~$
```

此时虽然终端窗口仍然显示在 Windows 上，但里面执行的命令已经实际运行于：

```text
Ubuntu
```

例如：

```bash
pwd
ls
uname -a
```

都由 Ubuntu 执行。

---

# 15. 安装 VMware Tools，解决主机与虚拟机复制粘贴

最开始 Windows 与 Ubuntu 之间无法方便地复制文本。

Ubuntu 中执行：

```bash
sudo apt install open-vm-tools open-vm-tools-desktop
```

其中：

```text
open-vm-tools
VMware 在 Linux 中使用的开源集成工具

open-vm-tools-desktop
增加桌面环境相关集成功能
```

安装后：

```bash
sudo reboot
```

重启 Ubuntu。

之后 Windows 与 Ubuntu 可以正常复制粘贴。

Ubuntu Terminal 中：

```text
Ctrl + Shift + C
→ Copy

Ctrl + Shift + V
→ Paste
```

不能把：

```text
Ctrl + C
```

当成普通复制。

因为终端中的：

```text
Ctrl + C
```

通常会向前台进程发送：

```text
SIGINT
```

其中：

```text
SIGINT
= Signal Interrupt
= 中断信号
```

常用于中止当前正在运行的程序。

---

# 16. Windows 配置 SSH Host 别名

原本每次登录都需要：

```powershell
ssh zhen@192.168.226.129
```

IP 和用户名比较长，因此配置 SSH Client。

编辑 Windows 文件：

```text
C:\Users\<Windows用户名>\.ssh\config
```

加入：

```sshconfig
Host ubuntu-dev
    HostName 192.168.226.129
    User zhen
```

其中：

```text
Host
自己定义的连接别名

HostName
真正连接的 IP 或域名

User
SSH 登录时使用的远端用户名
```

保存后：

```powershell
ssh ubuntu-dev
```

即可代替：

```powershell
ssh zhen@192.168.226.129
```

---

# 17. 配置 Windows → Ubuntu SSH 密钥认证

前面已经能够通过：

```
ssh ubuntu-dev
```

从 Windows 登录 Ubuntu。

但此时 SSH 默认仍然可能要求输入 Ubuntu 用户 `zhen` 的密码。

为了实现：

```
Windows
   │
   │ SSH 密钥认证
   ↓
Ubuntu
```

接下来配置 SSH Key，使 Windows 可以使用密钥完成身份认证。

## 17.1 首先检查 Windows 是否已经存在 SSH Key

在 Windows PowerShell 中执行：

```
ls ~/.ssh/
# ~：当前 Windows 用户的用户目录
# .ssh：SSH 配置和密钥默认保存目录
```

当前机器已经存在：

```
id_ed25519
id_ed25519.pub
```

其中：

```
id_ed25519
→ 私钥（Private Key）
→ 只能保存在 Windows 本机
→ 不能发送给其他机器或公开

id_ed25519.pub
→ 公钥（Public Key）
→ 可以复制到需要登录的服务器
```

因此本次**不需要重新执行** `**ssh-keygen**`。

原因是如果已经存在：

```
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

再次生成同名密钥时可能出现覆盖提示；如果误操作覆盖旧密钥，原来依赖这套密钥的认证关系可能失效。

## 17.2 如果 Windows 还没有 SSH Key

如果执行：

```
ls ~/.ssh/
```

发现不存在：

```
id_ed25519
id_ed25519.pub
```

则需要先在 Windows 中生成一套 SSH Key。

执行：

```
ssh-keygen -t ed25519
# ssh-keygen：SSH 密钥生成工具
# -t = type，指定密钥算法类型
# ed25519：现代公钥签名算法
```

随后会询问密钥保存位置，例如：

```
Enter file in which to save the key
```

如果没有特殊需求，直接按 Enter，使用默认位置：

```
C:\Users\<Windows用户名>\.ssh\id_ed25519
```

之后还可能询问：

```
Enter passphrase
```

这是给**私钥本身额外设置保护密码**。

可以设置 passphrase；如果设置，以后使用私钥时可能需要输入它，或者配合 SSH Agent 缓存。

完成后：

```
ls ~/.ssh/
```

应该能够看到：

```
id_ed25519
id_ed25519.pub
```

于是 Windows 上生成了一对密钥：

```
Windows ~/.ssh/
│
├── id_ed25519
│   └── 私钥
│       只保留在 Windows
│
└── id_ed25519.pub
    └── 公钥
        可以复制给 Ubuntu
```

## 17.3 查看 Windows 公钥

在 Windows PowerShell 中执行：

```
Get-Content ~/.ssh/id_ed25519.pub
# Get-Content：PowerShell 中读取文件内容
# 这里只读取 .pub 公钥文件
```

会得到一整行公钥内容，大致形如：

```
ssh-ed25519 AAAA... Windows用户@主机
```

需要复制的是**整行公钥**。

注意：

> 复制的是 `id_ed25519.pub`，不是 `id_ed25519`。

也就是：

```
id_ed25519      → 私钥 → 不复制
id_ed25519.pub  → 公钥 → 复制到 Ubuntu
```

## 17.4 将 Windows 公钥加入 Ubuntu

登录 Ubuntu 后，先确保 SSH 配置目录存在：

```
mkdir -p ~/.ssh
# mkdir = make directory，创建目录
# -p：目录已经存在时不报错，同时可以创建缺失的父目录
```

然后编辑：

```
nano ~/.ssh/authorized_keys
# authorized_keys：SSH Server 允许用于登录当前用户的公钥列表
```

将刚才 Windows 中：

```
id_ed25519.pub
```

的**整行公钥**粘贴进去，然后保存退出。

这里：

```
~/.ssh/authorized_keys
```

可以理解为：

```
authorized_keys
= 已授权公钥列表
```

它属于当前 Ubuntu 用户。

例如当前登录用户是：

```
zhen
```

那么：

```
~/.ssh/authorized_keys
```

实际就是：

```
/home/zhen/.ssh/authorized_keys
```

表示：

> 哪些公钥对应的客户端，有资格尝试通过公钥认证登录 Ubuntu 用户 `zhen`。

## 17.5 设置 SSH 文件权限

为了避免 SSH 因权限过宽而拒绝使用这些文件，可以设置：

```
chmod 700 ~/.ssh
# chmod = change mode，修改权限
# 700：只有当前用户可以读取、写入和进入该目录

chmod 600 ~/.ssh/authorized_keys
# 600：只有当前用户可以读取和写入该文件
```

因此：

```
~/.ssh/
→ 700

~/.ssh/authorized_keys
→ 600
```

## 17.6 SSH 密钥认证是怎么发生的

现在结构变成：

```
Windows
│
├── ~/.ssh/id_ed25519
│       │
│       └── 私钥
│           始终留在 Windows
│
└── ~/.ssh/id_ed25519.pub
        │
        │ 公钥内容被复制
        ↓
Ubuntu
│
└── ~/.ssh/authorized_keys
        │
        └── 保存 Windows 公钥
```

之后在 Windows 执行：

```
ssh ubuntu-dev
```

SSH 客户端使用 Windows 上的：

```
~/.ssh/id_ed25519
```

进行密钥认证。

Ubuntu 上的 SSH Server 则读取：

```
~/.ssh/authorized_keys
```

找到对应的公钥，并通过公钥密码学机制验证客户端是否确实持有对应私钥。

因此可以理解为：

```
Windows
持有私钥
id_ed25519
      │
      │ SSH 公钥认证
      ↓
Ubuntu
保存对应公钥
authorized_keys
      │
      ↓
验证 Windows 确实持有对应私钥
      │
      ↓
认证成功
```

需要注意：

> **认证过程中不需要把私钥发送给 Ubuntu。私钥始终保留在 Windows 本机。**

## 17.7 验证免密码登录

回到 Windows PowerShell：

```
ssh ubuntu-dev
```

如果配置正确，可以直接进入：

```
zhen@ubuntu-dev
```

而不再要求输入 Ubuntu 用户密码。

这里使用的：

```
ubuntu-dev
```

来自前面 Windows 中配置的：

```
~/.ssh/config
```

例如：

```
Host ubuntu-dev
    HostName 192.168.226.129
    User zhen
```

所以：

```
ssh ubuntu-dev
```

本质上相当于：

```
ssh zhen@192.168.226.129
```

只不过连接信息已经保存在 SSH Config 中。

## 17.8 区分当前环境中的两套 SSH Key

当前环境中实际上存在**两套完全独立的 SSH Key**。

第一套位于 Ubuntu：

```
Ubuntu
~/.ssh/id_ed25519
```

它用于：

```
Ubuntu
   │
   │ SSH Key
   ↓
GitHub
```

即：

```
Ubuntu → GitHub
```

GitHub 保存 Ubuntu 的**公钥**，Ubuntu 自己保存对应的**私钥**。

第二套位于 Windows：

```
Windows
~/.ssh/id_ed25519
```

它用于：

```
Windows
   │
   │ SSH Key
   ↓
Ubuntu
```

Ubuntu 的：

```
~/.ssh/authorized_keys
```

保存 Windows 对应的**公钥**。

因此完整结构为：

```
第一套：

Ubuntu
~/.ssh/id_ed25519        ← 私钥
        │
        │ SSH authentication
        ↓
GitHub                  ← 保存 Ubuntu 公钥


第二套：

Windows
~/.ssh/id_ed25519        ← 私钥
        │
        │ SSH authentication
        ↓
Ubuntu
~/.ssh/authorized_keys   ← 保存 Windows 公钥
```

虽然两个文件都叫：

```
id_ed25519
```

但它们：

```
所在机器不同
→ Windows / Ubuntu

密钥内容不同
→ 是两套独立生成的密钥

认证方向不同
→ Windows → Ubuntu
→ Ubuntu → GitHub

用途不同
→ 远程登录 Ubuntu
→ GitHub 身份认证
```

所以：

> `**id_ed25519**` **只是默认私钥文件名，不代表它们是同一把密钥。**

最终可以记成：

```
谁要证明自己的身份
→ 谁持有自己的私钥

谁负责验证身份
→ 谁保存对应的公钥
```

在当前环境中就是：

```
Windows → Ubuntu
Windows 持有私钥
Ubuntu 保存 Windows 公钥

Ubuntu → GitHub
Ubuntu 持有私钥
GitHub 保存 Ubuntu 公钥
```

这也是理解 SSH Key 最重要的一条主线。

---

# 18. 配置 VS Code Remote SSH

Windows VS Code 安装：

```text
Remote - SSH
```

然后连接之前配置好的：

```text
ubuntu-dev
```

连接后打开 VS Code Remote Terminal，执行：

```bash
which g++
which cmake
which gdb
git --version
```

其中：

```text
which
```

用于查找：

> 当前 Shell 最终会执行哪个程序。

结果：

```text
/usr/bin/g++
/usr/bin/cmake
/usr/bin/gdb
git version 2.53.0
```

说明当前 VS Code 虽然：

```text
界面运行于 Windows
```

但：

```text
Shell
G++
CMake
GDB
Git
程序运行环境
```

全部来自 Ubuntu。

结构：

```text
Windows
│
└── VS Code
      │
      │ Remote SSH
      ↓
Ubuntu
│
├── Source Code
├── Git
├── CMake
├── G++
├── GDB
└── Program
```

这就是后续主要采用的开发模式。

---

# 19. 创建第一个 CMake + GDB C++ 项目

进入项目目录：

```bash
cd ~/projects
```

建立：

```text
hello-ubuntu/
├── CMakeLists.txt
└── main.cpp
```

可以查看：

```bash
tree
```

---

## 19.1 编写 main.cpp

```cpp
#include <iostream>

int main() {
    std::cout << "Hello, Ubuntu!" << std::endl;
    return 0;
}
```

---

## 19.2 编写 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.20)

project(hello-ubuntu LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello-ubuntu main.cpp)
```

这里曾经误写：

```cmake
cmake_minimum_required(3.20)
```

于是出现错误：

```text
cmake_minimum_required called with unknown argument "3.20"
```

正确格式是：

```cmake
cmake_minimum_required(VERSION 3.20)
```

即：

```text
VERSION
```

不能省略。

---

## 19.3 CMake Configure

在：

```text
~/projects/hello-ubuntu
```

执行：

```bash
cmake -S . -B build
```

拆解：

```text
-S
= Source
指定源码目录

.
当前目录

-B
= Build
指定生成构建文件的目录

build
将构建相关文件放进 build/
```

所以：

```text
CMakeLists.txt
      ↓
cmake -S . -B build
      ↓
读取项目配置
      ↓
生成 Build System
      ↓
build/
```

需要特别区分：

```bash
cmake -S . -B build
```

主要完成：

```text
Configure + Generate
```

而不是：

```text
真正执行全部 C++ 编译
```

---

## 19.4 编译项目

执行：

```bash
cmake --build build
```

这里才是真正开始进行项目构建。

整个关系：

```text
CMakeLists.txt
      ↓
    CMake
      ↓
生成 Build System
      ↓
Make / Ninja 等
      ↓
     G++
      ↓
   Compile
      ↓
    Link
      ↓
hello-ubuntu
```

编译完成后运行：

```bash
./build/hello-ubuntu
```

得到：

```text
Hello, Ubuntu!
```

这里：

```text
./
```

表示：

> 从当前目录开始寻找程序。

因此：

```bash
./build/hello-ubuntu
```

就是执行：

```text
当前目录/build/hello-ubuntu
```

---

## 19.5 配置 Debug 构建

为了让 GDB 获得源码、变量、函数、行号等调试信息，重新执行：

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
```

其中：

```text
-D
≈ Define
给 CMake 定义一个变量
```

这里定义：

```text
CMAKE_BUILD_TYPE=Debug
```

即使用 Debug 构建。

然后重新构建：

```bash
cmake --build build
```

Debug 构建通常会使编译器保留：

```text
-g
```

等调试信息。

---

## 19.6 启动 GDB

执行：

```bash
gdb ./build/hello-ubuntu
```

其中：

```text
GDB
= GNU Debugger
```

后面：

```text
./build/hello-ubuntu
```

是要被调试的目标程序。

启动后进入：

```text
(gdb)
```

说明当前已经进入 GDB 命令环境。

---

## 19.7 设置断点

输入：

```gdb
break main
```

简写：

```gdb
b main
```

作用：

> 在 `main()` 附近设置断点。

然后启动程序：

```gdb
run
```

简写：

```gdb
r
```

程序执行到断点后停止：

```text
Breakpoint 1, main () at /home/zhen/projects/hello-ubuntu/main.cpp:4

4    std::cout << "Hello, Ubuntu!" << std::endl;
```

这说明：

```text
程序已经进入 main
↓
现在停在第 4 行对应的位置
↓
第 4 行尚未执行完成
```

---

## 19.8 使用 next 单步执行

输入：

```gdb
next
```

简写：

```gdb
n
```

得到：

```text
Hello, Ubuntu!
5    return 0;
```

说明执行顺序为：

```text
GDB 显示第 4 行
       ↓
       n
       ↓
执行第 4 行
       ↓
输出 Hello, Ubuntu!
       ↓
停到第 5 行
```

因此在当前这种 Debug 构建下，可以近似理解：

> GDB 当前显示的源码行通常是接下来准备执行的位置。

但更严格地说：

> CPU 实际停在某条机器指令地址上，GDB 再通过调试信息将该地址映射回源码行。

所以不能永远机械理解为：

```text
一行源码 = 一条机器指令
```

尤其开启编译优化后，源码行和机器指令之间的映射会变复杂。

---

## 19.9 查看调用栈

执行：

```gdb
backtrace
```

简写：

```gdb
bt
```

其中：

```text
backtrace
= 向后追踪
```

用于查看：

> 当前函数是经过怎样的调用链到达这里的。

当前小程序可能只有：

```text
#0 main()
```

以后复杂程序中可能看到：

```text
#0 parseRequest()
#1 handleClient()
#2 runServer()
#3 main()
```

如果程序崩溃：

```text
bt
```

通常是首先应该执行的 GDB 命令之一。

---

## 19.10 继续运行程序

执行：

```gdb
continue
```

简写：

```gdb
c
```

作用：

> 从当前位置继续运行，直到遇到下一个断点、异常或程序结束。

正常结束时可能看到：

```text
Inferior 1 exited normally
```

其中：

```text
inferior
```

是 GDB 对：

> 被调试程序 / 被调试进程

的称呼。

---

## 19.11 退出 GDB

执行：

```gdb
quit
```

简写：

```gdb
q
```

回到普通 Shell：

```text
zhen@ubuntu-dev:~/projects/hello-ubuntu$
```

---

# 20. 永久开启 GDB debuginfod

每次第一次启动 GDB 时出现：

```text
This GDB supports auto-downloading debuginfo from the following URLs:

https://debuginfod.ubuntu.com

Enable debuginfod for this session? (y or [n])
```

输入：

```text
y
```

只会对：

```text
当前这一次 GDB 会话
```

生效。

GDB 自己提示：

```text
To make this setting permanent,
add 'set debuginfod enabled on' to .gdbinit.
```

因此执行：

```bash
echo 'set debuginfod enabled on' >> ~/.gdbinit
```

拆解：

```bash
echo 'set debuginfod enabled on'
# 将这段字符串输出到标准输出

>>
# 将输出追加到后面的文件，而不是覆盖

~/.gdbinit
# 当前用户的 GDB 初始化配置文件
```

当前：

```text
~
=
/home/zhen
```

所以实际文件是：

```text
/home/zhen/.gdbinit
```

以后执行：

```bash
gdb ...
```

GDB 会自动读取该配置。

因此：

```text
~/.gdbinit
```

可以理解为：

> 当前 `zhen` 用户的 GDB 全局配置。

它会影响：

```text
~/projects/hello-ubuntu
~/projects/netdict
以后其他所有项目
```

但不会影响：

> Ubuntu 中其他用户的 GDB 配置。

---

# 附录：可以顺便完成的环境清理

## A. 查看磁盘和虚拟光驱

执行：

```bash
lsblk
```

其中：

```text
lsblk
≈ list block devices
列出块设备
```

当时可以看到：

```text
sda
sr0
sr1
loop...
```

其中：

```text
sda
```

是 Ubuntu 的虚拟硬盘。

而：

```text
sr0
sr1
```

是虚拟 CD/DVD 光驱。

其中一个：

```text
sr1  6G
```

对应 Windows 上的 Ubuntu 安装 ISO。

这里的：

```text
6G
```

表示虚拟光盘容量。

并不意味着：

> Ubuntu 的 60GB 虚拟硬盘又被占用了 6GB。

实际 ISO 文件仍位于 Windows，例如：

```text
C:\iso\ubuntu-....iso
```

VMware 只是将这个文件模拟成一张光盘挂给 Ubuntu。

安装完成后，可以关闭虚拟机，在 VMware 设置中断开或删除这两个虚拟 CD/DVD 设备。

---

## B. 检查 Wine Snap 残留

之前为了运行 Notepad++，Ubuntu 中安装了 Wine 相关 Snap。

删除 Notepad++ 后检查：

```bash
snap list | grep -i wine
```

拆解：

```text
snap list
列出已安装的 Snap 软件包

|
Pipe
管道，将左边命令的输出作为右边命令的输入

grep
按照文本模式筛选内容

-i
ignore case
忽略大小写

wine
搜索包含 wine 的行
```

得到：

```text
wine-platform
wine-platform-runtime-core24
```

既然已经不打算在 Ubuntu 上运行 Windows 程序，就删除：

```bash
sudo snap remove wine-platform

sudo snap remove wine-platform-runtime-core24
```

再次检查：

```bash
snap list | grep -i wine
```

如果：

```text
没有任何输出
```

说明 Wine Snap 残留已经清理完成。

---

# 最终开发环境结构

完成以上配置后：

```text
Windows
│
├── VS Code
├── SSH Client
│
└── VMware
      │
      └── Ubuntu 26.04.1
            │
            ├── GCC / G++
            ├── GNU Make
            ├── CMake
            ├── Git
            ├── GDB
            ├── strace
            ├── lsof
            └── OpenSSH Server
```

网络：

```text
                    Internet
                       ↑
                       │
                 ens33 / NAT
                       │
                  Ubuntu VM
                       │
              ens34 / Host-Only
                       │
                       ↓
                    Windows
```

开发：

```text
Windows VS Code
      │
      │ Remote SSH
      ▼
Ubuntu
      │
      ├── Source Code
      │
      ├── Git
      │
      ├── CMake
      │
      ├── G++
      │
      ├── GDB
      │
      └── Program
```

---

# 本次核心命令速查

## 系统信息

```bash
whoami
# 当前用户

hostname
# 当前主机名

hostnamectl
# 系统综合信息

uname -m
# CPU / 系统架构

echo $HOME
# 当前用户家目录
```

## 软件管理

```bash
sudo apt update
# 更新软件包索引

sudo apt upgrade
# 升级已安装软件

sudo apt install <package>
# 安装软件

sudo apt autoremove
# 删除无用依赖

sudo apt clean
# 清理 apt 缓存
```

## 网络

```bash
ip addr
# 查看网卡和 IP

ip route
# 查看路由表

ss -lnt
# 查看正在监听的 TCP socket
```

## SSH

```bash
ssh user@host
# SSH 登录远程主机

ssh ubuntu-dev
# 使用 Windows ~/.ssh/config 中定义的别名登录

ssh -T git@github.com
# 测试 GitHub SSH 身份认证

ssh-keygen -t ed25519 -C "comment"
# 生成 Ed25519 SSH Key
```

## Git

```bash
git --version

git config --global --list

git config --global init.defaultBranch main
```

## 查找程序

```bash
which g++

which cmake

which gdb
```

## 查看目录

```bash
tree
```

## CMake

```bash
cmake -S . -B build
# Configure + Generate

cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
# 生成 Debug 构建配置

cmake --build build
# 真正执行构建
```

## 运行程序

```bash
./build/hello-ubuntu
```

## GDB

启动：

```bash
gdb ./build/hello-ubuntu
```

GDB 内：

```gdb
b main
# break main
# 在 main 设置断点

r
# run
# 启动程序

n
# next
# 执行当前源码行并停到下一源码位置

bt
# backtrace
# 查看调用栈

c
# continue
# 继续运行

q
# quit
# 退出 GDB
```

永久开启 debuginfod：

```bash
echo 'set debuginfod enabled on' >> ~/.gdbinit
```

---

# 整个搭建过程的主线

```text
whoami / hostnamectl / uname
            ↓
      确认 Ubuntu 环境
            ↓
   apt update / upgrade
            ↓
      build-essential
            ↓
 git / curl / wget / tree / cmake
            ↓
      gdb / strace / lsof
            ↓
         Git 配置
            ↓
        ssh-keygen
            ↓
      Ubuntu → GitHub
            ↓
      openssh-server
            ↓
 systemctl / ss -lnt
            ↓
    ip addr / ip route
            ↓
 Windows ping Ubuntu
            ↓
 Windows ssh Ubuntu
            ↓
       SSH Config
            ↓
      SSH Key 免密
            ↓
  VS Code Remote SSH
            ↓
   hello-ubuntu 项目
            ↓
 cmake -S . -B build
            ↓
  cmake --build build
            ↓
 ./build/hello-ubuntu
            ↓
          GDB
            ↓
   b → r → n → bt → c → q
```

---

# 最终总结

这次真正完成的不是简单的：

> “在 Ubuntu 里安装几个软件”。

而是完整建立了一条 Linux C++ 开发链：

```text
Windows
   ↓
SSH / VS Code Remote SSH
   ↓
Ubuntu
   ↓
Git
   ↓
CMake
   ↓
Build System
   ↓
G++
   ↓
Executable
   ↓
Run / GDB
```

以后进行：

```text
C++ 工程
Linux 系统编程
Socket 网络编程
线程 / 进程
服务器项目
netdict
```