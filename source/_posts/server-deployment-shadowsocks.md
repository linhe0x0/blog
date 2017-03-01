title: 2、shadowsocks 部署和配置
tags: 服务器部署
categories: 技术
date: 2015-10-20 23:38:06
updated: 2015-10-20 23:38:06
---


关于 `shadowsocks` 是什么就不多介绍了，这是[官网](http://shadowsocks.org/en/index.html)，我们前面做了这么多就是为了它，闲话不多说，直接开始搭建服务吧。

### 服务端配置:

首先请确保你的服务器中已经安装了 `Python 2.6 or 2.7`，然后执行以下命令进行安装:

```
apt-get install python-pip
pip install shadowsocks

```

<!-- more -->

然后？然后就安装完成了啊。什么？你不确定？那你可以输入 `ssserver --version` 进行确认，如果安装成功，则会反馈程序的当前版本号。

```
$ ssserver --version
Shadowsocks 2.8.2
```

接下来，你可以通过 `ssserver --help` 查看该命令的用法。

```
$ ssserver --help
usage: ssserver [OPTION]...
A fast tunnel proxy that helps you bypass firewalls.

You can supply configurations via either config file or command line arguments.

Proxy options:
  -c CONFIG              path to config file
  -s SERVER_ADDR         server address, default: 0.0.0.0
  -p SERVER_PORT         server port, default: 8388
  -k PASSWORD            password
  -m METHOD              encryption method, default: aes-256-cfb
  -t TIMEOUT             timeout in seconds, default: 300
  --fast-open            use TCP_FASTOPEN, requires Linux 3.7+
  --workers WORKERS      number of workers, available on Unix/Linux
  --forbidden-ip IPLIST  comma seperated IP list forbidden to connect
  --manager-address ADDR optional server manager UDP address, see wiki

General options:
  -h, --help             show this help message and exit
  -d start/stop/restart  daemon mode
  --pid-file PID_FILE    pid file for daemon mode
  --log-file LOG_FILE    log file for daemon mode
  --user USER            username to run as
  -v, -vv                verbose mode
  -q, -qq                quiet mode, only show warnings/errors
  --version              show version information

Online help: <https://github.com/shadowsocks/shadowsocks>
```

比如说你可以这样启动服务:

```
ssserver -p 8388 -k password -m aes-256-cfb
```

当然了，每次都在命令行上配置是很麻烦的，所幸 `ssserver` 还支持以配置文件方式启动。比如说，我们在 `~/.config/shadowsocks/` 文件夹下创建一个 `config.json` 文件作为 `ssserver` 的启动配置文件(文件位置随意，放在什么地方都无所谓)， `config.json` 文件内容如下:

```
{
    "timeout":600,
    "method":"aes-256-cfb",
    "local_port":1080,
    "server":"0.0.0.0",
    "server_port":8388,
    "password":"password"
}
```
以上参数很简单，就不过多解释了，保存好文件之后执行以下命令启动服务即可:

```
ssserver -c ~/.config/shadowsocks/config.json
```

如果想在后台运行，可以带上 `nohup` 命令:

```
nohup ssserver -c ~/.config/shadowsocks/config.json &
```

当然了，还有更高级的进程管理技巧，这个下一篇文章里会详细介绍。

上面所说的 `config.json` 文件中还支持配置多端口，我们可以利用这个特性达到多账号使用的效果。配置方式为:

```
{
    "timeout":600,
    "method":"aes-256-cfb",
    "local_port":1080,
    "server":"0.0.0.0",
    "port_password": {
        "8388":"password1",
        "8389":"password2"
    },
    "_comment": {
        "8388":"描述一，可以用来记录端口备注或是对应的用户",
        "8389":"描述二，可以用来记录端口备注或是对应的用户"
    }
}
```
因为 `json` 文件不支持注释，因此配置文件中扩展了一个 `_comment` 参数用来进行备注，程序会自动忽略掉该参数。

### 客户端配置

相对来说客户端就太简单了，各个平台均有 `GUI` 版本的客户端，只需要下载安装即可。文章最下方提供下载链接。

以 `Mac` 客户端举例，安装完成之后，点击小飞机图标，选择添加服务器，然后将服务端配置中的 `端口号`，`加密方式`，`密码` 填写至对应的一栏中，其中服务器地址一栏要填写服务器的公网 `IP` 地址。之后就可以尽情的徜徉了。
