---
title: 给小盆友们的 Markdown 指南
description: Markdown光速入门篇，你线用了都说好
published: true
date: 2024-01-14T15:08:08.061Z
tags: 
editor: markdown
dateCreated: 2024-01-14T15:08:08.061Z
---

# 给小盆友们的 Markdown 指南

>   Proudly written in markdown.

~~今天来点大家想看的东西~~

“人不能不会 Markdown，正如西方不能没有耶路撒冷。——篠原はるな”

## 标题

在 Markdown 中，一共有 6 级标题，尺寸不一。本节“标题”即为 2 级标题。

| 标题     | 用法          |
| -------- | ------------- |
| 一级标题 | `# 标题`      |
| 二级标题 | `## 标题`     |
| 三级标题 | `### 标题`    |
| 四级标题 | `#### 标题`   |
| 五级标题 | `##### 标题`  |
| 六级标题 | `###### 标题` |

请注意：

1.   **不要**把标题当成一种加粗手段；
2.   井号 “#” 后必须跟上一个空格。

## 加粗

用**一对** “\*\*” 将你希望加粗的内容包围起来即可。

例如，只需要输入`**test**`，即有**test**。

## 斜体

用**一对** “\*” 将你希望加粗的内容包围起来即可。

例如，只需要输入`*test*`，即有*test*。

## 插入链接

例如，想要插入百度的链接，只需要输入`[百度](https://www.baidu.com/)`，即有如下效果：[百度](https://www.baidu.com/)。

## 插入代码

### 行内代码

用**一对** “\`” 将你希望作为行内代码的内容包围起来即可。

例如，输入\`printf("Hello, world!");\`，即有`printf("Hello, world!");`。

**注意：**该符号**不是**单引号。输入该符号的按键在`Esc`同`Tab`键之间。

![grave_accent](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/grave_accent.png)

### 代码块

例如，当你想要输入 Java 的代码块时，你的输入如下：

![markdown_codeblock_demo](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/markdown_codeblock_demo.png)

你将得到如下效果的代码块。

```java
try {
    document = Jsoup.connect(url)
            .userAgent(UA)
            .get();
} catch (IOException e) {
    e.printStackTrace();
}
```

## 引用

当你输入`> Test`时，你将得到如下效果的引用。

>   Test

## 分割线

当你想为你的 Markdown 文档添加一条分割线时，只需要输入`------`即可。效果如下：

------

## 列表

### 无序列表

用`- `（注意有空格）列出每一项即可。例如，当你键入：

```markdown
- Test1
- Test2
- Test3
```

即有如下效果：

- Test1
- Test2
- Test3

### 有序列表

用形如`1. `（注意有空格）的形式列出每一项即可。例如，当你键入：

```markdown
1. Test1
2. Test2
3. Test3
```

即有如下效果：

1. Test1
2. Test2
3. Test3

## 写在最后

Markdown 当然还有很多拓展用法，例如用 *Mermaid* 画流程图、嵌入 *LaTex* 以展现数学公式等等。但是一定需要掌握那些吗？不见得。从上面的基础用法开始，甚至这些基础用法记不清也没关系，如 Typora、VS Code 等软件能帮助你插入你想插入的内容，多多使用，即可熟练。

为什么要使用 Markdown？我的回答是：

1.   同 *LaTex* 等相比，非常轻盈；
2.   代码友好，正适合计算机方向的大家；
3.   只要有文本编辑器就能写，能跨平台显示，显示效果可以通过 CSS 配置；
4.   是 Github 等主流社区的指定文档编写方式；
5.   用 Typora、VS Code，乃至 Intellij IDEA，甚至是记事本，都能很方便地撰写；
6.   能够导出为 Word、PDF 等格式，可以说 Markdown 可以胜任绝大多数文档编写功能。

换言之，你完全可以把 Markdown 视作增强版的文本。你可以在互联网上找到无数 Markdown 的资料，也可以直接在线编辑 Markdown，时时查看效果。也许你会觉得上手稍微有点复杂，不如 Word 选择、按按钮简单，但是只要稍加练习，我相信你很快就可以掌握基本的 Markdown 用法，也会把使用 Markdown 作为你的日常文档编写方式。

**祝大家学习愉快！**