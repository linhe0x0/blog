title: 0、服务器主机选择和购买
tags: 服务器部署
categories: 技术
date: 2015-10-19 13:28:41
updated: 2015-10-19 13:28:41
---

> 由于某种不能说的原因，导致我们没法使用 Google，作为一个开发人员这是不能容忍的，近日来发现竟然连 [https://gist.github.com/](https://gist.github.com/) 都无法访问，这简直令人发指。于是各种代理服务就如雨后春笋一般出现，但是前段时间某技术升级加上各种手段，导致该关闭的关闭，该封停的封定，现在还有 `shadowsocks` 技术勉强可以维持，但该项目已经不再更新 _(:з」∠)_。

> 有些网站也会提供一些免费的 `shadowsocks` 账号供使用，但我体验了一段时间之后，第一觉得太麻烦，因为这种公共服务基本上每过一段时间就会改一次密码。第二是不太稳定，并且总觉得在私密性上放不下心，就想着自己利用服务器搭建一个私人服务。

因为之前没有接触过服务器端的部署，蹒跚学步的过程中遇到了很多困惑和问题，在这里也做一下记录，写一个 `"服务器部署从入门不一定到精通"` 系列，待多年以后回头来看我是这样被大家看着长大的 O(∩_∩)O哈哈哈~。

<!-- more -->

## 服务器商选择

最初的目的是为了搭建一些服务以达到代理访问外网的目的，因此毋庸置疑的就要选择那些海外服务器。目前口碑最高的是 [Linode](https://www.linode.com/) 和 [digitalocean](https://www.digitalocean.com/?refcode=d2b2550b5631), 至于两者的区别可以参考[http://www.zhihu.com/question/21454090](http://www.zhihu.com/question/21454090) 或者其他资料。总之，我选择了 [digitalocean](https://www.digitalocean.com/?refcode=d2b2550b5631)，如果你也选择这个服务商，点击前面的链接注册后可以获得 **10美金** 的奖励，自己搜索注册可没有哟~

## 购买服务器

注册完账号之后，访问 [https://cloud.digitalocean.com/droplets/new](https://cloud.digitalocean.com/droplets/new) 即可选购一个新服务器。

0、绑定支付方式

选购之前需要绑定支付方式才能进行购买，方式有两种，一种是绑定信用卡，一种是通过 `paypal` 支付 5 美元。

1、填写 Droplet Hostname

给你的服务器起一个名字。

2、Select Size

`digitalocean` 最低提供 `5美金/月` 的套餐，并且是按小时扣费。如果你只是为了搭建一个代理服务， 这个套餐足够使用了。

3、Select Region

`digitalocean` 目前提供7个城市的机房可选，其中 `New York` 和 `Amsterdam` 各有三个机房，其他地区各一个机房可选。大陆用户建议选择美国西海岸机房，因为优化做的很棒，访问速度还可以。新推出的新加坡机房虽然在亚洲，但是好像从评价来看速度和优化并不理想。我选择了 `San Francisco` 机房。

4、Select Image

小白用户和开发者建议选择 `Ubuntu`，运维和高级人员可以使用 `CentOS`系统。

5、Available Settings

其中 `Backups` 自动备份选项是附加收费业务，每月20%的收费标准。

6、Add SSH Keys

添加 `ssh` 公钥，建议在这里直接添加 `ssh` 公钥，会方便很多。如果你没有选择添加 `ssh keys`，那么在服务器创建好之后会给你发一封包含服务器密码的邮件，你使用该密码第一次登陆服务器时，会要求更改密码。

7、Create Droplet

然后点击 `Create Droplet` 按钮，稍等一分钟你就拥有了一台海外服务器咯。

之后你在 [https://cloud.digitalocean.com/](https://cloud.digitalocean.com/) 就可以查看到你的服务器了。


## 您的鼓励是作者写作最大的动力

如果您认为本网站的文章质量不错，读后觉得收获很大，不妨小额赞助我一下，让我有动力继续写出高质量的文章：我的支付宝账号是 `sqrtthree@foxmail.com`, [点击查看二维码](http://7xl8me.com1.z0.glb.clouddn.com/alipay.JPG)
