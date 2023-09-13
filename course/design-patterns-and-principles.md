---
title: 设计模式与设计原则
description: 
published: true
date: 2023-06-28T23:29:48.643Z
tags: 
editor: markdown
dateCreated: 2023-06-28T08:12:40.139Z
---

# 设计模式与设计原则

## 设计原则

设计原则的目标是：

- 可扩展性：新功能易加⼊系统。
- 灵活性：允许代码修改平稳发⽣，不会涉及很多其他模块。
- 可插入性：容易将⼀个类换为另⼀个具有同样接⼝的类。

共有七种设计原则：

|                        设计原则                         | 设计原则简介                                                 |
| :-----------------------------------------------------: | :----------------------------------------------------------- |
|  单一职责原则（Single Responsibility Principle, SRP）   | 类的职责要单一，不能将太多的职责放在一个类中。               |
|       **开闭原则（Open-Closed Principle, OCP）**        | 软件实体对扩展是开放的，但对修改是关闭的，即在不修改一个软件实体的基础上去扩展其功能。 |
|   里氏代换原则（Liskov Substitution Principle, LSP）    | 在软件系统中，一个可以接受基类对象的地方必然可以接受一个子类对象。 |
| **依赖倒转原则（Dependency Inversion Principle, DIP）** | 要针对抽象层编程，而不要针对具体类编程。                     |
|  接口隔离原则（Interface Segregation Principle, ISP）   | 使用多个专门的接口来取代一个统一的接口。                     |
|    **合成复用原则（Compose Reuse Principle, CRP）**     | 在系统中应该尽量多使用组合和聚合关联关系，尽量少使用甚至不使用继承关系。 |
|            迪米特法则（Law of Demeter, LoD）            | 一个软件实体对其他实体的引用越少越好，或者说如果两个类不必彼此直接通信，那么这两个类就不应当发生直接的相互作用，而是通过引入一个第三者发生间接交互。 |

### SRP

**不要存在多于一个导致类变更的原因**。如果一个类承担的职责过多，就等于把这些职责耦合在一起了。一个职责的变化可能会削弱或者抑制这个类完成其他职责的能力。

核心：**解耦和增强内聚性**。

### OCP

软件组成实体应该是**对扩展开放的，但是对修改是关闭**的。在设计一个软件的时候，应当使这个软件可以在不被修改的前提下扩展：

- 已有模块，尤其是**最重要的抽象层模块不能动**：保证稳定性和延续性。
- 可以扩展新模块：增加新行为，保证灵活性

开-闭法则认为应该**试图去设计出永远也不需要改变的模块，关键在于抽象化**。一个软件系统的所有模块不可能都满足OCP，但应该努力最小化不满足OCP的模块数量

### LSP

使用**指向基类(超类)的引用的函数，必须能够在不知道具体派生类(子类)对象类型的情况下使用**它们。一个软件如果使用的是一个父类的话，如果把该父类换成子类，它不能察觉出父类对象和子类对象的区别，也就是凡是父类适用的地方子类也适用。**继承只有满足里氏代换原则才是合理**的。

清楚地表明了 IS-A 关系全部都是与行为有关的，**一个子类型不得具有比基类型更多的限制**。

> 可能这对于基类型来说是合法的，但是可能会因为违背⼦类型的其中⼀个额外限制，从⽽违背了LSP。

### DIP

**抽象不应当依赖于细节（传统设计），细节应当依赖于抽象**。

要针对接口编程，不要针对实现编程：

- 变量、参数、返回值等应声明为**抽象类**
- **不要继承非抽象类**
- 不要重载**父类的非抽象方法**

可应用于任何存在一个类向另一个类发送消息的地方，以此避免类与类之间的耦合，通过接口来间接使用。

使用**接口的六个优点**：

1. Client不必知道其使⽤对象的**具体所属类**。
2. ⼀个对象可以很容易地被实现了**相同接⼝的)的另⼀个对象所替换**。
3. 对象间的连接不必硬绑定(hardwire)到⼀个具体类的对象上，因此**增加了灵活性**。
4. **松散耦合**(loosens coupling)。
5. 增加了**重⽤**的可能性。
6. **提⾼了(对象)组合的机率**，因为被包含对象可以是任何实现了⼀个指定接⼝的类。 

### ISP

使用**多个专门的接口比使用单一的总接口好**，一个类对另一个类的依赖性应建立在最小的接口上。

### CRP

**优先使用(对象)组合，而非(类)继承。**容器类仅能通过被包含对象的接口来对其进行访问，“黑盒“复用封装性好，实现上的相互依赖性比较小，每一个类只专注于一项任务。

- 优点：黑盒、封装性强、相互依赖程度低
- 缺点：系统中依赖过多，多态情况下需要仔细核对接口定义

与继承相比，这样**更容易进行新的实现，易于修改扩展已有的实现**。

继承的缺点：

- **破坏了封装性**，因为这会将父类的实现细节暴露给子类，“白盒”复用
- 当父类的实现更改时，子类也不得不会随之更改
- 从父类继承来的实现将不能在运行期间进行改变

### LoD

又称最少知识原则，**一个对象应该对其他对象尽可能少的了解**。只和直接朋友通信，不和陌生人说话。如果两个类不必彼此通信，那么这两个类就不应当发⽣直接的相互作⽤。**如果其中的⼀个类需要调⽤另⼀个类的某⼀个⽅法的话，可通过第三者转发这个调⽤**。

狭义上满足这四个条件之一即可：

1. 当前对象本身
2. 以参量形式传入当前对象中的对象
3. 当前对象的实例变量直接引用的对象（删去）
4. 当前对象所创建的对象

可以起到**控制信息过载，提高封装能力**的作用：

1. 创建**弱耦合**类，利于复用。
1. **降低成员访问权限**。
1. 设计**不变类**。

## UML



## 设计模式

### 创建型模式

#### 简单工厂模式（封装可变性）

简单⼯⼚模式是有⼀个⼯⼚类**根据传⼊的参量**决定创建出哪⼀种产品类的实例。模式的核⼼是⼯⼚类。这个类**含有必要的判断逻辑**，可以决定在什么时候创建哪⼀个产品类的实例。

不论某个实例的生产工艺发生什么变化，都与调用工厂方法的人无关；将产品的创建和使用分离。

**对于工厂角色而言，不符合OCP**。

- 优点：工厂类包含必要的判断逻辑以创建产品；客户端免除了创建产品对象的责任，仅负责消费产品；实现了对责任的分割
- 缺点：工厂类中了所有的产品创建逻辑，成为全能类/上帝类；当产品类有不同的接口种类时，工厂类需要判断在什么时候创建某种产品。这种对时机的判断和对哪一种具体产品的判断逻辑混合在一起，使得系统在将来进行功能扩展时较为困难。

![简单工厂](https://s2.loli.net/2023/06/28/3qQHFE8WigIbhKm.png)

包含了以下角色：

- 工厂角色
- 抽象产品角色
- 具象产品角色

示例：

```java
/* IVender.java */
public interface IVender {
  void order();
}

/* VenderA.java and VenderB.java */
public class VenderA implments IVender {
  @override
  public void order() {
    // do something.
  }
}

public class VenderB implments IVender {
  @override
  public void order() {
    // do something.
  }
}

/* VenderFactory.java */
public class VendorFactory {
  public static IVender createVender(String type) {
    switch(type) {
      case "A":
        return new VenderA();
      case "B":
        return new VenderB();
      default:
        throw new RuntimeException("xxx");
    }
  }
}

/* Client.java */
public class Client {
  public static void main(String[] args) {
    String type = "A";
    IVender iVender = VenderFactory.createVenter(type);
    iVender.order();
  }
}

/* BAD: Client2.java */
public class Client2 {
  public static void main(String[] args) {
    private static final String TYPE_A = "A";
    private static final String TYPE_B = "B";
    
    public static void main(String[] args) {
      String type = "A";
      if (Object.equals(TYPE_A, type)) {
        // order A;
      } else if (Object.equals(TYPE_B, type)) {
        // order B;
      } else {
        throw new RuntimeException("xxx");
      }
    }
  }
}
```

#### 工厂方法模式

工厂方法模式是类的创建模式，又叫做虚拟构造子（Virtual Constructor）模式或者多态性工厂（Polymorphic Factory）模式。**多个具体的⼯⼚**，负责**多个不同的产品族**，继承⼀个抽象的⼯⼚。 ⼯⼚⽅法模式的⽤意是**定义⼀个创建产品对象的⼯⼚接⼝，将实际创建⼯作推迟到⼦类中**。

> ⼯⼚⽅法模式可以允许很多具体⼯⼚类从抽象⼯⼚类中将创建⾏为继承下来，从⽽可以成为多个简单⼯⼚模式的综合，进⽽推⼴了简单⼯⼚模式

工厂方法与简单工厂：**⼯⼚⽅法模式退化后可以变得很像简单⼯⼚模式**。设想如果⾮常确定⼀个系统只需要⼀个具体⼯⼚类，那么就不妨把抽象⼯⼚类合并到具体的⼯⼚类中去。由于反正只有⼀个具体⼯⼚类，所以不妨将⼯⼚⽅法改成为静态⽅法，这时候就得到了简单⼯⼚模式。

**工厂方法模式完美符合OCP原则**。

> 随着创建者中的方法越来越法，工厂方法将越来越像抽象工厂。

![工厂方法实现](https://s2.loli.net/2023/06/28/BzMu7oyphxbiefR.png)

![教材给出的UML](https://s2.loli.net/2023/06/28/IGHVkADKp13Po2B.png)

包含以下成员：

1. **抽象产品** （Product） 将会对接口进行声明。 对于所有由创建者及其子类构建的对象， 这些接口都是通用的。

2. **具体产品** （Concrete Products） 是产品接口的不同实现。

3. **抽象工厂** （Creator） 类声明返回产品对象的工厂方法。 该方法的返回对象类型必须与产品接口相匹配。

   你可以**将工厂方法声明为抽象方法**， 强制要求每个子类以不同方式实现该方法。 或者， 你也可以在基础工厂方法中返回默认产品类型。

   注意， 尽管它的名字是创建者， 但它最主要的职责并**不是**创建产品。 一般来说， 创建者类包含一些与产品相关的核心业务逻辑。 工厂方法将这些逻辑处理从具体产品类中分离出来。 打个比方， 大型软件开发公司拥有程序员培训部门。 但是， 这些公司的主要工作还是编写代码， 而非生产程序员。

4. **具体工厂** （Concrete Creators） 将会重写基础工厂方法， 使其返回不同类型的产品。

   注意， 并不一定每次调用工厂方法都会**创建**新的实例。 工厂方法也可以返回缓存、 对象池或其他来源的已有对象。

示例：

![工厂方法示例](https://s2.loli.net/2023/06/28/Ebdq5klwAWUXsaK.png)

```java
// 创建者类声明的工厂方法必须返回一个产品类的对象。创建者的子类通常会提供
// 该方法的实现。
class Dialog is
    // 创建者还可提供一些工厂方法的默认实现。
    abstract method createButton():Button

    // 请注意，创建者的主要职责并非是创建产品。其中通常会包含一些核心业务
    // 逻辑，这些逻辑依赖于由工厂方法返回的产品对象。子类可通过重写工厂方
    // 法并使其返回不同类型的产品来间接修改业务逻辑。
    method render() is
        // 调用工厂方法创建一个产品对象。
        Button okButton = createButton()
        // 现在使用产品。
        okButton.onClick(closeDialog)
        okButton.render()


// 具体创建者将重写工厂方法以改变其所返回的产品类型。
class WindowsDialog extends Dialog is
    method createButton():Button is
        return new WindowsButton()

class WebDialog extends Dialog is
    method createButton():Button is
        return new HTMLButton()


// 产品接口中将声明所有具体产品都必须实现的操作。
interface Button is
    method render()
    method onClick(f)

// 具体产品需提供产品接口的各种实现。
class WindowsButton implements Button is
    method render(a, b) is
        // 根据 Windows 样式渲染按钮。
    method onClick(f) is
        // 绑定本地操作系统点击事件。

class HTMLButton implements Button is
    method render(a, b) is
        // 返回一个按钮的 HTML 表述。
    method onClick(f) is
        // 绑定网络浏览器的点击事件。


class Application is
    field dialog: Dialog

    // 程序根据当前配置或环境设定选择创建者的类型。
    method initialize() is
        config = readApplicationConfigFile()

        if (config.OS == "Windows") then
            dialog = new WindowsDialog()
        else if (config.OS == "Web") then
            dialog = new WebDialog()
        else
            throw new Exception("错误！未知的操作系统。")

    // 当前客户端代码会与具体创建者的实例进行交互，但是必须通过其基本接口
    // 进行。只要客户端通过基本接口与创建者进行交互，你就可将任何创建者子
    // 类传递给客户端。
    method main() is
        this.initialize()
        dialog.render()
```

#### 抽象工厂

抽象工厂模式是所有形态的工厂模式中最为抽象和最具一般性的一种形态。抽象⼯⼚模式可以向客户端提供⼀个接⼝，使得**客户端在不必指定产品的具体类型的情况下，创建多个产品族中的产品对象**。 

抽象⼯⼚模式与⼯⼚⽅法模式的**最⼤区别**就在于，⼯⼚⽅法模式针对的是⼀个产品等级结构；⽽抽象⼯⼚模式则需要⾯对多个产品等级结构（产品族，是指位于不同产品等级结构中，功能相关联的产品组成的家族）。 

> 所有具体的⼯⼚都出⾃⼀个抽象的⼯⼚，⽽⼀个具体的⼯⼚可⽣产多个具体的对象，这些具体的对象可以是实现不同的接⼝（跨接⼝产品⼯⼚） 。
>
> 如果采用工厂方法模式，就势必要**使用多个独立的工厂等级结构来对付多个产品等级结构**。由于一系列产品等级结构的相似性，会导致一系列平行的工厂等级结构。**随着产品等级结构的数目增加，工厂方法模式所给出的工厂等级结构的数目也会随之增加**。

![抽象工厂模式UML](https://s2.loli.net/2023/06/28/eLspPJwYEfvbc4m.png)

包含：

1. **抽象产品** （Abstract Product） 为构成系列产品的一组不同但相关的产品声明接口。

2. **具体产品** （Concrete Product） 是抽象产品的多种不同类型实现。 所有变体 （维多利亚/现代） 都必须实现相应的抽象产品 （椅子/沙发）。

3. **抽象工厂** （Abstract Factory） 接口声明了一组创建各种抽象产品的方法。

4. **具体工厂** （Concrete Factory） 实现抽象工厂的构建方法。 每个具体工厂都对应特定产品变体， 且仅创建此种产品变体。

   尽管具体工厂会对具体产品进行初始化， 其构建方法签名必须返回相应的*抽象*产品。 这样， 使用工厂类的客户端代码就不会与工厂创建的特定产品变体耦合。 **客户端** （Client） 只需通过抽象接口调用工厂和产品对象， 就能与任何具体工厂/产品变体交互。

示例：

![抽象工厂示例](https://s2.loli.net/2023/06/28/14YGWJmPLaXk7i8.png)

```java
// 抽象工厂接口声明了一组能返回不同抽象产品的方法。这些产品属于同一个系列
// 且在高层主题或概念上具有相关性。同系列的产品通常能相互搭配使用。系列产
// 品可有多个变体，但不同变体的产品不能搭配使用。
interface GUIFactory is
    method createButton():Button
    method createCheckbox():Checkbox


// 具体工厂可生成属于同一变体的系列产品。工厂会确保其创建的产品能相互搭配
// 使用。具体工厂方法签名会返回一个抽象产品，但在方法内部则会对具体产品进
// 行实例化。
class WinFactory implements GUIFactory is
    method createButton():Button is
        return new WinButton()
    method createCheckbox():Checkbox is
        return new WinCheckbox()

// 每个具体工厂中都会包含一个相应的产品变体。
class MacFactory implements GUIFactory is
    method createButton():Button is
        return new MacButton()
    method createCheckbox():Checkbox is
        return new MacCheckbox()


// 系列产品中的特定产品必须有一个基础接口。所有产品变体都必须实现这个接口。
interface Button is
    method paint()

// 具体产品由相应的具体工厂创建。
class WinButton implements Button is
    method paint() is
        // 根据 Windows 样式渲染按钮。

class MacButton implements Button is
    method paint() is
        // 根据 macOS 样式渲染按钮

// 这是另一个产品的基础接口。所有产品都可以互动，但是只有相同具体变体的产
// 品之间才能够正确地进行交互。
interface Checkbox is
    method paint()

class WinCheckbox implements Checkbox is
    method paint() is
        // 根据 Windows 样式渲染复选框。

class MacCheckbox implements Checkbox is
    method paint() is
        // 根据 macOS 样式渲染复选框。

// 客户端代码仅通过抽象类型（GUIFactory、Button 和 Checkbox）使用工厂
// 和产品。这让你无需修改任何工厂或产品子类就能将其传递给客户端代码。
class Application is
    private field factory: GUIFactory
    private field button: Button
    constructor Application(factory: GUIFactory) is
        this.factory = factory
    method createUI() is
        this.button = factory.createButton()
    method paint() is
        button.paint()


// 程序会根据当前配置或环境设定选择工厂类型，并在运行时创建工厂（通常在初
// 始化阶段）。
class ApplicationConfigurator is
    method main() is
        config = readApplicationConfigFile()

        if (config.OS == "Windows") then
            factory = new WinFactory()
        else if (config.OS == "Mac") then
            factory = new MacFactory()
        else
            throw new Exception("错误！未知的操作系统。")

        Application app = new Application(factory)
```

#### 单例

单例模式确保某⼀个类只有⼀个实例，⽽且⾃⾏实例化并向整个系统提供这个实例。 

课程上介绍了两种单例模式：

- 饿汉式单例类：定义类成员变量的时候直接`new`出来（该成员变量是`private static`的）

  > Java 语言中单例类的一个最重要的特点是类的构造子是私有的，从而避免外界利用构造子直接创建出任意多的实例。值得指出的是，由于构造子是私有的，因此，此类不能被继承。

- 懒汉式单例类：第⼀次`getInstance()`时初始化。 

> 从资源利用的角度来讲，饿汉是比懒汉差的。

饿汉式：

```java
public class EagerSingleton {
  private static fianl EagerSingleton instance = new EagerSingleton();
  
  private EagerSingleton() {
    // some thing
  }
  
  public static EagerSingleton getInstance() {
    return instance;
  }
}
```

懒汉式加锁：

```java
public static LaztSingleton getInstance() {
  if (instance == null) {
    synchronized(LazySingleton.class) {
      if (instance == null) {
        isntance = new LazySingleton();
      }
    }
    return instance;
  }
}
```

### 设计型/结构型模式

#### 适配器

将⼀个类的接⼝转换成客户希望的另外⼀个接⼝。Adapter模式使得原本由于接⼝不兼容 ⽽不能⼀起的那些类可以⼀起⼯作。根据工作方式可以分为：

- 类适配器（⽤继承的⽅式使⽤被适配对象） 
- 对象适配器（⽤组合的⽅式使⽤被适配对象）（可以通过构造函数传⼊被适配对象）

![类适配器](https://s2.loli.net/2023/06/28/4juILOSovX2TelU.png)

![对象适配器](https://s2.loli.net/2023/06/28/hGmtcDv2JpAET6w.png)

示例：

```java
class Adaptee {
  public void SpecificRequest() {}
  }
 
interface Target {
  public void Request();
}
 
class Adapter implements Target {
  Adaptee adaptee;
  public Adapter(Adaptee ee) {
    adaptee = ee;
  }
  public void Request() {
    // Implement behavior using 
    // methods in Adaptee:
    adaptee.SpecificRequest();
  }
} 
```

#### 代理

为其他对象提供一种代理以控制对这个对象的访问。有四种代理形式：

- **远程代理（Remote proxy）**：隐藏一个对象存在于不同地址空间的事实。这种代理也被称为大使。
- **虚代理（Virtual Proxy）**：可以进行最优化，例如根据要求创建对象。
- **保护代理（Protection Proxies）**：允许在访问一个对象时有一些附加的内容处理。
- **智能指引（Smart Reference）**：同上。

**代理和被代理的对象都实现同一个接口**。

> 为什么要控制对于某个对象的访问呢？ 举个例子：有这样一个消耗大量系统资源的巨型对象， 你只是偶尔需要使用它， 并非总是需要。你可以实现延迟初始化： 在实际有需要时再创建该对象。 对象的所有客户端都要执行延迟初始代码。 不幸的是，这很可能会带来很多重复代码。
>
> 在理想情况下， 我们希望将代码直接放入对象的类中， 但这并非总是能实现： 比如类可能是第三方封闭库的一部分。

![代理模式UML](https://s2.loli.net/2023/06/28/OyPNZgmH1G6pKxc.png)

包含：

1. **服务接口** （Service Interface） 声明了服务接口。 代理必须遵循该接口才能伪装成服务对象。

2. **服务** （Service） 类提供了一些实用的业务逻辑。

3. **代理** （Proxy） 类包含一个指向服务对象的引用成员变量。 代理完成其任务 （例如延迟初始化、 记录日志、 访问控制和缓存等） 后会将请求传递给服务对象。

   通常情况下， 代理会对其服务对象的整个生命周期进行管理。

4. **客户端** （Client） 能通过同一接口与服务或代理进行交互， 所以你可在一切需要服务对象的代码中使用代理。

示例：

```java
interface Subject{
  void f();
  void g();
  void h();
}
 
class Proxy implements Subject {
  private Subject realSubject;
  public Proxy() { 
    realSubject = new RealSubject (); 
  }
  // Pass method calls to the realSubject:
  public void f() { realSubject.f(); }
  public void g() { realSubject.g(); }
  public void h() { realSubject.h(); }
}

```

#### 组合模式

将对象组和成树型结构以表⽰“部分-整体”的层次结构。Composite使得**⽤户对单个对象和组合对象的使⽤具有⼀致性**。 

以下情况适用于组合模式：

- 需要表示对象的部分-整体层次结构
- 希望用户忽略对象与单个对象的不同，用户将统一地使用组合结构中的所有对象。

效果：

- 定义了包含基本对象和组合对象的类层次结构基本对象可以被组合成更复杂的组合对象，而这个组合对象又可以被组合，这样不断的递归下去。客户代码中，**任何用到基本对象的地方都可以使用组合对象**。
- 简化客户代码客户可以**一致地使用组合结构和单个对象**。通常用户不知道(也不关心)处理的是一个叶节点还是一个组合组件。这就简化了客户代码, 因为在定义组合的那些类中不需要写一些充斥着选择语句的函数。
- 使得**更容易增加新类型的组件**。新定义的Composite或Leaf子类自动地与已有的结构和客户代码一起工作，客户程序不需因新的Componeent类而改变。
- 使你的设计变得更加一般化容易增加新组件也会产生一些问题，那就是很难限制组合中的组件。有时你希望一个组合只能有某些特定的组件。使用 Composite 时，你不能依赖类型系统施加这些约束，而必须在运行时刻进行检查。

![组合模式结构](https://s2.loli.net/2023/06/28/Pr7QRFNjSH2lEc6.png)

1. **组件** （Component） 接口描述了树中简单项目和复杂项目所共有的操作。

2. **叶节点** （Leaf） 是树的基本结构， 它不包含子项目。

   一般情况下， 叶节点最终会完成大部分的实际工作， 因为它们无法将工作指派给其他部分。

3. **容器** （Container）——又名 “组合 （Composite）”——是包含叶节点或其他容器等子项目的单位。 容器不知道其子项目所属的具体类， 它只通过通用的组件接口与其子项目交互。

   容器接收到请求后会将工作分配给自己的子项目， 处理中间结果， 然后将最终结果返回给客户端。

4. **客户端** （Client） 通过组件接口与所有项目交互。 因此， 客户端能以相同方式与树状结构中的简单或复杂项目交互。

示例：

![组合模式示例](https://s2.loli.net/2023/06/28/FDGTWigHRQMI2OA.png)

```java
// 组件接口会声明组合中简单和复杂对象的通用操作。
interface Graphic is
    method move(x, y)
    method draw()

// 叶节点类代表组合的终端对象。叶节点对象中不能包含任何子对象。叶节点对象
// 通常会完成实际的工作，组合对象则仅会将工作委派给自己的子部件。
class Dot implements Graphic is
    field x, y

    constructor Dot(x, y) { …… }

    method move(x, y) is
        this.x += x, this.y += y

    method draw() is
        // 在坐标位置(X,Y)处绘制一个点。

// 所有组件类都可以扩展其他组件。
class Circle extends Dot is
    field radius

    constructor Circle(x, y, radius) { …… }

    method draw() is
        // 在坐标位置(X,Y)处绘制一个半径为 R 的圆。

// 组合类表示可能包含子项目的复杂组件。组合对象通常会将实际工作委派给子项
// 目，然后“汇总”结果。
class CompoundGraphic implements Graphic is
    field children: array of Graphic

    // 组合对象可在其项目列表中添加或移除其他组件（简单的或复杂的皆可）。
    method add(child: Graphic) is
        // 在子项目数组中添加一个子项目。

    method remove(child: Graphic) is
        // 从子项目数组中移除一个子项目。

    method move(x, y) is
        foreach (child in children) do
            child.move(x, y)

    // 组合会以特定的方式执行其主要逻辑。它会递归遍历所有子项目，并收集和
    // 汇总其结果。由于组合的子项目也会将调用传递给自己的子项目，以此类推，
    // 最后组合将会完成整个对象树的遍历工作。
    method draw() is
        // 1. 对于每个子部件：
        //     - 绘制该部件。
        //     - 更新边框坐标。
        // 2. 根据边框坐标绘制一个虚线长方形。


// 客户端代码会通过基础接口与所有组件进行交互。这样一来，客户端代码便可同
// 时支持简单叶节点组件和复杂组件。
class ImageEditor is
    field all: CompoundGraphic

    method load() is
        all = new CompoundGraphic()
        all.add(new Dot(1, 2))
        all.add(new Circle(5, 3, 10))
        // ……

    // 将所需组件组合为复杂的组合组件。
    method groupSelected(components: array of Graphic) is
        group = new CompoundGraphic()
        foreach (component in components) do
            group.add(component)
            all.remove(component)
        all.add(group)
        // 所有组件都将被绘制。
        all.draw()
```

#### 桥接模式

把**抽象部分和⾏为部分分离**，使它们都能独⽴变化，将抽象化与实现化进⾏脱藕。（不要使实现直接继承⼀种抽象的抽象类）。 

实质是把两个继承体系的东⻄组合起来。⽅法是在其中⼀个体系的抽象类中以组合的形式装⼊另⼀个体系的抽象类。 

> 装饰模式不管装饰成什么样，最终都实现同⼀个接⼝。桥接模式是把另⼀个体系的东⻄搬进来，另⼀个体系实现⾃⼰的接⼝，可以和本体系的接⼝⽆关。 

![将一个类层次转化为多个相关的类层次](https://s2.loli.net/2023/06/28/f3G7ZxgP82ztJYj.png)

![桥接模式UML](https://s2.loli.net/2023/06/28/xS7JaKW6tAjIBHz.png)

包含：

1. **抽象部分** （Abstraction） 提供高层控制逻辑， 依赖于完成底层实际工作的实现对象。

2. **实现部分** （Implementation） 为所有具体实现声明通用接口。 抽象部分仅能通过在这里声明的方法与实现对象交互。

   抽象部分可以列出和实现部分一样的方法， 但是抽象部分通常声明一些复杂行为， 这些行为依赖于多种由实现部分声明的原语操作。

3. **具体实现** （Concrete Implementations） 中包括特定于平台的代码。

4. **精确抽象** （Refined Abstraction） 提供控制逻辑的变体。 与其父类一样， 它们通过通用实现接口与不同的实现进行交互。

5. 通常情况下， **客户端** （Client） 仅关心如何与抽象部分合作。 但是， 客户端需要将抽象对象与一个实现对象连接起来。

一个示例：

![桥接模式示例](https://s2.loli.net/2023/06/28/gu3ZEvLIhXYVHf7.png)

```java
// “抽象部分”定义了两个类层次结构中“控制”部分的接口。它管理着一个指向“实
// 现部分”层次结构中对象的引用，并会将所有真实工作委派给该对象。
class RemoteControl is
    protected field device: Device
    constructor RemoteControl(device: Device) is
        this.device = device
    method togglePower() is
        if (device.isEnabled()) then
            device.disable()
        else
            device.enable()
    method volumeDown() is
        device.setVolume(device.getVolume() - 10)
    method volumeUp() is
        device.setVolume(device.getVolume() + 10)
    method channelDown() is
        device.setChannel(device.getChannel() - 1)
    method channelUp() is
        device.setChannel(device.getChannel() + 1)


// 你可以独立于设备类的方式从抽象层中扩展类。
class AdvancedRemoteControl extends RemoteControl is
    method mute() is
        device.setVolume(0)


// “实现部分”接口声明了在所有具体实现类中通用的方法。它不需要与抽象接口相
// 匹配。实际上，这两个接口可以完全不一样。通常实现接口只提供原语操作，而
// 抽象接口则会基于这些操作定义较高层次的操作。
interface Device is
    method isEnabled()
    method enable()
    method disable()
    method getVolume()
    method setVolume(percent)
    method getChannel()
    method setChannel(channel)


// 所有设备都遵循相同的接口。
class Tv implements Device is
    // ……

class Radio implements Device is
    // ……


// 客户端代码中的某个位置。
tv = new Tv()
remote = new RemoteControl(tv)
remote.togglePower()

radio = new Radio()
remote = new AdvancedRemoteControl(radio)
```

#### 装饰器模式

**动态地给⼀个对象添加⼀些额外的职责**，别名也叫Wrapper。Decorator必须和要包装的的对象具有相同的接⼝。 有时我们希望给某个对象⽽不是整个类添加⼀些功能。 

> **装饰器和被包装的对象是同⼀层的**，装饰器⾃⼰底下可以派⽣出不同种类。装饰器使⽤组合的⽅式持有⼀个实现了该接⼝的⽰例，来装饰它。被装饰的对象是调⽤者传给装饰器的。（既继承，⼜组合） 装饰器⾃⼰⼀般是⼀个抽象类。 

![装饰器模式示例](https://s2.loli.net/2023/06/28/NMYXaApg4Rqitzv.png)

![装饰模式结构UML](https://s2.loli.net/2023/06/28/SBUC6u2gsjzrxn7.png)

1. **部件** （Component） 声明封装器和被封装对象的公用接口。
2. **具体部件** （Concrete Component） 类是被封装对象所属的类。 它定义了基础行为， 但装饰类可以改变这些行为。
3. **基础装饰** （Base Decorator） 类拥有一个指向被封装对象的引用成员变量。 该变量的类型应当被声明为通用部件接口， 这样它就可以引用具体的部件和装饰。 装饰基类会将所有操作委派给被封装的对象。
4. **具体装饰类** （Concrete Decorators） 定义了可动态添加到部件的额外行为。 具体装饰类会重写装饰基类的方法， 并在调用父类方法之前或之后进行额外的行为。
5. **客户端** （Client） 可以使用多层装饰来封装部件， 只要它能使用通用接口与所有对象互动即可。

### 行为型模式

#### 责任链模式

在责任链模式⾥，**很多的对象由每⼀个对象对其下家的引⽤⽽联接起来形成⼀条链**。请 求在这个链上传递，直到链上的某⼀个对象决定处理此请求。发出这个请求的客户端并不知道链上的哪⼀个对象最终处理这个请求，这使得系统可以在不影响客户端的情况下动态地重新组织链和分配责任。 

即：⼀个对象持有⼀个⾃⼰本⾝的引⽤，叫做`nextXX`。在⾃⼰的某个⽅法被调⽤时，先决定⾃⼰要不要处理这个请求，如果决定⾃⼰不处理，判断⼀下如果`nextXX`不为空，就继续调⽤这个对象的同名⽅法。 

责任链模式**减低了发出命令的对象和处理命令的对象之间的耦合**，它允许多与⼀个的处理者对象根据⾃⼰的逻辑来决定哪⼀个处理者最终处理这个命令。换⾔之，发出命令的对象只是把命令传给链结构的起始者，⽽不需要知道到底是链上的哪⼀个节点处理了这个命令。

![责任链 UML](https://s2.loli.net/2023/06/28/H6KklUMb4gxZvzR.png)

包含：

1. **处理者** （Handler） 声明了所有具体处理者的通用接口。 该接口通常仅包含单个方法用于请求处理， 但有时其还会包含一个设置链上下个处理者的方法。

2. **基础处理者** （Base Handler） 是一个可选的类， 你可以将所有处理者共用的样本代码放置在其中。

   通常情况下， 该类中定义了一个保存对于下个处理者引用的成员变量。 客户端可通过将处理者传递给上个处理者的构造函数或设定方法来创建链。 该类还可以实现默认的处理行为： 确定下个处理者存在后再将请求传递给它。

3. **具体处理者** （Concrete Handlers） 包含处理请求的实际代码。 每个处理者接收到请求后， 都必须决定是否进行处理， 以及是否沿着链传递请求。

   处理者通常是独立且不可变的， 需要通过构造函数一次性地获得所有必要地数据。

4. **客户端** （Client） 可根据程序逻辑一次性或者动态地生成链。 值得注意的是， 请求可发送给链上的任意一个处理者， 而非必须是第一个处理者。

一个示例：

#### 观察者模式

定义对象间的⼀种⼀对多的依赖关系，当⼀个对象的状态发⽣改变时，所有依赖于他的对象都得到通知并被⾃动更新。 

改变⼀个对象需要同时改变其他对象，其他对象之间互相不知道。被改变的对象⾥⽤⼀个`List`存储所有它的观察者（接收器是接⼝），当该对象有属性被改变，调⽤`notifyObservers`⽅法，⽤`for`循环执⾏所有观察者⾥的`update`函数。 

添加观察者到被改变对象的这个操作是由观察者在被创建时，传⼊被改变对象的引⽤，调⽤其attach⽅法把this添加到其中。

![Observer UML](https://s2.loli.net/2023/06/28/KG2VfCEeM1XNQFL.png)

1. **发布者** （Publisher） 会向其他对象发送值得关注的事件。 事件会在发布者自身状态改变或执行特定行为后发生。 发布者中包含一个允许新订阅者加入和当前订阅者离开列表的订阅构架。

   当新事件发生时， 发送者会遍历订阅列表并调用每个订阅者对象的通知方法。 该方法是在订阅者接口中声明的。

2. **订阅者** （Subscriber） 接口声明了通知接口。 在绝大多数情况下， 该接口仅包含一个 `update`更新方法。 该方法可以拥有多个参数， 使发布者能在更新时传递事件的详细信息。

3. **具体订阅者** （Concrete Subscribers） 可以执行一些操作来回应发布者的通知。 所有具体订阅者类都实现了同样的接口， 因此发布者不需要与具体类相耦合。

   订阅者通常需要一些上下文信息来正确地处理更新。 因此， 发布者通常会将一些上下文数据作为通知方法的参数进行传递。 发布者也可将自身作为参数进行传递， 使订阅者直接获取所需的数据。

4. **客户端** （Client） 会分别创建发布者和订阅者对象， 然后为订阅者注册发布者更新。

#### 策略模式

定义⼀系列算法，把他们⼀个个封装起来,并且使他们可以相互替换。使算法可独⽴于使⽤它的客户。



![策略模式UML](https://s2.loli.net/2023/06/28/5SEp17DeicvZmCz.png)

![策略模式 UML](https://s2.loli.net/2023/06/29/JaAC14ziBcMqjrd.png)

适用于：

1. 以不同的格式保存文件；
2. 以不同的算法压缩文件；
3. 以不同的算法截获图象；
4. 以不同的格式输出同样数据的图形，比如曲线或框图bar等

#### 命令模式

在软件系统中，行为请求者与行为实现者通常是一种紧轉合的关系，但某些场合， 比如需要对行为进行记录、撤销或重做、 事务等处理时，这种无法抵御变化的紧糯合的设计就不太合适。

本质：对命令进行封装，将发出命令与执行命令的责任分开。

每一个命令都是一个操作：请求的一方发出请求，要求执行一个操作；接收的一方收到请求，并执行操作。请求方和接收方独立开来，使得请求的一方不必知道接收请求的一方的接口，更不必知道请求是怎么被接收，以及操作是否被执行、 何时被执行，以及是怎么被执行的。使请求本身成为一个对象，这个对象和其它对象一样可以被存储和传递。

![](https://s2.loli.net/2023/06/28/VvQt1FY7bWixrlT.png)

命令模式的关键在于引入了**抽象命令接口**，且发送者针对抽象命令接口编程，只有实现了抽象命令接口的具体命令才能与接收者相关联。

优点：

1. 解除了请求者与实现者之间的糯合，降低了系统的聘合度。
2. 对请求排队或记录请求日志，支持撤销操作。

2. 可以容易地设计一个组合命令 (Macrocommand)。
3. 新命令可以容易地加入到系统中。

缺点：因为针对每一个命令都需要设计一个具体命令类， 使用命令模式可能会导致系统有过多的具体命令类。

适用场景：

1. 当需要对行为进行 “记录、撤销/重做” 等处理时
2. 系统需要将请求者和接收者解赮，使得调用者和接收者不直接交互。
3. 系统需要在不同时间指定请求、请求排队和执行请求。
4. 系统需要将一组操作组合在一起，即支特宏命令。

![UML](https://s2.loli.net/2023/06/28/AKRFflDWUbn7ze8.png)