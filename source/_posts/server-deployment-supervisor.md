title: 3、Supervisor 部署和配置
tags: 服务器部署
categories: 技术
date: 2015-10-21 00:08:41
updated: 2015-10-21 00:08:41
---


[Supervisor](http://supervisord.org/) 是一个用来监控和控制多个服务进程的命令行程序，我们可以用来方便的进行后台进程管理，比如说异常退出时能自动重启。

### 安装

`Ubuntu` 可以直接通过 `apt` 安装：

```
apt-get install supervisor
```

<!-- more -->

### 配置文件详解

`supervisor` 通过对应的配置文件来进行进程的监控和管理，因此安装完成之后我们需要给将要监控的进程写一个配置文件。每个进程的配置文件都建议单独分拆，放在 `/etc/supervisor/conf.d/` 目录下，以 `.conf` 作为扩展名。如我们的 `shadowsocks.conf` 配置文件。

下面是配置文件的格式和支持的参数，只需要写我们需要配置的参数即可。

```
[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
;serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
;username=chris              ; should be same as http_username if set
;password=123                ; should be same as http_password if set
;prompt=mysupervisor         ; cmd line prompt (default "supervisor")
;history_file=~/.sc_history  ; use readline history if available
添加单个进程

; 管理单个进程的配置，可创建多个，下面是所有可能的配置选项
;[program:theprogramname]
;command=/bin/cat              ; 启动进程的命令 使用相对路径，可以加参数
;process_name=%(program_name)s ; 进程名称 表达式 (默认 %(program_name)s)
;numprocs=1                    ; 进程数目 (def 1)
;directory=/tmp                ; 执行命令所在的目录 (def no cwd)
;umask=022                     ; 进程默认权限 (default None)
;priority=999                  ; 进程启动相对优先权 (default 999)
;autostart=true                ; 跟随supervisor启动时启动 (default: true)
;autorestart=unexpected        ; 计划启动 (default: unexpected)
;startsecs=1                   ; 延时启动 (def. 1)
;startretries=3                ; 最多连续启动失败 (default 3)
;exitcodes=0,2                 ; 进程结束代码 (default 0,2)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; 最长结束等待时间，否则使用 SIGKILL (default 10)
;stopasgroup=false             ; 是否想UNIX进程组发送结束信号 (default false)
;killasgroup=false             ; SIGKILL UNIX 进程组 (def false)
;user=chrism                   ; 设置启动此程序的用户
;redirect_stderr=true          ; 重定向程序的标准错误到标准输出 (default false)
;stdout_logfile=/a/path        ; 标准输出的日志路径, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; 日志文件最大值，否则循环写入 (default 50MB)
;stdout_logfile_backups=10     ; 标准输出日志备份数目 (default 10)
;stdout_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stderr_logfile=/a/path        ; 标准错误输出日志路径, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; 日志文件最大值，否则循环写入 (default 50MB)
;stderr_logfile_backups=10     ; 标准错误日志备份数目 (default 10)
;stderr_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;environment=A="1",B="2"       ; 进程附加环境 (def no adds)
;serverurl=AUTO                ; override serverurl computation (childutils)
```

例如，可以简单的配置成:

```
[program:long_script]
command=/usr/local/bin/long.sh
autostart=true
autorestart=true
stderr_logfile=/var/log/long.err.log
stdout_logfile=/var/log/long.out.log
```

编写完配置文件之后，通过 `service supervisor restart` 命令重启 `supervisor` 服务，也可以通过以下方式进行温柔的重启服务和进程:

```
supervisorctl reread
supervisorctl update
```

### 命令行工具 supervisorctl
查看所有命令:

```
supervisorctl help

default commands (type help ):
=====================================
add    clear  fg        open  quit    remove  restart   start   stop  update
avail  exit   maintail  pid   reload  reread  shutdown  status  tail  version
```
常用命令的功能:

```
supervisorctl stop programName，停止某一个进程(programName)，programName为[program:chatdemon]里配置的值。
supervisorctl start programName，启动某个进程
supervisorctl restart programName，重启某个进程
supervisorctl stop groupworker: ，重启所有属于名为groupworker这个分组的进程(start,restart同理)
supervisorctl stop all，停止全部进程，注：start、restart、stop都不会载入最新的配置文件。
supervisorctl reload，载入最新的配置文件，停止原有进程并按新的配置启动、管理所有进程。
supervisorctl update，根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受影响而重启。
```

执行 `supervisorctl` 命令需要管理员权限，如果你以普通用户的身份登陆服务器，需要在命令前使用 `sudo` 命令: 

```
sudo supervisorctl reread
sudo supervisorctl update
```

## 参考资料
* [http://www.liaoxuefeng.com/article/0013738926914703df5e93589a14c19807f0e285194fe84000](http://www.liaoxuefeng.com/article/0013738926914703df5e93589a14c19807f0e285194fe84000)
* [https://www.digitalocean.com/community/tutorials/how-to-install-and-manage-supervisor-on-ubuntu-and-debian-vps](https://www.digitalocean.com/community/tutorials/how-to-install-and-manage-supervisor-on-ubuntu-and-debian-vps)

## 您的鼓励是作者写作最大的动力

如果您认为本网站的文章质量不错，读后觉得收获很大，不妨小额赞助我一下，让我有动力继续写出高质量的文章：我的支付宝账号是 `sqrtthree@foxmail.com`, [点击查看二维码](http://7xl8me.com1.z0.glb.clouddn.com/alipay.JPG)
