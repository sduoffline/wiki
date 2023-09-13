---
title: Git Workflow 规范
description: 
published: true
date: 2023-07-26T20:16:43.410Z
tags: 
editor: markdown
dateCreated: 2023-07-26T19:55:34.150Z
---

## Simplified GitFlow

> 这种工作流程是我总结的，主要吸收的是 GitFlow 和 GithubFlow 的优点，兼顾简易性和严谨性，适用于规模不大的团队。大团队遵循团队原有的工作流程即可，或遵循下文中 3 种标准的工作流。

项目开发初期，只在 main 分支上进行。这一阶段的主要工作是搭建项目框架、编写基础代码。力求快速完成，基本只解决依赖管理、编译构建等问题，不涉及业务逻辑。

之后按照以下工作流程进行。

------

- 长期存在的分支
  - main 分支：对外发布的稳定版本。通常情况下只允许其他分支将代码合入，不允许向 main 分支直接提交代码（对应生产环境）
  - develop 分支：日常开发中最新的开发版。用来集成测试最新合入的开发成果，包含要发布到下一个 Release 的代码（对应开发环境）
- 短期分支
  - 根据需求从 main 拉起新分支，不特地区分

当积累适当多的 feature 后，对当前 main 分支进行打包，定版本后发布。可以将 develop 分支作为测试通道发布。不同的通道（channel）使用不同的发布频率，根据 feature 数区分。

进行 feature 开发时，根据设想的功能，从 develop 分支上拉取新分支。开发完成后，向 develop 分支发起 pull request。

从 feature 分支合并向 develop 分支时，使用 `--no-ff` 参数。请注意：`sqush merge`和`rebase`只允许在这个过程中使用。

当 develop 分支上的 feature 达到一定数量并通过测试后，可以将 develop 分支合并到 main 分支。

## GitFlow

- 长期存在的分支
  - master 分支：对外发布的稳定版本。通常情况下只允许其他分支将代码合入，不允许向 Master 分支直接提交代码（对应生产环境）
  - develop 分支：日常开发中最新的开发版。用来集成测试最新合入的开发成果，包含要发布到下一个 Release 的代码（对应开发环境）
- 短期分支
  - feature 分支：特性分支，为了开发某种特定功能，从 Develop 分支上面分出来的。开发完成后，要再并入 Develop。
  - hotfix 分支：热修复分支，生产环境发现新 Bug 时创建的临时分支，问题验证通过后，合并到 Master 和 Develop 分支。
  - release 分支：预发布分支，发布正式版本之前（即合并到 Master 分支之前），基于 Develop 分支创建。发布完成后，合并到 Master 和 Develop 分支（对应集成测试环境）。

------

将 develop 分支合并到 master 分支时，使用：

```shell
git checkout master
git merge --no-ff develop
```

> 默认情况下，Git 执行 "快进式合并"（fast-farward merge），会直接将 Master 分支指向 Develop 分支。使用 --no-ff 参数后，会执行正常合并，在 Master 分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法。关于合并的更多解释，请参考 Benjamin Sandofsky 的[《Understanding the Git Workflow》](http://sandofsky.com/blog/git-workflow.html)。

------

命名规范：
- feature 分支：形如 `feature-*` 的格式。
- hotfix 分支：形如 `hotfix-*` 或 `fixbug-*` 的格式。
- release 分支：形如 `release-*` 的格式。

------

### 评价

优点：清晰可控。

缺点：
- 如果特性分支过多，很容易造成代码冲突，从而提高了 merge 的成本
- 每次提交都涉及多个分支，所以太不适合提交频率较高的项目
- 需要同时维护两个长期分支，大多数工具都将 master 当作默认分支，可是开发是在 develop 分支进行的，导致频繁切换分支
- 适用于基于“版本发布”的项目，而非“持续发布”的项目

## GithubFlow

- 长期存在的分支
  - main 分支：对外发布的稳定版本。通常情况下只允许其他分支将代码合入，不允许向 main 分支直接提交代码（对应生产环境）
- 短期分支
  - 根据需求从 main 拉起新分支，不特地区分

------

新功能开发完成后，向 main 分支发起 pull request。

如果新功能需要讨论，也可以发起 PR，同贡献者讨论、review代码，在过程中也可以继续提交代码。

PR 被接受后被合并入 main 分支，部署后原分支被删除。

### 评价

优点：
- 简单
- 适用于“持续发布”的项目

缺点：
- main 分支的代码不一定就是发布代码，有时不得不另起 production 分支

## GitlabFlow

遵循“上游优先”（upstream first）的原则，即：**所有的修改都必须先合并到上游分支（主分支 main），然后再合并到下游**。

### 持续发布

- 长期存在的分支
  - main 分支：是所有分支的上游分支，所有的修改都必须先合并到 main 分支，然后再合并到下游分支
  - production 分支：对应生产环境
  - pre-production 分支：对应预发布环境
  - ...（根据需要创建分支，对应不同环境）
- 短期分支
  - 根据需求从 main 拉起新分支，不特地区分

遵循上下游排序原则，代码的变化必须从相对上游合并至下游。只有紧急情况，才允许跳过上游。

从上游到下游的变化合并使用`cherry-pick`，只有在需要全部接纳的情况下才使用`merge`。

### 版本发布

- 长期存在的分支
  - main 分支：对外发布的稳定版本。通常情况下只允许其他分支将代码合入，不允许向 main 分支直接提交代码（对应生产环境）
- 短期分支
  - 根据需求从 main 拉起新分支，不特地区分

### 评价

优点：
- 确保了代码的稳定性和一致性
- 通过长期存在的分支对应不同环境，可以实现持续交付和部署

缺点：
- 流程相对固定，可能无法满足某些特定项目或团队的灵活需求。对于一些复杂的开发流程，可能需要使用其他工作流程模型
- `cherry-pick`提交可能会导致一些意想不到的问题

## Credit

参考文档：
- Git 工作流程 - 阮一峰的网络日志 https://www.ruanyifeng.com/blog/2015/12/git-workflow.html
- Git 工作流中常见的三种分支策略：GitFlow、GitHubFlow 以及 GitLabFlow - 云社区 - 华为云 https://bbs.huaweicloud.com/blogs/281789