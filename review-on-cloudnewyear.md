---
title: 后端开发文档：回顾“云过年”
description: 以“云过年”项目后端为例，展示一个成熟的单体后端项目架构
published: true
date: 2023-03-06T03:05:54.740Z
tags: 
editor: markdown
dateCreated: 2023-03-06T03:01:52.839Z
---

# Review on *Cloud New Year*

- [Review on *Cloud New Year*](#review-on-cloud-new-year)
  - [概览](#概览)
  - [初始化](#初始化)
    - [数据库](#数据库)
    - [配置注入](#配置注入)
    - [其他](#其他)
  - [引入 OpenAPI](#引入-openapi)
    - [指定文档信息](#指定文档信息)
    - [指定文档的路由地址](#指定文档的路由地址)
    - [指定 OpenAPI 扫描的包列表](#指定-openapi-扫描的包列表)
  - [代码编写](#代码编写)
    - [CORS](#cors)
    - [Token校验](#token校验)
    - [自定义Interceptor \& Filter](#自定义interceptor--filter)
    - [自定义注解](#自定义注解)
    - [数据库存取](#数据库存取)
    - [Redis](#redis)
  - [部署图床](#部署图床)
    - [安装NextCloud](#安装nextcloud)
    - [图床部署](#图床部署)
      - [SharingPath 插件方式](#sharingpath-插件方式)
      - [共享图床文件夹方式](#共享图床文件夹方式)
    - [上传图片](#上传图片)
      - [（可选）确定真实文件格式](#可选确定真实文件格式)
      - [unirest方式](#unirest方式)
  - [部署](#部署)
    - [安装 Docker](#安装-docker)
    - [配置 redis](#配置-redis)
      - [拉取镜像](#拉取镜像)
      - [运行容器](#运行容器)
      - [查看 redis 虚拟 IP](#查看-redis-虚拟-ip)
      - [维护时查看 redis 信息](#维护时查看-redis-信息)
    - [配置后端](#配置后端)
      - [准备 jar 文件](#准备-jar-文件)
      - [Dockerfile](#dockerfile)
      - [构建镜像](#构建镜像)
      - [运行容器](#运行容器-1)
      - [更新后端](#更新后端)

>   我在年青时候也曾经做过许多梦，后来大半忘却了，但自己也并不以为可惜。所谓回忆者，虽说可以使人欢欣，有时也不免使人寂寞，使精神的丝缕还牵着己逝的寂寞的时光。——鲁迅

*云过年* 项目是一个完备，且充分开发的后端项目。本篇文档力图追溯这个项目从零开始的每一个值得关注的变化，并且为未来提供模板和启发。

该项目主要由 Fopdoodle 开发，篠原はるな提供支援。

## 概览

（以后写）

## 初始化

一个 Springboot 项目，最初的依赖包括：

-   spring web
-   mysql, lombok, mybatis-plus
-   unirest（一个简化的轻量级 HTTP 客户端库。官方文档：https://kong.github.io/unirest-java/）
-   JWT 相关依赖
-   configuration processor

### 数据库

使用腾讯云的云数据库，配置在`application.yml`中。

主要采用`Mapper`搭配注解方式操作数据库。

### 配置注入

`/bean/ValueConfig.java`：

```java
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConfigurationProperties(prefix = "value")
@Data
public class ValueConfig {
    private String aesKey;
}
```

用于从`application.yml`中读取静态值注入。例如，使用 JWT 时用到的 AES key，即可用这个方法注入代码，而不必在代码中写死。

相应的，`application.yml`中加入：

```yaml
value:
  aesKey: ...
```

填入你希望使用的 AES key，即可通过`ValueConfig`注入。

### 其他

准备了`VO`、`PO`，用统一认证登录实现了用户相关接口，项目处见雏形。

回顾框架的搭建，一大疏忽在于，没有重视`.gitignore`文件，导致项目被`target`文件夹污染。

## 引入 OpenAPI

集成 OpenAPI，主要参考[廖雪峰的教程](https://www.liaoxuefeng.com/wiki/1252599548343744/1283318525984802)。

具体使用，在 https://gitlab.sduonline.cn/snippets/4 上有所介绍，样例代码如下：

`AuthController.java`：

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

一个重要的问题在于，接口调用成功以后的数据都是由`VO`包装的，如何在 OpenAPI 中展现完整的返回数据呢？

本项目采取的方式是用 JSON 字符串直接用作样例。虽然缺失了可读性，但是使得文档的编写更加随心所欲。例如，约定俗成的 token，只需要用`"Some token strings"`代替即可。**注意**，其他自定义的实体类并不像 token 串一样，因此需要好好给出示例。

### 指定文档信息

在`/bean`中做`SwaggerConfiguration`的配置，可以修改文档标题、版本等信息。而这些静态信息可以由`ValueConfig`注入。

使用例 `SwaggerConfiguration.java`：

```java
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SwaggerConfiguration {
    @Bean
    public OpenAPI openAPI(){
        return new OpenAPI().info(new Info().
                title("API 文档").
                description("Cloud New Year Project").
                version("v0.0.1SNAPSHOT"));
    }
}
```

### 指定文档的路由地址

在`application.yml`中填入：

```yaml
springdoc:
  swagger-ui:
    path: /api/docs.html
```

即可指定路由地址。

### 指定 OpenAPI 扫描的包列表

`application.yml`：

```yaml
springdoc:
  swagger-ui:
    path: /api/docs.html
  packages-to-scan: com.example.XXX
```

在`packages-to-scan`中填入即可。

## 代码编写

### CORS

（画饼 ing）

### Token校验

(待补充)

### 自定义Interceptor & Filter

拦截器（Interceptor）同 Filter 过滤器一样，它俩都是面向切面编程——AOP 的具体实现（AOP切面编程只是一种编程思想而已）。你可以使用 Interceptor 来执行某些任务，例如在 Controller 处理请求之前编写日志，添加或更新配置。

在 Spring boot 中，当请求发送到 Controller 时，在被Controller处理之前，它必须经过 Interceptors（0或多个）。

总而言之，拦截器（Interceptor）是在请求传到Controller之前对传入的请求进行处理，可以使其往后传或者直接截止不传 。一般而言，Interceptor的作用是验证Token、Cookie，日志记录，统一处理报错等等。

那么如何自定义一个Interceptor呢？  簡単です！

我们要实现HandlerInterceptor这个接口，首先定义一个接口的实现类 ：

```java
public class RequestInterceptor implements HandlerInterceptor
```
这个接口有三个方法
```java
default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    return true;
}

default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {

}

default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {

}
```
根据名字大致可以得出以下判断：

-   preHandle： 该方法在传递到Controller之前被调用，返回一个boolean值，若为true则向后传递，若为false则拦截不传递（后续的Interceptor也接收不到）
-   postHandle： Controller处理完成之后调用，Controller报错则不执行，且调用的顺序和preHandle相反，类似于 Netty 的 pipeline，最后定义的最先调用（我没咋用过==）
-   afterCompletion： 在请求处理完成之后调用，一般用来清理资源，只要该拦截器的preHandle为true就会被执行，不管Controller是否有报错，执行顺序和postHandle相同（我没咋用过==） 

那么写完Inteceptor之后，运行程序时，这个Inteceptor并没有生效，这是为什么呢？因为还没有注册它，Springboot还不认识它。

Springboot中注册拦截器也非常简单，在WebMvcConfigurer中的addInterceptors方法中添加即可：

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(requestInterceptor).addPathPatterns("/**");
}
```
`requestInterceptor`就是我们创建的拦截器，可以直接 new 一个传入或者用 Spring boot 框架作为 Bean 注入。因为在拦截器里面调用了一些 Bean 所以不能直接实例化，而是得用自动装配的方式添加。在拦截器的类上添加`@Component`注解并在`WebMvcConfigurer`中添加下面代码注入`Inteceptor`：

```java
@Autowired
private RequestInterceptor requestInterceptor;
```
经过了以上操作，我们的`Interceptor`已经成功运作了！おめでとう！

偶滴任务已经完成了！ 

ちょっと待って，那么`Filter`又是什么呢？`Filter`顾名思义过滤器，主要用来过滤请求。`Filter`也是一个接口，经常用的一个方法是`doFilter` ：

```java
@Override
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
    chain.doFilter(request, response);
}
```
当调用接口时，会调用`doFilter`方法，其中chain是用来控制请求的传递的。那么`Filter`和`Interceptor`有什么区别的？
1. `Filter`的调用在`Interceptor`之前  
2. `Interceptor`可以有`pre` `post` `after`三种状态，而`Filter`只会在传入请求的时候调用
3. `Filter`中取决于是否调用`chain.doFilter()`来决定是否向后传递
4. `Filter`中的`chain.doFilter()`是阻塞式的，会等传递完Filter并且Controller处理完成之后才会返回
5. `Filter`可以通过某种方式决定向后传递的参数(传递一个自己包装过的`ServeletRequest`)，`Interceptor`好像不行  
6. 不用在`WebMvcConfigurer`中注册，只要在类上加`@Component`注解纳入框架内即可
7. `Interceptor`通过注册的顺序判断先后执行顺序，Filter通过类上的`@Order(x)`注解判断先后，数字越小越先执行（优先级越高）

### 自定义注解  

通过自定义注解可以方便地配置类、方法、变量等属性,减少程序的耦合性。例如SpringBoot内置`@Autowired`让我们不用考虑怎么传入参数，`@Component`让我们的类自动被实例化且纳入框架当中，再例如lombox的`@Data`、`@Setter`、`@Getter`等等注解，不仅使我们的程序功能更加强大，同时也让开发更加方便。

接下来我们就用自定义的注解来实现调用接口必须登录的功能：自定义一个RequiredLogin注解类。

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RequiredLogin {
    boolean required() default false;
}
```
来看这几个元注解。

`@Target(ElementType[])`：这个指定了能用`@RequiredLogin`注解的对象,`ElementType.METHOD`指只能在方法上加此注解,其他还有`ElementType.Type`等等；

`@Retention(RetentionPolicy)`： 这个指定了该注解的生命周期,`RetentionPolicy.RUNTIME`指此注解将被保留下来,如`@RententionPolicy.Source`则是只存在于编译期间,如lombok的`@Data`编译结束后这个注解就消失了。

注解类似于接口,可以写一些方法名,也可以当做是其属性值（类型只支持8大基本类型，可能因为处理注解的时候很多类还没被编译过）。

这里定义了一个`boolean required()`并设置的初始值为`false`，可以通过`required = true/false`来设置该属性值,也可以通过`required()`获取该属性值，如  

```java
@RequiredLogin(required = true)
```
或
```java
if (method.getAnnotation(RequiredLogin.class).required()) {
    do something...  
}
```
自定义注解之后就可以在不同的地方通过反射来获取注解，如  
```java
Method method = ((HandlerMethod) handler).getMethod();
if (method.isAnnotationPresent(RequiredLogin.class)) {
    if (method.getAnnotation(RequiredLogin.class).required()) {
        do something...
    }
}
```


### 数据库存取

(待补充)

### Redis

（画饼 ing）

## 部署图床

### 安装NextCloud

NextCloud是一款开源网盘系统，可以用它作为图床来存储图片。

可以使用`snap`命令一键安装NextCloud：

```shell
sudo apt update
sudo apt install snapd
sudo snap install nextcloud
```
也可以使用 NextCloud 官方的 [All-in-One 镜像](https://github.com/nextcloud/all-in-one)，使用 Docker 快速部署 NextCloud。

安装成功后，即可进入NextCloud配置界面。

### 图床部署

#### SharingPath 插件方式

接下来开始部署图床：

1. 为 NextCloud 安装 Sharing Path。
2. 开一个专门的图床账户。
3. 新建一个用于存放图片的 folder。
4. 配置 Sharing Path。

Copy prefix 就是 `https://{Your cloud URL}/apps/sharingpath/{Account name}`，Sharing folder 填写准备当图床的文件夹即可。

Basic Auth 可以通过手动查看浏览器中的数据获取，也可以靠 Postman 生成查看。

在 Header 中填入 Basic Auth，接口用法如下：

**PUT** https://{Your cloud URL}/remote.php/dav/files/{Account name}/{Sharing folder}/{pic_filename}。

上传成功。直链就是 `https://{Your cloud url}/apps/sharingpath/{Account name}/{Shared folder}/{pic_filename}`。

#### 共享图床文件夹方式

建立图床文件夹，选择共享。

随意上传一张图片，在 NextCloud 中浏览它。运用`F12`，你将会发现图片的链接，通过一系列探索，你将获得自己图片的直链。

![Main_page_of_a_picture_service_account_in_nextcloud](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Main_page_of_a_picture_service_account_in_nextcloud.png)

点击左下角`File settings`，你将在展开页面中获取 WebDAV 地址。假设这个地址是`addr_url`，那么，通过 **PUT** addr_url/example.jpg，即可将`example.jpg`上传。

### 上传图片

#### （可选）确定真实文件格式

由于上传的文件名是由后端指定的，因此我们需要确定上传的图片的文件名。

第一步就是确定文件格式。尽管无伤大雅，现代的图片浏览方式也都不全按后缀名判断文件格式，我们还是自己检测。

确定文件格式最准确的方式就是通过二进制文件头。代码如下：

```java
public Result formatDetect(byte[] binaryData) {
    // 获取二进制文件格式
    StringBuilder builder = new StringBuilder();
    if (binaryData == null || binaryData.length <= 0) {
        return Result.error(405, "文件错误");
    }
    String hv;
    for (int i = 0; i < 4; i++) {
    // 以十六进制（基数 16）无符号整数形式返回一个整数参数的字符串表示形式，并转换为大写
        hv = Integer.toHexString(binaryData[i] & 0xFF).toUpperCase();
        if (hv.length() < 2) {
            builder.append(0);
        }
        builder.append(hv);
    }
    String header = builder.toString();
    // 确定文件格式
    String HTTP_Content_Type = null;
    if(header.startsWith("FFD8FF"))HTTP_Content_Type = "image/jpeg";
    else if(header.startsWith("89504E47"))HTTP_Content_Type = "image/png";
    else if(header.startsWith("47494638"))HTTP_Content_Type = "image/gif";
    else HTTP_Content_Type = "application/octet-stream";
    return Result.success(HTTP_Content_Type);
}
```

接收`byte[]`形式的二进制文件，它的`HTTP_Content_Type`被 Result 包装了一层，你也可以直接返回 String。

#### unirest方式

```java
HttpResponse<String> response = Unirest.put(url).
                header("Authorization", "Basic XXX").
                // Authorization 中填入 Basic Auth 字符串
                header("Accept", "*/*").
                header("Connection", "keep-alive").
                body(bytes).
                asString();
```

其中 url 是 WebDAV 的地址，是包含完整图片名称的链接；bytes 是 `byte[]` 类型的二进制图片文件。

## 部署

本次项目没有采用`docker compose`的方式，而是选择了用Docker分开部署 redis 和后端项目。

### 安装 Docker

对于国内服务器，使用：

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

对于境外服务器，不需要使用阿里云镜像。

使用`docker version`以查看是否安装成功。

### 配置 redis

#### 拉取镜像

**一定要注意选用官方镜像。**

这次项目遇到的不少 redis 问题，极有可能是因为篠原はるな使用了其他服务魔改过的 redis 镜像。

```shell
docker pull redis
```

即可拉取 redis 的最新镜像。使用`docker images`查看已经有的镜像列表。如无意外，应当在列表中找到 redis。

#### 运行容器

现在运行 redis 实例。由于没有持久化、密码、主从配置等等需求，所以不需要加别的参数运行。想要具体配置可以参考 TrueDei 的[*最详细的 docker 中安装并配置 redis*](https://blog.csdn.net/qq_17623363/article/details/106418353)一文，或者浏览官方文档。

假设希望运行的 redis 容器名为`cny-redis`，则运行：

```shell
docker run -itd --name cny-redis -p 6379:6379 redis
```

其中参数`-itd`分别意味着这个 redis 容器：

- 打开 STDIN，可控制台交互
- 分配 tty 设备，支持终端登录
- 容器可后台运行

参数`--name`指定了容器名；`-p`做端口映射，将容器的 6379 端口映射到宿主机的 6379 端口；最后面的`redis`表示该容器根据`redis`镜像产生。

键入`docker ps`即可查看容器列表。如无意外，应当在列表中找到`cny-redis`。

#### 查看 redis 虚拟 IP

现在查看`cny-redis`的虚拟 IP。因为本次项目是分体部署，后端根据 IP 连接 redis 服务，所以需要找到`cny-redis`容器的虚拟 IP。

通过`docker ps`找到`cny-redis`的`container ID`，假设为`abcd123`。接下来键入：

```shell
docker inspect --format '{{ .NetworkSettings.IPAddress }}' abcd123
```

即可得到 redis 容器的虚拟 IP。在后端项目中连接 redis 的部分填写这个 IP，使得后端项目能在服务器上的 Docker 环境中正确连接 redis。在本地运行时，改为`localhost`。

如果重启了 redis 容器，则需要重新获取一次虚拟 IP。因此，在部署中，redis 只部署、运行一次，之后不再开启、关闭。

#### 维护时查看 redis 信息

通过`docker logs cny-redis`可以查看 redis 的 log 信息。

如果想要进入容器，例如使用`redis-cli`，则键入：

```shell
docker exec -it cny-redis /bin/bash
```

其中参数`-it`分别意味着：

- 保持 STDIN 打开
- 分配伪终端

末尾的`/bin/bash`表示运行 bash。

接下来即可使用`redis-cli`。一般会查看`keys *`，以及`DBSIZE`等信息。

### 配置后端

#### 准备 jar 文件

一般来说 Maven | Lifecycle | Package 即可打包，在输出信息中可以找到路径。一般在`target`目录下。

将 jar 文件复制出来并重命名。例如，在本文中，将其命名为`cny-backend.jar`。

在本地使用`java -jar cny-backend.jar`测试无误，即可将文件上传至服务器。在服务器新建一个名为`cny`文件夹，放下 jar 包即可。

#### Dockerfile

使用`touch Dockerfile`以新建`Dockerfile`文件。

接下来，编辑`Dockerfile`：

```dockerfile
FROM openjdk:11
ADD cny-backend.jar  /app/cny-backend-docker.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/cny-backend-docker.jar"]
```

第一行指定 JDK 版本，按需选择。一般选择 JDK 11 或 17。

第二行将同目录下，刚上传的 jar 包 add 进指定目录并重命名。例如`/app/cny-backend-docker.jar`，说明将`cny-backend.jar`添加到**镜像**的`app`目录下，并重命名为`cny-backend-docker.jar`。可以在容器运行后进入容器，找到该文件夹和文件。

#### 构建镜像

键入`docker build -t cny-backend .`（注意末尾有“.”）以构建镜像。

该指令意为：使用当前目录的 Dockerfile 创建镜像，命名为 cny-backend。

也可以使用`docker build -t cny/cny-backend:latest .`，这意味着该镜像的标签是“latest”。

使用`docker images`查看已经有的镜像列表。如无意外，应当在列表中找到后端项目的镜像。

#### 运行容器

使用`docker run -d -p 8080:8080 --name cny-container cny-backend`来构建实例，运行容器。

参数`-d`表示该容器后台运行；`--name`指定容器名称，例如“cny-container”；末尾的`cny-backend`表示以该镜像构建容器。

参数`-p`做端口映射，如果服务器 8080 端口被占用，那么可以写：

```shell
docker run -d -p 8081:8080 --name cny-container cny-backend
```

这意味着将容器的 8080 端口映射到宿主机的 8081 端口。

使用`docker logs cny-container`，即可看到熟悉的输出信息。

#### 更新后端

首先停用容器：

```shell
docker stop cny-container
```

接着删除旧版容器：

```shell
docker rm cny-container
```

这是因为`docker rm`指令只能删除已经停止的容器，不能删除正在运行的容器。

使用`docker rm -f cny-container`可以强制停止并删除容器。

下一步删除旧版镜像：

```shell
docker rmi cny-backend
```

之后，再根据上文中的部署方法部署后端即可，即完成更新。

该过程也可以写好脚本简化操作：

通过`touch deploy.sh`来新建一个部署脚本，使用`chmod 775 deploy.sh`为它提供运行权限。脚本内容如下：

```shell
#!/bin/bash
docker rm -f cny-container
docker rmi cny-backend
docker build -t cny-backend .
docker run -d -p 8080:8080 --name cny-container cny-backend
```

那么每一次更新，只需要替换掉旧版 jar 包，然后通过`./deploy.sh`即可部署。