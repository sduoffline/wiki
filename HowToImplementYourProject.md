---
title: 课程设计/实训项目实现指北——从需求分析到上线运行
description: 与大家分享如何从需求分析、视觉设计开始到编码开发，以及最后服务部署、运行维护来完整实现一整个项目。
published: true
date: 2023-01-10T07:22:59.715Z
tags: 
editor: markdown
dateCreated: 2023-01-09T02:56:34.514Z
---

# 课程设计/实训项目实现指北

## 前言

为什么有这份文档？

相信看到这份文档的读者，或多或少已经/即将经历课程设计的折磨，对于学生在线的小朋友来说可能即将开始寒假/暑假实训，又或者在Bilibil上看到过一些课程设计的开题/验收答辩视频。不知道你是否注意到，绝大多数课程设计这种项目的开发，最终收获的其实是一份“玩具代码”，项目缺少必要的需求分析、代码规范、测试，并不（完全）具有实际投入使用的能力。

从面向作业的角度来讲，这种实现程度无可厚非，但本文的存在就是用来指导读者更加科学规范流程化的方法实现您的项目。

> 本文编写内容以一个Web项目为例进行讨论，事实上，如果你需要使用C++、Java编写一个单独应用，这套规则同样适用，即便你在进行其他小组项目（例如一些选修课程），仍可以参考这份文档。
{.is-info}

如果您发现自己在小组作业中存在沟通的困境，技术上成员之间难以互相理解，应用跑起来之后存在许多漏洞，或者希望将自己编写的应用分享给朋友们，那么这份文档大概会对你有所帮助。

## 组建你的队伍：先生，您也不希望队友中途跑路吧

### 请确保你们有一致的目标

在实现一个项目的过程中，每个人的目标或许会存在差异，如果队友之间存在的差异过大，你们的合作或许会比较折磨。

以课程设计/大作业为例，通常来讲，这个项目终归是要算入绩点的，所以你可以选择借助自己的熟练度，尽快实现需求，这样会有更高的性价比；又或者尝试一些比较新的东西，这样就会有额外的学习成本。在一个多人项目中，保证大家这方面的态度是比较重要的，请选择与你目标一致的队友，避免可能存在的分裂。

### 别让等待成为遗憾

尽管小组作业中的摸鱼现象十分现实和普遍，但我还是建议您尽可能多的和队友进行沟通，特别是在你们并不十分熟悉的情况下。

对于leader，在没有外部领导的情况下，您需要负担起push组员的责任。我建议您不必过于担心与teammate之间的关系——承担相应的工作是每位成员应尽的责任。在push到位的情况下，建议适当记录各自的工作内容和贡献，如果组员达成了共识，贡献过少的队友获得较少的收益也是一件合乎情理的事情。

但是对于其余成员，也需要注意，任何项目的推进都需要每个人共同努力。如果你有了有趣、巧妙的idea请务必及时分享，并且与组员保持良好的沟通状态，杜绝人间蒸发的行为。

> 通常来讲，我们所讨论的这些项目短则耗费一两天，多则一两个月，中间难免有个人原因影响个人工作。在多数情况下，向你的队友们说明原因，大家是可以互相理解的，但请勿拒绝沟通。
{.is-info}

最后，**请不要做DDL战士**。如果你还没有足够的经验，可能会错判某个项目的实现难度。尽早开始需求分析和技术可行性的分析将会很大程度上避免项目烂尾。

## 需求分析：我们究竟需要哪些功能？

## 视觉设计：请不要设计出令人眼前一黑的交互

### Follow一个设计规范

如果您并没有视觉设计相关的基础，那么选择一个您喜欢的设计语言将会是极好的选择，例如Google的Material Design或Apple的Cupetial。

对于前端开发者，您应该知道MUI、Vuetify是Material Design风格的一种实践；Element-UI、Ant Design则是另一种风格的组件库。您应该尝试驾驭组件库，保证您的视觉元素总是附和您的视觉设计，并且具有统一的风格。

### 尊重平台行为差异

如果您需要开发跨平台的应用，那么请保证您的交互设计符合各个平台的习惯。例如，滚动、编辑以及提醒的方式，这些交互元素在Android、iOS、Windows和macOS上或多或少存在一些区别。当您的设计与平台习惯不一致时，用户往往认为这是您的产品出现了错误，并且影响使用体验。

## 实现需求：永远不要孤军奋战

组内分工是绕不开的，不同的组有各自的套路。但是一般来说，一个规范的工作组总是需要合理的配置。

### 团队分工

对于一个三人组，很可能需要一个类似 PM（Project Manager）的角色。

PM 需要做的事情，包括但不限于：

-   和前端对接
-   审阅代码
-   处理 Issue
-   分配任务
-   ……

对于一个三人组，一个可行的方案是：1 位 PM，2 位开发者。

请注意，PM **并不**意味着他不需要写代码。恰恰相反，他可能需要更高的代码素养，才能经营好一个团队。

### Git

譬如说，后端小组 A 使用 Git 进行协同开发，那么首先要决定 Git server。学线 Git 是一个选择，当然如果一整个组都能用魔法上网，也鼓励大家用 Github。

#### 项目权限

很多情况下，尚在团队开发阶段的项目，我们可能并不想公之于众。还记得学线 Git 上的项目吗？大家是在被拉进团队以后，才能看到团队项目的。

所以，在 Git 服务上组成小组，在小组内进行 Git 活动是很好的。团队项目不要放在个人名下。

------

除此以外，项目仓库可以设置可见性。想公开时就选择 public，不想公开就选择 private。在具体实施上，Gitlab、Github 等可能有略微的差异，大家自行摸索。

### 偏向代码的讨论

偏向设计的讨论等，用电子邮件、QQ、微信等方式都可以，不过偏向代码的讨论，比如需要加某个 API，比如哪里出了 bug，这些都放在 Git 项目的 issues 区讨论比较合适。

以 First Git Job 为例：

![Issues_Example_of_SDUOnline_Gitlab](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Issues_Example_of_SDUOnline_Gitlab.png)

在某个 Issue 中，可以看到：

![An_Issue_Example_In_First_Git_Job](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/An_Issue_Example_In_First_Git_Job.png)

Issue 有 Open 和 Closed 两种状态。它可以加上不同的 Labels 用来快速区分。例如，上图中的 Issue 就被加上了“闲聊”的标签。很显然，这是可以自定义的。

提 Issue 使用 Markdown 语法。

至于更多用法，例如模板、引用等，请自行探索。

### 接口文档：到底是前端还是后端给？

> 本条目讨论的问题只存在与前后端分离项目中。但是，您仍然可以从中了解到如何与队友沟通。
{.is-info}

后端的篠原はるな小姐对此表示：接口文档和需求文档是分不开的。首先要求产品、前端、移动等部门讨论出需求文档，根据需求，后端产生接口并提供文档。

一个完整的接口文档规范应当如下图所示：

![OpenAPI_example](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/OpenAPI_example.png)

请注意，需求和接口文档是虽然有先后关系，但是内容上却互相完善，逐渐丰富的。

### 开发规范

遵守一套高质量的开发规范对您自身和整个团队都是非常有利的，开发规范有助于您开发更复杂、庞大的项目以及其他人阅读理解您的代码。

事实上，开发规范并没有相应的规范，对于不同规模、不同业务的团队，开发规范或多或少有一些区别。通常来讲，一门语言/框架下的开发规范可能包含以下内容：

- 日志打印格式的要求。
- 注释的要求。
- 对于支持类型推导的强类型语言，规定变量声明的方式。
- 分包方式。
- 如何对代码进行分层。
- Git仓库的相应规范。
- ...

开发规范的拟定可以交给PM。您可以参考`Effective XXX`来获取一门语言的最佳实践，或者参考阿里巴巴、Google等商业公司的语言手册，但最重要的是结合您的需求，避免被规范限制开发效率。

### 选择合适的技术

对于一个Web项目来讲，你可以选择使用Vue、React或者原生方案编写前端，后端则可以选择Java编写的Spring方案、Go或者Node.js；如果您需要编写一个独立应用/大作业，可以选择使用Java、C++、Go等语言；如果您需要GUI应用，那么可以尝试Flutter、Electron或者原生开发......

您会发现，要实现一个项目时可以选择的技术十分丰富，这就要求您的团队做出选择。这一任务应该交给团队中技术最深厚、了解最广的人来主持（同时也就是PM），您需要根据团队内开发者的技术栈、项目的性能要求、不同方案轮子的丰富程度等方面进行决策。对于Web前端、客户端以及后端三个主要的开发方向，您可以参考下面的讨论。

#### Web前端

> 等待补充
{.is-danger}

#### 客户端

这里的客户端包括Android、iOS移动客户端，以及Windows、Linux和macOS桌面客户端。通常，您有如下选择：

- Flutter。Flutter是Google开源的一个跨平台UI框架，您可以使用它编写跨平台的代码。在学生在线，Flutter是移动开发部使用的主要技术。

> Flutter是一个十分优秀的UI框架，您可以使用它编写整个应用，或者通过FFI进行C/C++/Go语言的原生调用。使用Flutter，您将收获一个更加优雅的UI，碾压你还在使用黑色终端窗口的同学，移动总监如是说。
> {.is-success}

- 平台原生。对于Android开发，您可以使用Java、Kotlin；iOS、macOS下使用Objectiv-C、Swift；Windows和Linux下使用C++。原生方案有理论上最高的性能，代价是较高的开发成本。
- Electron。如果您的项目对性能不敏感，并且您的团队以Web开发者为主，那么使用Electron将会是一个还可以的选择。Electron通过将应用运行在Chromium内核内，使得开发者可以通过编写Web项目的方式开发桌面客户端。
- React-native（RN）。与Electron类似，您可以使用React-Native将Web应用变成移动客户端应用。
- MORE...

#### 后端

### 一些你需要具备的意识

#### 使用一些设计模式

设计模式是一套被反复使用、多数人知晓的、经过分类编目的、代码设计经验的总结。 通常认为总共有23种设计模式，软件学院通常会有专门的课程或在软件工程课程中讲解。

许多朋友认为设计模式过于晦涩难懂，很难理解其使用场景及产生原因。其实，有一部分设计模式是您可以在项目中使用以提高开发效率，又或者被一些框架进行了包装，例如：

- 门面/外观模式。典型例子是`Slf4j`，他使用门面模式对日志系统的具体实现进行了包装，这使得开发者可以在不改动代码的状态下进行日志系统的变更。
- 单例模式。当您需要使用`shared preference`或者进行网络请求时，我们建议使用单例模式对**工具类**进行包装。单例模式使得您不需要反复创建新的对象，而是维护一个全局单例。
- 建造者模式。也就是大家常见的使用链式调用和`builder()`来创建对象的一些方式。当您需要来之同一个类，但是要就有不同结构对象时，就可以通过构造另一个建造者来进行实列化。

#### 科学打印log

不管是开发还是上线后的维护，log是定位问题最有效的工具，没有之一。无论前后端还是移动开发，在日志方面我们有一些共通的原则：

1. 对日志进行分级。
2. 对于可能存在问题的代码，打印出相关的参数。
3. 打印时间戳。这将有助于你确定问题发生的位置，特别是在上线后。
4. 完整输出你的异常内容，并避免使用`e.printStackTrace()`，这会阻塞你的用户请求。
5. 使用门面模式下的日志框架`Slf4j`打印日志。
6. 欢迎补充。

如果你的项目需要上线运行，那么使用`Sentry`这种收集错误的平台也是个不错的选择，`Sentry`
为您提供了更全面的统计错误信息的功能。

#### 使用异步代码编写你的UI

很多时候我们的程序会被“阻塞”，例如进行文件的读写操作、网络请求、大文件的编解码时，由于程序通常都是按照自上而下的顺序执行，面对这种耗费时间长的操作就会停止其他代码的执行，导致我们必须等待这部分耗时代码执行完毕才能继续下面的内容。反映在UI上，这通常意味着界面停止渲染，呈现“卡死”的状态。显然，这是非常致命的。

为了应对这种情况（不局限于UI），主流语言都为我们提供了可选的解决方案，或者说一系列模型，例如：

- 多线程/多进程。例如使用Java的`Thread`。
- 回调函数。
- “来自未来的结果”。例如`JavaScript`中`的Promise`、`Dart`（`Flutter`使用的语言）的`Future`。
- async/await。本质上是上一方法的语法糖，但使得我们以同步的方式书写异步的逻辑。
- 用户态线程。例如`Go`的协程。

具体细节，大家可以参考这篇文章[异步编程的几种方式](https://ericfu.me/several-ways-to-aync/)。

> 如果你的程序要进行“耗时”操作，那么使用异步方法编写总是值得被考虑。但是，您或许需要了解对于计算机来讲，不同的异步方案对**计算密集型任务**和**IO密集型任务**效果是有差异的。
{.is-warning}

简单来讲，当计算机在进行大规模计算时，程序耗时的原因是大量的运算占用了CPU和内存，这种情况可以称为**计算密集型任务**；当计算机进行网络请求、文件的读写，程序耗时的原因是CPU的运算速度和内存、缓存的读写速度远远超过IO设备，导致计算单元“等待”IO设备，这可以称为**IO密集型任务**。

多线程/多进程/协程都可以通过开辟新的线程单独进行计算，这种情况下计算任务可以与UI渲染的任务分开运行在不同线程下，配合异步的回调函数或者 async/await 机制就可以达到您需要的效果；对于IO密集的任务，您其实没有必要开辟新的线程/进程/协程，这是因为操作系统将会异步的执行网络请求、文件读写，因此您只需要异步的执行相关函数，操作系统就可以配合您执行这套异步操作。

## 测试代码：不要做撒手掌柜

### 前后端对接

在测试阶段，一个重要的事情是前后端对接。我们从一个例子开始。假设你写了一个 REST API，如下：

**POST** `www.example.com/course_table`

那么，后端开发者当然对某个 controller 非常清楚；然而对接的前端程序员却不了解 API。他如果只能看到上面那行 API，试问：

-   他怎么知道如何使用该 API？
-   他怎么知道 POST 的信息应该有哪些？
-   他怎么知道如果报错，错误代码都意味着什么？
-   ……

所以，编写 API 文档的活也是在后端开发者身上的。

一种简单的方式是：把所有详细信息用 Markdown / txt 写下来，发给对方。但是，*都什么年代，还在用传统方式发 API 文档？*

**对后端同学：**

现在我们介绍更加新潮、优雅、现代、自动化的方式——集成 OpenAPI。它还有一个为人熟知的名字：Swagger。

它的效果如下图：

如何使用？请参考[廖学峰的文章](https://www.liaoxuefeng.com/wiki/1252599548343744/1283318525984802)，或者[篠原春奈的博客](https://icooper.cc/archives/399)，或者只看本篇文档。（但还是推荐都看）

以 FirstSpringBoot 项目为例，首先在 `pom.xml` 中引入依赖：

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>1.6.9</version>
</dependency>
```

只要在 `artifactId` 中打上“springdoc”，就能靠自动补全快速加入依赖了。

直接运行项目，在浏览器中输入`localhost:8080/swagger-ui.html`，就能看到：

![Result_after_directly_adding_openapi](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Result_after_directly_adding_openapi.png)

~~应该不会有人在改了端口号以后还复制粘贴文中的url吧~~

点开一个加法的 API，我们能看到：

![A_detailed_API_info_in_openAPI](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_detailed_API_info_in_openAPI.png)

仅仅是不加配置的默认状态，就已经勉强能用了。

但前端仍然不知道所谓的参数 `a`、`b` 究竟是什么意思，这些信息需要后端给出。

![Annotion_added_code_to_present_detailed_info_in_openAPI](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Annotion_added_code_to_present_detailed_info_in_openAPI.png)

在代码中加入这些注解即可，对 API 的描述信息用 `@Operation(summary = "XXX")`，对参数的描述信息用 `@Parameter(description = "XXX")`。

![Result_of_a_detailed_api_info_in_openapi](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Result_of_a_detailed_api_info_in_openapi.png)

这样，对接的前端程序员就知道这个 API 该如何使用了。

现在我们提供一些更复杂的实现：

效果如下:

![OpenAPI_example](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/OpenAPI_example.png)

对应的示例代码为：

```java
public class AuthController {

    @Autowired
    AuthService authService;

    @Operation(summary = "登录接口", description = "传入学号密码进行统一认证登录，返回token", responses = {
            @ApiResponse(responseCode = "200", description = "登录成功", content = @Content(
                    mediaType = "application/json",
                    schema = @Schema(type = "object", example = "{\n\t\"code\": 200,\n\t\"data\": \"Some token strings\",\n\t\"msg\": null}")
            ))
    })
    @RequestMapping(value = "/login", method = RequestMethod.POST)
    public Result login(@RequestParam @Parameter(description = "学号") String u,
                        @RequestParam @Parameter(description = "密码") String p) {
        return authService.login(u, p);
    }
}
```

当然，本文档举的例子还是非常简单的，实际开发中大家可能会遇到更加复杂的情况，请自行探索。你也可以尝试使用 javadoc 的方式生成文档，学习成本可能会比 OpenAPI 更高一些，but also OK.

## 部署运行：或许你需要一个公网ip（

### 让后端在云上跑起来（Docker 方式）

>   懒得写了，把[之前写的](https://icooper.cc/archives/393)再搬过来得了……

~~都什么年代，还在用传统方式部署后端。~~

让后端在云上跑起来，会要求一点 linux 知识（因为绝大多数的服务器都跑 linux），这个活可以丢给 PM 做。

#### Docker 简介

大家应该都用过虚拟机。例如，当我想要运行 linux 程序的时候，我可以在 Windows 系统里安装 Linux 的虚拟机，在虚拟机内运行想要运行的程序。

然而，很多时候我们只想运行一个软件（例如我们的后端程序），那么安装一整个完整的系统是非常不划算的事情。

Docker 提供了这样一种方式的虚拟：在 Docker 的环境下，你只使用必要的东西。

比如我们的后端程序，我们只要有 jdk 就够了，那么我们拉取 jdk，放上我们的 jar 文件，一番配置，就成了一个镜像。

拿着这份镜像，我们就可以通过镜像创建一个个容器到处部署。把容器玩坏了？再拿镜像跑个容器就行了。

#### 安装 Docker

根据服务器的系统，选择合适的版本部署。

部署完成后，可以通过 `docker --version` 来查看。

#### Dockerfile

将后端项目打包为 jar 文件，上传到服务器。

在 jar 文件目录，新建 `Dockerfille`。`Dockerfile` 文件内容如下：

```dockerfile
FROM openjdk:11
ADD XXX-backend.jar  /app/XXX-backend-docker.jar
EXPOSE 4567
ENTRYPOINT ["java","-jar","/app/XXX-backend-docker.jar"]
```

第一行拉取 openjdk 11，第二行将上传的 jar 文件添加到将要创建的容器的 /app 目录下，并更名。

第三行指定暴露的端口，和后端项目暴露端口一致即可。

第四行就是 Docker 镜像启动时的执行命令。

#### 生成镜像和容器

##### 创建镜像

在项目目录中执行：`docker build -t XXX-app .`

`XXX-app` 是镜像名称，用`.` 来指定当前目录。

使用 `docker images` 来查看镜像目录。

##### 创建容器

在项目目录执行：`docker run -d -p 4567:4567 --name XXX XXX-app`

`-d` 表示后台运行，`-p` 指定端口映射。第一个 `4567` 指镜像暴露的端口，第二个表示映射到外部的端口。可以保持一致。

`--name` 指定容器名称，在容器名称后写明创建容器的镜像。

### 让后端在云上跑起来（Screen 方式）

> Linux 中的 screen 命令用于多重视窗管理程序。
>
> screen 为多重视窗管理程序。此处所谓的视窗，是指一个全屏幕的文字模式画面。通常只有在使用 telnet 登入主机或是使用老式的终端机时，才有可能用到 screen 程序。

虽然有点不说人话，但是要知道，当 ssh 连接断开以后，这个 session 是会被关闭的。而 Screen 就像是开了另一个屏幕，使后端程序能在后台持续运行。

和上面 Docker 方式运行的本质是一样的，都是`java -jar XXX.jar`命令的执行。重点在于，这条命令要运行在 screen 中。

让我们从一个例子开始：

假使你的项目名为X，那么你可以：

```shell
screen -S X-backend # -S（注意大写）指定 screen 作业的名称
```

这时你就进入了这个 screen，你可以：

```shell
cd X-backend
java -jar X-backend.jar
```

在该 screen 中，你就成功运行起了后端项目。键入`ctrl+A`后键入`D`，即可 detach 离开。

通过`screen -ls`命令，可以查看主机上的 screen 作业列表；`screen -r X-backend`即可回到该 screen。

------

和 Docker 方式相比，screen 方式所需要的配置更为简单，当然相应地部提高了移植难度。对于我们目前的项目而言，倒没有什么影响，可以任意选择。

## 产品验收：让我康康！
