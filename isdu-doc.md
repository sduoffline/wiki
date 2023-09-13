---
title: iSDU-Flutter开发文档
description: 
published: true
date: 2023-01-14T05:18:45.974Z
tags: isdu
editor: markdown
dateCreated: 2022-12-06T08:51:02.495Z
---

# iSDU-Flutter

[![996.icu](https://image-host.towncoder.cn/uPic/link-996.icu-red-20221120134644169.svg)](https://996.icu)
[![LICENSE](https://image-host.towncoder.cn/uPic/license-Anti%20996-blue-20221120134644414.svg)](https://github.com/996icu/996.ICU/blob/master/LICENSE)

### 介绍

iSDU app implemented by Flutter

### 某些开发者特权(main.dart/isInDebugMode)

成绩查询不需要教学评估(grade.dart)
教学评估可以一键完成(evaluate.dart)
打包命令在./package.cmd内


## 开发规范

> 所有违反规范的提交不会被通过

### 命名规范

1. dart文件以**UnderScoreCase(下划线命名)**进行命名,例如`user_info_page.dart`
2. dart文件中类名遵循**大写驼峰变量命名**,例如`UserInfoPage`
3. dart文件中方法以及变量命名遵循**小写驼峰命名**,例如`getUserInfo()` `UserInfo info`
4. **禁止**类变量以无意义的形式命名,例如 `int a`应使用实际作用名称例如`int resultCode`
5. 尽可能使用私有类型,以`_`开头

### 变量规范

1. **减少**自动类型推断变量出现在类变量中，**var**至高只允许出现在函数作用域内

2. **禁止**使用任何类型的全局变量，如有需要用实体类进行封装

3. 使用任何类型的**静态非final/const的public变量**必须写清使用静态变量的原因,参考以下注释，如可以使用**静态private变量**
   (单例模式可以除外)
   ```
   /// 静态变量作用
   /// 为什么必须使用静态变量
   /// 静态变量的可修改范围,[修改者]
   /// 创建静态变量的日期 & 作者
   ```

4. **禁止**使用 **dynamic类型**的参数作为函数的参数/返回值,例如

   ```dart
   /// 以下写法禁止出现
   Future add(a, b) async {
       //do something...
       return a + b;
   }
   ///应使用以下写法,当然使用泛型,或者细化num为int等也可以,返回值可以为void或者Future<void>
   Future<num> add(num a, num b) async {
        //do something...
       return a + b;
   }
   ```

5. 无需修改且能初始确定的变量设置为final类型

6. 对于两个具有一一映射关系的变量,考虑以下方式进行替代

    1. 将一一映射关系的变量封装成一个实体类,并用一个List替代之前的两个List
    2. 如果需要键值查询,可以考虑简单的通过map存储,但必须添加注释,标明键值的作用

7. 所有的泛型变量的声明必须严格声明出**泛型类型**,**禁止**出现类似 `List nums`的声明,应用`List<int> nums`的类似形式替代

### 代码规范

1. **抵制**commit的代码中出现的warning

2. **严禁任何形式的类之间Map类型传参**(fromJson,toJson除外),如需要封装实体类

3. 注释规范参考如下

   ```dart
   /// 类的注释使用三条杠
   class Info {
       /// 类变量使用三条杠
       String name;
       /// 类方法使用三条杠
       String getName() {
           //方法内变量使用两条杠
           String ignore;
           //方法内部使用两条杠
           return 'name:$name';
       }
   }
   ```

4. **import**的时候不要使用**show,hide**关键字,使用**as**替代

5. **慎用lambda表达式**,例如在forEach内部如果是异步函数需要同步,要使用Future.wait(),或者使用await for替换

6. **禁止**Future.then体系的嵌套

   ```dart
   /// 禁止以下形式的代码出现
   void fun1() {
       f1.then((v1) {
           f2.then((v2) {
               //do something
           });
       });
   }
   /// 使用以下形式的等价代码替代
   void fun2() {
       fun3();
   }
   Future<void> fun3() async {
       var v1 = await f1();
       var v2 = await f2();
       //do something
   }
   ```

7. 使用setState前请**慎重**,避免出现已经被卸载的界面仍出现setState操作,setState请尽可能作用于最小组件
    如使用StatefulBuilder需要在异步过程结束后setState,可以使用CustomStatefulBuilder


   ```dart
   void f1() async {
       ///await的过程中有可能触发dispose()的情况
       await someFunction();
       ///不应使用此方式
       setState((){});
       ///应使用如下方式
       if (mounted) {
           setState((){});
       }
   }
   ```

8. 提交中**禁止**出现print()函数,使用 **debugPrint()** 或 **LogUtil.log()** 替代

9. 需要字符串拼接时使用`$`关键字,例如 `'姓名:$name'`

10. 所有的方法调用如有泛型参与必须严格声明出**泛型类型**(即使为dynamic)

    **禁止**出现类似 `static Future<List> _hitCourseCache() async {}`的声明
    使用`static Future<List<Course>> _hitCourseCache() async {}`的形式替代

### 统一使用API介绍

| API描述        | 调用                         | 未登录返回值 |
|--------------|----------------------------|--------|
| 获取用户Token    | UserAPI.token              | ''     |
| 获取用户身份(同步)   | UserAPI.curUser            | null   |
| 获取用户身份(异步)   | await getUserInformation() | null   |
| 判断用户是否是管理员   | UserAPI.isAdmin            | false  |
| 判断用户是否辅修账户   | UserAPI.isMinor            | false  |
| 判断用户是否绑定辅修账户 | UserAPI.isBindMinor        | false  |

### 工具类介绍

1. CacheUtil

   > 负责i山大app的缓存维护功能(主要是清空缓存)

2. ClipBoardUtil

   > 负责i山大与剪切板的交互

3. TimeConvert

   > 负责不同类型和格式的时间的转换,以及DateTime和String的互相转换

4. DatabaseUtil

   > 对Sqlite的封装,轻易不需要改动,只需要在数据库结构变化时更新`_mgrDbVersion`

5. IconUtil

   > 负责映射name与对应的Icon

6. ImageUtil

   > 负责处理图片的封装和压缩

7. LogUtil

   > 负责产品日志的记录,release中的日志由此类收集,换言之除了debugPrint使用LogUtil记录日志

8. NotificationBarUtil

   > 负责与状态栏通知的交互操作

9. PackageInfoUtil

   > 负责i山大app包信息的处理和缓存

10. PermissionUtil

    > 负责i山大app的原生权限申请

11. SecurityUtil

    > 负责i山大app加密部分的运行(考虑到反编译的安全性,拆开到各处似乎更好,不过目前没有这种必要)

12. SharedPreferenceUtil

    > 负责SharedPreference的键定义,以及同步存取和单例模式

13. ShortCutsUtil

    > 负责Android原生桌面快捷键的交互

14. Store

    > 负责将数据库语句封装为NoSql的形式展现

15. StringUtil

    > 负责富文本的预处理等工作

16. PlatformUtil

    > 负责判断平台特性

17. PathUtil

    > 获取储存路径

### 主题色介绍

1. 使用**Get.theme**或**Theme.of(context)**获取Theme,**禁止**自己封装获取Theme的方法(除非你能说服当前的项目组长)
2. 参照**bean/themes.dart**的注释

## 仓库规范（建议）

### 版本管理规范

项目代码release包括三类：

- 大版本(x.0.0)
- 小版本(x.x.0)
- 补丁(x.x.x)

### Git Commit代码提交规范

#### commit message格式

git commit 提交样式规范：

> :
>
> 注意：冒号后面又空格

```
<类型>: <标题>
<空一行>
<内容>
```

#### 1.3.2 格式说明

##### <类型>

用于说明 commit 的类别(type)，只允许使用下面7个标识。

```
# 主要type
feat:     增加新功能
fix:      修复bug

# 特殊type
docs:     只改动了文档相关的内容
style:    不影响代码含义的改动，例如去掉空格、改变缩进、增删分号
build:    构造工具的或者外部依赖的改动，例如webpack，npm
refactor: 代码重构时使用,重构（即不是新增功能，也不是修改bug的代码变动）
revert:   执行git revert打印的message
version: 

# 暂不使用type
test:     添加测试或者修改现有测试
perf:     提高性能的改动
ci:       与CI（持续集成服务）有关的改动
chore:    不修改src或者test的其余修改，例如构建过程或辅助工具的变动
```

当一次改动包括`主要type`与`特殊type`时，统一采用`主要type`。

##### <标题>

commit 目的的简短描述，不超过50个字符

##### <内容>

对本次 commit 的详细描述，可以分成多行，可详细说明代码变动的动机。

#### 示例

```
feat: 添加了xxx功能

对本次 commit 的详细描述，可以分成多行，可详细说明代码变动的动机
```

### 分支管理规范（新增PR规范）

#### 远程仓库

##### 分支约束

远程仓库只允许出现五种类型的分支：

- **主分支**：项目的主要分支也就是master分支。用于正式发布，该分支禁止任何人直接提交，提交合并请求由对应项目主管人员确认合并。
- **开发分支**：项目的开发迭代分支，用于开发发布，常规开发任务的代码由个人开发分支或热修复分支合并至该分支，该分支禁止任何人直接提交，提交合并请求由对应项目主管人员确认合并。
- **个人开发分支**：项目的个人开发迭代分支，用于个人开发，常规开发任务的代码可直接push
- **热修复分支**：项目的问题修复分支，用于修复已知bug，常规任务的代码可直接push
- **归档分支**：项目的里程碑版本保留分支，由测试负责人、项目负责人、产品负责人直接定义版本，从主分支分支归档出新分支。

##### 主分支

主分支全局仓库唯一，分支名称固定为**stable**
，任何对主分支的直接提交定性为严重违规行为，需要尝试对主分支进行合并应当由对应仓库的开发负责人提交合并请求并由上级负责人通过合并实现对主分支的变更。

##### 开发分支

开发分支全局仓库唯一，分支名称固定为**develop**，开发人员不允许对该分支直接提交，需要通过提交Merge
Request进行合并，任何向开发分支提交的代码在提交之前应当保证能够正常通过编译、部署并运行，无法部署的提交应当立刻修复并重新提交。开发分支部署的代码应当由开发人员完成所有的单元测试，全部通过之后再向测试分支提交。

##### 个人开发分支

个人开发分支仓库可以有多个，分支名称固定为**dev/xxx**，开发人员各自创建开发分支，通过提交
Merge Request向开发分支合并代码，个人开发分支部署的代码应当由开发人员完成所有的单元测试，全部通过之后再向开发分支提交。

##### 热修复分支

热修复分支全仓库可以有多个，分支名称固定为**hotfix/xxx**，在需要修复某些bug时由开发人员创建，通过提交Merge
Request向开发分支合并代码，热修复分支部署的代码应当由开发人员完成所有的单元测试，全部通过之后再向开发分支提交。

##### 归档分支

归档分支全局仓管可以具备多个，直接由主分支派生，除特殊情况下禁止任何提交行为，
归档分支名称格式为**release/版本号**（如：`release/1.2.0`），
分支由对应项目的开发主要负责人再与测试负责人沟通之后创建，已经后续无论是BUG、还是功能性问题都不应当项归档分支提交。

#### 提交描述

所有代码的提交、合并都应该通过文字目前描述出改此提交、合并的目的如下面几种：

- `修复问题：#xxxx`
- `修复问题：xxxx`
- `完成功能：#xxxx`
- `完成功能：xxxxx`

任何无意义或者具体目的的提交禁止同步至远程仓库，包括远程临时分支（禁止commit）

![img](https://image-host.towncoder.cn/uPic/gitdoc.jpg)