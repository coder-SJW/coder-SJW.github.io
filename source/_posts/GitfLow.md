---
title: GitfLow
date: 2022-02-16 10:31:01
tags:
  - gitFlow
categories: git
cover: https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222113516599.png
top_img: https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222113516599.png
---

# 一、开始

> Gitflow 实际上只是 Git 工作流程的抽象概念。这意味着它规定了要设置什么样的分支以及如何将它们合并在一起。

>  git流程图：

![image-20220222113516599](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222113516599.png)

我们将在下面讨论分支的目的。 git-flow 工具集是一个具有安装过程的实际命令行工具。 git-flow 的安装过程很简单。 git-flow  的软件包可在多个操作系统上使用。

- 在 OSX 系统上，您可以执行 brew install git-flow。
- 在 Windows  上，您需要下载并安装 git-flow。安装 git-flow 后，您可以通过执行 git flow init 在项目中使用它.Git-flow 是一个围绕 Git 的包装器。 git flow init 命令是默认 git init  命令的扩展，除了为您创建分支外，不会更改存储库中的任何内容 #

# 二、操作

## 1.（Feature branches）功能分支

每个新功能都应该驻留在自己的分支中，可以将其推送到中央存储库以进行备份/协作。但是，功能分支不是从主分支分支，而是使用开发作为其父分支。当一个功能完成后，它会被合并回develop。功能不应该直接与 main 交互。（如下图）

![image-20220216105154362](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220216105154362.png)

### 1.1创建功能分支

> 不使用git-flow进行创建

```shell
git checkout develop #切换到develop分支
git checkout -b feature_branch #在develop分支上创建功能分支并切换
```

> 使用git-flow进行创建

```sh
git flow feature start feature_branch
```

继续你的工作并像往常一样使用 Git。

### 1.2完成功能分支

> 不使用git-flow

```shell
git checkout develop #切换到develop分支
git merge feature_branch #合并功能分支
```

> 使用git-flow

```shell
git flow feature finish feature_branch
```

## 2.（Release branches）发布分支

![image-20220216112954357](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220216112954357.png)

 		一旦 develop 获得了足够的发布功能（或预定的发布日期临近），您就可以从 develop  分支分支发布。创建此分支会启动下一个发布周期，因此在此之后不能添加新功能——只有错误修复、文档生成和其他面向发布的任务应该在此分支中进行。一旦准备好发布，发布分支就会合并到 main 中并用版本号标记。此外，它应该重新合并到开发中，这可能自发布启动以来已经取得了进展。  使用专门的分支来准备发布，可以让一个团队完善当前版本，而另一个团队继续为下一个版本开发功能。它还创建了明确定义的开发阶段（例如，很容易说“本周我们正在为版本 4.0 做准备”，并在存储库的结构中实际看到它）。  制作发布分支是另一个简单的分支操作。与特性分支一样，发布分支也是基于开发分支的。可以使用以下方法创建新的发布分支。

> 不使用git-flow创建发布分支

```shell
git checkout develop #切换到develop分支
git checkout -b release/0.1.0 # 创建发布分支
```

> 使用git-flow创建发布分支

```shell
git flow release start 0.1.0
Switched to a new branch 'release/0.1.0'
```

​		一旦发布分支准备好发布，它将被合并到主分支和开发分支中，**然后发布分支将被删除**。**重新合并到开发中很重要**，因为可能已将关键更新添加到发布分支中，并且需要新功能可以访问它们。

> 不使用git-flow合并发布分支到主分支

```shell
git checkout main #切换到主分支
git merge release/0.1.0 #合并发布分支
```

> 使用git-flow合并发布分支到主分支

```shell
git flow release finish '0.1.0'
```

## 3.（Hotfix branches）修复分支

![image-20220222111836746](C:\Users\28552\AppData\Roaming\Typora\typora-user-images\image-20220222111836746.png)

​		这个分支用来修复主线master的BUG。修复分支很像发布分支和功能分支，只是它们基于main分支而不是develop分支。这是唯一应该直接从 main  分叉的分支。修复完成后，应将其合并到 main分支 和 develop分支（或当前发布分支）中，并且 main 分支应使用更新的版本号进行标记。拥有专门的错误修复开发线可以让您的团队解决问题，而无需中断工作流程的其余部分或等待下一个发布周期。您可以将修复分支视为直接与 main分支  一起使用的临时发布分支。

可以使用以下方法创建修复分支：

> 不使用git-flow创建

```shell
git checkout main
git checkout -b hotfix_branch
```

> 使用git-flow创建

```shell
git flow hotfix start hotfix_branch
```

一个修复分支合并到主分支和开发分支中，类似于完成一个发布分支，如下。

```shell
git checkout main
git merge hotfix_branch
git checkout develop
git merge hotfix_branch
git branch -D hotfix_branch
```

```shell
git flow hotfix finish hotfix_branch
```
