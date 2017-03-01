title: 1、SSH 登陆配置
tags: 服务器部署
categories: 技术
date: 2015-10-20 13:23:27
updated: 2015-10-20 13:23:27
---

简单说，SSH是一种网络协议，用于计算机之间的加密登录。关于详细的信息呢，各位可自行 Google。

我们在上一步创建好服务器之后，我们可以在 [https://cloud.digitalocean.com/](https://cloud.digitalocean.com/) 查看到服务器列表和 `ip` 地址。

因为我在初始化的过程中没有直接添加 `ssh` 认证登陆，所以我登陆到服务器之后要先自行添加和配置。

<!-- more -->

### 配置公钥登录

1、首先，在收到包含有服务器密码的邮件之后，打开终端(或是命令行)工具，输入 `ssh user@host` 命令进行登录，由于是第一次登陆，所以默认登陆为 `root`账户，例如:

```
ssh root@192.168.0.0
```

2、之后会提示让你输入密码，你需要将邮件中的初始密码输入，注意，输入密码的时候屏幕不会出现任何反馈，会可能会造成一种没有输入的错觉，你只需正常输入并敲击回车表示完成即可。

3、第一次登陆成功之后，会提示让你进行修改密码，修改完密码之后即以 `root` 身份登入服务器。

4、使用密码登录，每次都必须输入密码，非常麻烦。因此我们需要配置公钥登录。首先在本地生成对应的公钥。如果你之前已经生成过，则可以直接跳过这一步。SSH 公钥默认储存在账户的主目录下的 `~/.ssh` 目录。使用一下命令查看:

```
cd ~/.ssh
ls
```
关键是看有没有用 `something` 和 `something.pub` 来命名的一对文件，这个 `something` 通常就是 `id_dsa` 或 `id_rsa`。有 `.pub` 后缀的文件就是公钥，另一个文件则是密钥。假如没有这些文件，或者干脆连 .ssh 目录都没有，可以用 ssh-keygen 来创建，生成的命令为:

```
ssh-keygen
```
它先要求你确认保存公钥的位置 `~.ssh/id_rsa`，然后它会让你重复一个密码两次，如果不想在使用公钥的时候输入密码，可以留空。

5、拿到公钥之后，就要将公钥上传到服务器。谨记: 一定要在本地保管好你的私钥，而公钥可以发布到各服务器上。

`Linux` 用户可以使用 `ssh-copy-id user@host`进行上传，`Mac` 用户则需要麻烦一点:

```
scp ~/.ssh/id_rsa.pub user@host:~/.ssh/
ssh user@host
cd ~/.ssh/
cat id_rsa.pub >> authorized_keys
```
如果服务器 `.ssh` 目录下没有 `authorized_keys` 文件，则可以创建一个。

基本上到这里就算是配置完成了，你可以退出然后重新登录一下测试是否配置成功。

### SSH 配置

> SSH 的配置均通过修改 `/etc/ssh/sshd_config` 配置文件实现，例如: `vim /etc/ssh/sshd_config`。**修改配置时最好进行备份操作**

> 修改完之后如果想要立即生效需要重启SSH服务: `sudo service ssh restart`

> 更多的 SSH 服务命令可阅读文章下方的参考资料。


1、建议修改ssh登录端口，修改端口可以防止被端口扫描。找到 `Port 22`，修改成一个五位的端口：`Port 22222` 之类的一个比较大的数字，减小被扫描到的可能性。*建议先添加一个端口，待确认能够通过新添加的端口连接成功，再将原端口注释掉。*

2、为了安全设置，可以选择禁用 `root` 账户的密码登录，设置配置文件中的 `PermitRootLogin` 为 `without-password`。甚至可以直接禁用 `root` 账户登录，新建一个普通用户进行登录。

### 新建普通用户登录

目前我们总是以 `root` 用户的身份进行登录，而 `root` 由于权限太高造成风险也随之升高，因此我们需要在服务器上创建一个普通用户身份以做平时登录使用。

创建普通用户的命令为:
```
sudo adduser newUser
```
这样系统就会自动创建用户主目录，创建用户同名的组，然后屏幕就会出现以下信息:

```
正在添加用户"newUser"…
正在添加新组"newUser" (1006)…
正在添加新用户"newUser" (1006) 到组"newUser"…
创建主目录"/home/newUser"…
正在从"/etc/skel"复制文件…
输入新的 UNIX 口令： (作者注:这里是输入新账号的密码)
重新输入新的 UNIX 口令：
```

两次输入 `newUser` 的初始密码，出现的信息如下:

```
passwd: password updated successfully
Changing the user information for db
Enter the new value, or press ENTER for the default
Full Name []:
Room Number []:
Work Phone []:
Home Phone []:
Other []:
Full Name []:
```
等信息一路回车

```
这个信息是否正确？ [Y/n]
```

致此，新用户就创建好了。

当然了，还有一些事情需要处理。

##### 1、让该用户具有使用 `sudo` 命令的权限
通过编辑配置文件 `/etc/sudoers` 来实现:

```
vim /etc/sudoers

// 然后添加如下内容:

%newUser ALL=(ALL) ALL
newUser ALL=(ALL) ALL
```

保存，退出。深藏功与名。

##### 2、使用普通用户身份进行登录
当然了，你如果想要以此用户的身份进行 `ssh` 登录，还需要将之前的公钥上传至该用户目录下。

### 本地 SSH 配置
> 每次登录都需要输入用户名和 Host，麻烦不？当然麻烦了。

上面讲了辣么多的服务器端配置，这里就来讲一下本地配置。本地的配置文件默认为 `~/.ssh/config`，如果不存在则新创建一个，其配置写法如下:

```
Host    别名
    HostName        主机名
    Port            端口
    User            用户名
    IdentityFile    密钥文件的路径
```

上面的配置可以缺省，不需要每一个都填写。有了这些配置，就可以这样用 `ssh` 登陆服务器了:

```
ssh 别名
```
怎么样？是不是方便了很多呢。 O(∩_∩)O哈哈~

## 参考资料
* [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
* [SSH的配置和命令介绍](http://segmentfault.com/a/1190000002492828#articleHeader4)
* [Ubuntu中useradd和adduser的区别](http://www.kafan.cn/edu/584682.html)
