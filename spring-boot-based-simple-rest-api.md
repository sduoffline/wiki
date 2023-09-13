---
title: 培训一：基于 Spring boot 的简单 REST API
description: 2022级后端培训文档
published: true
date: 2023-02-22T16:04:12.774Z
tags: 
editor: markdown
dateCreated: 2023-02-22T16:04:09.315Z
---

# 培训一：基于 Spring boot 的简单 REST API

>   “情况到这里变得严重起来。——篠原はるな“

## REST（RESTful） API

RESTful API 是一种应用程序接口（API）的架构风格，它使用 HTTP 请求来访问和使用数据。该数据可用于 GET、PUT、POST 和 DELETE 数据类型，指的是读取、更新、创建和删除有关资源的操作。

Wait, 什么是 API？

### API

**应用程序接口**（英语：**a**pplication **p**rogramming **i**nterface[[1\]](https://zh.wikipedia.org/wiki/应用程序接口#cite_note-刘瑞新2018-1)），缩写为 **API**[[2\]](https://zh.wikipedia.org/wiki/应用程序接口#cite_note-UnmehopaVemuri2006-2)，是一种[计算](https://zh.wikipedia.org/wiki/计算)[接口](https://zh.wikipedia.org/wiki/接口)，它定义多个[软件](https://zh.wikipedia.org/wiki/软件)[中介](https://zh.wikipedia.org/wiki/中介)之间的[交互](https://zh.wikipedia.org/w/index.php?title=交互&action=edit&redlink=1)，以及可以进行的[调用](https://zh.wikipedia.org/w/index.php?title=调用&action=edit&redlink=1)（call）或[请求](https://zh.wikipedia.org/w/index.php?title=请求&action=edit&redlink=1)（request）的种类，如何进行调用或发出请求，应使用的数据格式，应遵循的惯例等。它还可以提供[扩展机制](https://zh.wikipedia.org/w/index.php?title=扩展机制&action=edit&redlink=1)，以便用户可以通过各种方式对现有功能进行不同程度的扩展 [[3\]](https://zh.wikipedia.org/wiki/应用程序接口#cite_note-Fisher1-3)。一个 API 可以是完全定制的，针对某个组件的，也可以是基于行业标准设计的以确保互操作性。通过[信息隐藏](https://zh.wikipedia.org/wiki/面向对象的程序设计)，API 实现了[模块化编程](https://zh.wikipedia.org/wiki/模块化编程)，从而允许用户实现独立地使用接口。

------

举个例子，当你在 ilibilib 直播平台给虚拟主播“篠原春奈OvO”送礼物时，请思考：

-   刷礼物这个动作是怎么做到的？

缩窄并简化一下情况，假设你是个前端程序员，你看到的“刷礼物”的动作是什么？

事实上，当前端向后端提交了“XXX 给 XXX 主播送了 XXX礼物”的信息时，对于前端而言这个动作就完成了。（请注意，这是个简化情形）

后端系统会调用某个 API 处理这条信息，并对数据进行修改（like, -￥138……）

**简而言之**，你可以理解为 API 就是个供人使用的功能模块。比如 i山大 的查询课表 是个 API，查看志愿时长是个 API，等等。

### REST

什么是 REST API？上面已经解释过了，相信你已经完全理解了。（bushi

你可以简单地将 REST API 认作是：通过 HTTP 请求完成的 API。

Wait，HTTP 请求？

### HTTP 请求

相信大家在网上冲浪的时候，都会做许多操作。但是仔细想一想，这些操作的性质一样吗？

例如：

-   下载一部你喜欢的视频
-   上传艺术品（bushi~~涩图~~
-   登录 i山大
-   ……

如果你有兴趣，可以参见[这篇文档](https://www.runoob.com/http/http-methods.html)，来看到标准的 HTTP 请求。

但是暂时，我们只需要认识 `GET` 与 `POST`。

#### GET

非常简单，直接请求指定页面的信息，返回页面信息。

![GET_example_with_curl](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/GET_example_with_curl.png)

图中就是一个`GET`的例子。你可以先不考虑什么是`curl`，你只要知道：**我向 baidu.com 发送了 GET 请求**。

看看我得到的信息，是否有一些熟悉？

![baidu_source_code](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/baidu_source_code.png)

这是百度的结果。很显然，如果你向一个 API 发送 `GET` 请求，你就会得到这个 API 该有的回应。

#### POST

>   POST 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和 / 或已有资源的修改。

简单来说，你可以把 `POST` 理解为 `GET` 的加强版。

举个例子，有个网站登录的 API，是 `example.com/login`。这个 API 会要求两个参数，`u` 表示账号，`p` 表示密码。假设它是通过 `GET` 请求的，会怎么样呢？

答案是，你发的请求为：`GET example.com/login?u=shinohara&p=1234`。

对于 `GET` 请求来说，参数的表示方式是：

-   在 API 后跟上一个问号，表示接下来是参数
-   如果有多个参数，中间用 AND 符号（&）连接

看到了吗？`GET` 请求的参数是用明文写在 URL 里的。但是登录密码这种信息真的适合写在这里吗？

显然不适合。

所以我们需要 `POST`，一个需求就是传递密码这种信息。

By the way，用 `POST` 就安全了吗？难道密码不也随着请求发出去了吗？

确实。但是，哦，我的上帝，首先密码从 URL 消失了，任何一个没有计算机基础的人都不能从那该死的地址栏中读取出那该死的密码，呃，我发誓，是真的。其次，that is **exactly** why we **need** `HTTPS`。不过，这回我们不讲这个。

------

`POST` 还有一个好处，就是能发送的信息量更大。显然 `GET` 发送的信息量受制于 URL 的长度，并不能很大。

## 准备工作

什么？前面那么一大堆东西，还没讲到今天的主题吗？

别急，这一 part 也不会讲到。

但是，正所谓“工欲善其事，必先利其器”，我们得做好准备工具。

### Postman

>    “发送 POST 请求的人”，wow，什么鬼名字。

其实这一次培训倒也可以不下载啦……因为我们只会用到 `GET` 请求，这样的话，直接用浏览器就能做到了。

------

在 [这里](https://www.postman.com/downloads/) 下载Postman，也可以在同一页面“Try the Web Version”。

记得注册账号。直接点击[这里](https://identity.getpostman.com/signup)可以注册。

接下来安装登录一条龙。于是……

![Postman_main_screenshot](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Postman_main_screenshot.png)

你能看到类似的界面。至于用法等等其他的，以后再说吧（

### Spring boot

**Spring Boot**为开发提供一个具有最小功能的 Spring 应用程序，并提供了一个新的范例。使用**Spring Boot**将能够以更灵活的方式开发 Spring 应用程序，并且能够通过最小（或可能没有）配置 Spring 来专注于解决应用程序的功能需求。它使用全新的开发模型，通过避免一些繁琐的开发步骤和样板代码和配置，使 Java 开发非常容易。

**Spring Boot**可以轻松创建单独的，生产级的基于 Spring 的应用程序，我们只管“运行”。查看 Spring 平台和第三方库。大多数**Spring Boot**应用程序只需要很少的 Spring 配置。

#### Spring？

可以先有个概念：

- Spring 是核心，提供了基础功能；
- Spring MVC 是基于 Spring 的一个 MVC 框架 ；
- Spring Boot 是为简化Spring配置的快速开发整合包；
- Spring Cloud 是构建在Spring Boot之上的服务治理框架。

但是本次培训，我们只专注于主题：**基于 Spring boot 的简单REST API**

#### Maven

这回就不写官方的定义了（~~懒得打~~）

众所周知，在程序越来越庞大、复杂的今天，一个软件产品完全由一个人独立完成几乎是不可能的。

现代软件开发是模块化的，我们将一些常用的部分打包装起来，以后用到就不用重新写一遍了。比如，如何用 Java 发送 HTTP 请求？

这么重要的需求，肯定有人写好了啊！所以我们就不必关心底层的原理具体是怎么实现的（当然，还是有必要的，不过不是现在），而是专注于本身的逻辑。

这就好比，你想要一座精美的屋子，一种方式是对毛坯房动手，只关心视觉效果所以装修就好了；另一种则是从烧砖开始，一步一步搭建起来。虽然这两种开发方式**没有**优劣之分，但是在这里我们选择第一种。

而 Maven 就是这么一个简化 Java 程序员复用前人代码的方式。

试着使用 [Maven search](https://search.maven.org) 看看？

#### 注解

注解（*Annotation*）是放在 Java 源码的类、方法、字段、参数前的一种特殊 “注释”。但不同于注释，注解是有效果的，注释则会被直接忽略。

比如，`@Override` 就是一个典型的注解。

你也可以看看[这篇文档](https://www.runoob.com/w3cnote/java-annotation.html)来想想注解到底是什么玩意，但是相信我，也许你长期都会想不明白这个东西。

没关系，我们先上手，慢慢就会懂的。换言之，虽然我不知道电脑是怎么造的，我还不会用吗？用着用着就会了。

## 建立 Spring boot 项目

Finally！We are here！

首先打开 IDEA。

![IDEA_new_project](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/IDEA_new_project.png)

三十年河东，三十年河西，这一次，选点不一样的！

于是……

![IDEA_new_springboot_project](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/IDEA_new_springboot_project.png)

先改名字，然后下面的一些选项会跟着修改。例如：

![IDEA_new_springboot_change_name](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/IDEA_new_springboot_change_name.png)

整挺好。再把 location 改为你想放的地方，就可以直接 Next 了。![IDEA_new_springboot_choose_module](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/IDEA_new_springboot_choose_module.png)

只需选择 Spring Web 即可。

------

稍等片刻，你将看到：

![A_new_springboot_project](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_new_springboot_project.png)

Wow, awesome, amazing!

现在，你已经成功写出了你的第一个 Spring boot 程序。

什么？还妹写呢！确实，但它确实是一个简单却完整的 Spring boot 项目。

------

![A_simple_running_springboot_project](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_simple_running_springboot_project.png)

在左侧文件中找到`./src/main/java`，打开到这个以`Application`结尾的程序，点击小绿三角运行，你就将得到如上图所示的东西。

让我们仔细看看里面有啥。

1.   Hmmm，最右边有个 Maven，看到了吗？我们这个 Springboot 项目，是依赖于 Maven 的。
2.   下面终端的输出信息里有一句“Tomcat started on port(s): 8080 (http) with context path ''”，看到了吗？你可以理解为，这个 Springboot 项目的 REST API 运行在`localhost:8080`上。

Wait，`localhost`？好吧，运用你的英语能力，你知道这是本机的意思。

正如上面所说的，REST API 就是 URL，并且我们暂时只 `GET`，那么赶紧用浏览器打开一下吧！

![Empty_springboot_project_error_on_the_browser](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Empty_springboot_project_error_on_the_browser.png)

报错了！还是 404！注意到了吗？404 Not Found。对了，我们还没开始写呢，当然没找到了。

但是我们已经迈出了坚实的一步。

------

![IDEA_new_package_in_springboot_project](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/IDEA_new_package_in_springboot_project.png)

右击`Java`目录下那个灰色的文件夹一样的东西，选择新建 Package。

![New_package_in_springboot](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/New_package_in_springboot.png)

直接在后面加上“controller”，回车。

你将看到，多了一个名为“controller”的文件夹。想必大家看出来了，点号“.”其实表示了一种层级关系，就像一级一级的文件夹。

再右击“controller”文件夹，选择新建 Java Class，这里把它取名为 Main，回车。

你将看到：

![Main_controller_in_springboot_tutorial](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Main_controller_in_springboot_tutorial.png)

突然熟悉起来（

我们要把它作为一个 REST API，不是吗？所以，它是一个……REST 控制器？

![RESTController_annotation_in_springboot](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/RESTController_annotation_in_springboot.png)

在 `Main` 这个 class 上输入 `@RestController` 回车，IDEA 会自动为你导入它。

------

干得漂亮。现在可以说你已经有一个 API 了，让我们继续填充它。

新加一个`helloWorld`方法，并在它的上面加上这句话：`@GetMapping("/")`，智能的 IDEA 同样会为你补全导入。

![helloworld_in_springboot_as_a_rest_controller](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/helloworld_in_springboot_as_a_rest_controller.png)

现在按照刚才的方式运行这个项目，在浏览器中打开`localhost:8080`看看？

![helloworld_in_broswer](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/helloworld_in_broswer.png)

>   完 全 勝 利

复盘一下，我们做了什么呢？

首先，什么是`GetMapping`？后面跟的是什么？

动用你的英语能力，你知道“GetMapping”是“映射Get请求”的意思。后面跟的`"/"`表示这个 REST API 的 URL 就是 `localhost:8080`！

这样，当你访问这个网址的时候，你实际上是在：**GET** `localhost:8080`，那么得到的就是你所返回的字符串。

“你好，世界！”

------

你已经写出了一个 REST API。但是这还不够。

为什么呢？因为这个 API 不接受参数。

现在，我们来做一个 `a+b` 的 API。

![add_API_in_springboot](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/add_API_in_springboot.png)

这会，出现了一个新家伙：`@RequestParam`。“Param”是“Parameter”的缩写。那么，动用你的英语能力，你知道它的意思是“the 参数 of the 请求”，对吗？

还真是。

除此以外，它和我们写 `a+b` 的方法有任何区别吗？没有。

在浏览器地址栏输入：`localhost:8080/add?a=2&b=3`，看看效果。

![The_result_of_add_controller_in_broswer](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_result_of_add_controller_in_broswer.png)

太棒了！你得到了 5！这正是 `2 + 3 == 5`啊！

等等，`JSON`又是什么？这个网页似乎和之前的 Hello World 有所不同！

是的。但是天色已经晚了，让我们抛开那些东西吧，毕竟，我们可是实现了一个能接收参数的 API 啊！

## 后记

对于那些下载了 Postman 的同学们，请看：

![the_add_API_result_in_postman](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/the_add_API_result_in_postman.png)

Postman 可以让你从浏览器那解放出来，专注于发送请求，以及查看请求的结果。非常的方便。如果你下载了的话，请坚持使用吧！

------

我们已经完成了上面这个加法的 REST API。

想一想：

-   它有没有什么缺陷呢？
-   能依样画葫芦做出四则运算的 API 吗？
-   ……

有太多问题需要考虑了，但是让我们抛开那些东西吧，毕竟，我们可是实现 Java 网络编程中的 REST API 啊！

------

我知道这并不是简单的内容，有太多可以出错的地方了，因此我非常详细地写这篇 tutorial，希望大家能多多上手，勇敢尝试。谢谢大家！