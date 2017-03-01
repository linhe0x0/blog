title: 谈谈我理解的API接口设计
tags: api
categories: 技术
date: 2015-09-08 13:03:43
updated: 2015-09-08 13:03:43
---

随着web 技术的发展, 前端技术在 web 开发中占着越来越重的比重. 尤其是最近出现的 AngularJS/React 等框架的出现, 让开发 SPA 应用类的开发方式产生了巨大的革新. 在开发过程中, 前后端的数据交互也变得越来越密切. 再加上层出不穷的各种终端设备, 我们迫切需要一个统一的 api 接口规范设计. 主要参考了Github 的 API 设计规范, 我按照自己的理解总结了一下.

<!-- more -->

## 关于协议

为了确保通信安全, 建议API与用户的通信协议，总是使用HTTPS协议.

## 关于域名

为了提高可维护性, 建议尽量将API部署在专用域名之下. 如 `https://api.github.com`

## 关于版本号

建议将 API 的版本号放入 URL 中, 如`https://api.github.com/v1/`. 但是 Github 似乎采用了另外一种方案, 就是将版本号放在了HTTP头信息中, [看这里看这里](https://developer.github.com/v3/media/#request-specific-version). 但个人感觉不是太直观. 但前端在请求中可以省略3个字符. _(:з」∠)_

## 关于请求路径

RESTful API是目前比较成熟的一套互联网应用程序的API设计理论. 在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词. 并且建议所用的名词和数据库中的集合名相对应. 并且在获取一组数据/集合的时候, 应统一使用复数形式.

比如, 需要请求所有的用户信息. 可以设计为这样: `https://api.example.com/v1/users`.

## 关于请求方法

常用的 HTTP请求方法有5个. 分别如下:

| Method | Description |
| ------------- |:-------------:|
| GET | 从服务器取出资源 |
| POST | 在服务器新建一个资源 |
| PUT | 用于完整的替换资源或者创建指定身份的资源(更新资源) |
| PATCH | 用于局部更新资源 |
| DELETE | 用于删除某个资源 |

比如:

* GET `/users` 列出所有的用户
* GET `/users/:ID` 列出指定用户的信息
* POST `/users` 新建一个用户
* DELETE `/users/:ID` 删除指定的用户

也可以使用语义化的路径方式进行分类, 如:
* GET `/users` 列出所有的用户
* POST `/users/create` 创建一个新用户
* POST `/users/update` 更新信息

## 关于常见的请求参数

比如在数据过多, 需要对数据进行分页请求的时候, 我们应该统一 API 请求参数. 常见的有这些.

* `limit=10` 指定返回记录的数量
* `offset=10` 指定返回记录的开始位置。
* `page=2&per_page=100` 指定第几页，以及每页的记录数。
* `sortby=name&order=asc` 指定返回结果按照哪个属性排序，以及排序顺序。
* `animal_type_id=1` 指定筛选条件

## 关于返回值

返回值的数据格式应严格按照 JSON 规范.JSON（JavaScript Object Notation）是一种轻量级，基于文本，语言无关的数据交换格式。其包括了基本数据类型4种和复合数据类型2种，共6种数据类型.

首先. 基本数据类型有:

* Number可以表示整数和浮点数。
* Boolean可以表示真假，值为true或false。
* String表示一个字符串。
* Null通常用于表示空对象. 输出的数据结构中空字段的值一律为 null.

复合数据类型Object是无序的集合，以键值对的方式保持数据。

1. 必须根据操作和状态设置相应的 HTTP 响应状态码. 具体可参考[http://tools.ietf.org/html/rfc7231#page-49](http://tools.ietf.org/html/rfc7231#page-49)、[http://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81](http://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)

2. Content-Type字段定义了响应体的类型. 一般情况下，浏览器会根据该类型对内容进行正确的处理。推荐设置为"text/javascript"或"text/plain". 并且通常需要指定一个字符集, 如果字符编码为UTF-8时，可以不指定字符集

3. 返回的数据包含在http响应体中。数据 必须(MUST) 是一个JSON Object。该Object可能包含3个字段：status，statusInfo，data。

4. status字段必须是一个不小于0的JSON Number整数，表示请求的状态. 0：表示server端理解了请求，成功处理并返回. 非0：表示发生错误, 可以根据错误类型扩展错误码.

5. statusInfo字段通常是一个JSON String或JSON Object，表示除了请求状态外server端想要对status做出的说明，使client端能够获取更多信息进行后续处理. 这个字段属于可选字段.

6. data 字段可以是任意 JSON 类型, 表示请求返回的数据主体. 数据主体data包含了在请求成功时有意义的数据.

例如:

{% codeblock %}
// 请求成功
{
    status: 0,
    statusInfo: '请求成功',
    data: {
        name: 'Tom',
        ag: 12
    }
}

// 请求失败
{
    status: 1,
    statusInfo: '权限不足'
    // 或者是复杂类型
    statusInfo: {
        'text': '参数错误',
        'parameters': {
            "email": "电子邮件格式不正确"
        }
    }
}
{% endcodeblock %}

## 关于文档规范

可参考 github 的文档写作形式.

1、 请求信息
```
GET '/users' 获取用户列表 // 请求方式, 请求路径, 方法描述
```

2、 请求参数

| Name | Type | Description |
| --------- | --------- | -------- |
| 参数一 | 参数类型 | 参数描述 |

3、 Response 响应结果示例
给出不同状态的响应结果示例, 便于接口对接和调试.
```
{
    status: 0,
    statusInfo: '请求成功',
    data: [{
        id: 1,
        name: 'Tom'
    }]
}
```
