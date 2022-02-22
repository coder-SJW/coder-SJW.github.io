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

​		每个新功能都应该驻留在自己的分支中，可以将其推送到中央存储库以进行备份/协作。但是，功能分支不是从主分支分支，而是使用开发作为其父分支。当一个功能完成后，它会被合并回develop。功能不应该直接与 main 交互。（如下图）

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

​			一旦 develop 获得了足够的发布功能（或预定的发布日期临近），您就可以从 develop  分支分支发布。创建此分支会启动下一个发布周期，因此在此之后不能添加新功能——只有错误修复、文档生成和其他面向发布的任务应该在此分支中进行。一旦准备好发布，发布分支就会合并到 main 中并用版本号标记。此外，它应该重新合并到开发中，这可能自发布启动以来已经取得了进展。  使用专门的分支来准备发布，可以让一个团队完善当前版本，而另一个团队继续为下一个版本开发功能。它还创建了明确定义的开发阶段（例如，很容易说“本周我们正在为版本 4.0 做准备”，并在存储库的结构中实际看到它）。  制作发布分支是另一个简单的分支操作。与特性分支一样，发布分支也是基于开发分支的。可以使用以下方法创建新的发布分支。

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

![image-20220222111836746](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222111836746.png)

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



# 三、例子

![image-20220222172122699](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222172122699.png)

下面的例子将演示Gitflow流程如何被用来管理一次产品发布。假设你已经创建好了一个中央仓库。



## 1、创建develop分支

![image-20220222173428594](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222173428594.png)

> 第一步：是给默认的master配备一个develop分支。一种简单的做法是：让一个开发者在本地建立一个空的develop分支，然后把它推送到服务器。

```shell
git branch develop
git push -u origin develop
```

> 第二步：develop分支将包含项目的所有历史，而master会是一个缩减版本。现在，其他开发者应该克隆（clone）中央仓库，并且为develop创建一个追踪分支。

```shell
git clone <url>
git checkout -b develop origin/develop
```



## 2、张三和罗翔开始开发新功能

![image-20220222172607442](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222172607442.png)

> 第一步：他们俩各自建立了自己的分支。**注意**-->他们在创建分支时，父分支不能选择master，而要选择develop。

```shell
git checkout -b some-feature develop
```

>  第二步：他们俩都在自己的功能开发分支上开展工作。

```shell
git status
git add <some-file>
git commit 
```



## 3、张三完成了新功能的开发

![image-20220222173116175](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222173116175.png)

​		在提交过几次代码之后，张三觉得她的功能做完了。如果她所在的团队使用“PR”，此刻她可以提出一个将她所完成的功能合并入develop分支的请求。要不然，她可以自行将她的代码合并入本地的develop分支，然后再推送到中央仓库，像这样：

> 第一条命令确保了本地的develop分支拥有最新的代码——这一步必须在将功能代码合并之前做！**注意**，新开发的功能代码永远不能直接合并入master。必要时，还需要解决在代码合并过程中的冲突。

```shell
git pull origin develop
git checkout develop
git merge some-feature
git push
git branch -d some-feature
```



## 4、张三开始预备一次发布

![image-20220222173714714](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222173714714.png)

​		尽管罗翔还在忙着开发他的功能，小马却可以开始准备这个项目的第一次正式发布了。类似于功能开发，她使用了一个新的分支来做产品发布的准备工作。在这一步，发布的版本号也最初确定下来。

```shell
git checkout -b release-0.1 develop
```

​		**这个分支专门用于发布前的准备**，包括一些清理工作、全面的测试、文档的更新以及任何其他的准备工作。它与用于功能开发的分支相似，不同之处在于它是专为产品发布服务的。

​		一旦张三创建了这个分支并把它推向中央仓库，这次产品发布包含的功能也就固定下来了。任何还处于开发状态的功能只能等待下一个发布周期。



## 5、张三完成了发布

![image-20220222174154538](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222174154538.png)

​		一切准备就绪之后，张三就要把发布分支合并入**master**和**develop**分支，然后**再将发布分支删除**。**注意**，往develop分支的合并是很重要的，因为开发人员可能在发布分支上修复了一些关键的问题，而这些修复对于正在开发中的新功能是有益的。

```shell
git checkout master
git merge release-0.1
git push
git checkout develop
git merge release-0.1
git push
git branch -d release-0.1
```

> 发布分支扮演的角色是功能开发（develop）与官方发布（master）之间的一个**缓冲**。无论什么时候你把一些东西合并入master，你都应该随即打上合适的标签。

```shell
git tag -a 0.1 -m"Initial public release" master
git push --tags
```

​		另外：Git支持钩子（hook）的功能，也就是说，在代码仓库里某些特定的事件发生的时候，可以执行一些预定义的脚本。因此，一种可行的做法是：在服务器端配置一个钩子，当你把master推送到中央仓库或者推送标签时，Git服务器能为产品发布进行一次自动的构建。

## 6、用户发现了一个bug

![image-20220222174717774](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222174717774.png)

​		当一次发布完成之后，张三便回去与罗翔一起开发其他功能了。突然，某个用户提出抱怨说当前发布的产品里有一个bug。为了解决这个问题，张三（或者罗翔）基于master创建了一个用于维护的分支(Hotfix）。她在这个分支上修复了那个bug，然后把改动的代码**直接合并入master**。

```shell
git checkout -b issue-#001 master
\# Fix the bug
git checkout master
git merge issue-#001
git push
```

​		跟用于发布的分支一样，在维护分支上的改动**也需要合并入develop分支，这一点是很重要的！**因此，张三务必不能忘了这一步。随后，她就**可以将维护分支删除。**

```shell
git checkout develop
git merge issue-#001
git push
git branch -d issue-#001
```



# 四、相关文档

[A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/ )

[Gitflow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) 
