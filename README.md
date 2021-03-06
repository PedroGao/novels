# git work flow

## 规则

- 在功能分支中执行开发工作。

  _为什么：_

  > 因为这样，所有的工作都是在专用的分支而不是在主分支上隔离完成的。它允许您提交多个 pull request 而不会导致混乱。您可以持续迭代提交，而不会使得那些很可能还不稳定而且还未完成的代码污染 master 分支。

- 从 `develop` 独立出分支。

  _为什么：_

  > 这样，您可以保持 `master` 分支中的代码稳定性，这样就不会导致构建问题，并且几乎可以直接用于发布（当然，这可能对某些项目来说要求会比较高）。

- 永远也不要将分支（直接）推送到 `develop` 或者 `master` ，请使用合并请求（Pull Request）。

  _为什么：_

  > 通过这种方式，它可以通知整个团队他们已经完成了某个功能的开发。这样开发伙伴就可以更容易对代码进行 code review，同时还可以互相讨论所提交的需求功能。

- 在推送所开发的功能并且发起合并请求前，请更新您本地的`develop`分支并且完成交互式变基操作（interactive rebase）。

  _为什么：_

  > rebase 操作会将（本地开发分支）合并到被请求合并的分支（ `master` 或 `develop` ）中，并将您本地进行的提交应用于所有历史提交的最顶端，而不会去创建额外的合并提交（假设没有冲突的话），从而可以保持一个漂亮而干净的历史提交记录。

- 请确保在变基并发起合并请求之前解决完潜在的冲突。

- 合并分支后删除本地和远程功能分支。

  _为什么：_

  > 如果不删除需求分支，大量僵尸分支的存在会导致分支列表的混乱。而且该操作还能确保有且仅有一次合并到`master` 或  `develop`。只有当这个功能还在开发中时对应的功能分支才存在。

- 在进行合并请求之前，请确保您的功能分支可以成功构建，并已经通过了所有的测试（包括代码规则检查）。

  _为什么：_

  > 因为您即将将代码提交到这个稳定的分支。而如果您的功能分支测试未通过，那您的目标分支的构建有很大的概率也会失败。此外，确保在进行合并请求之前应用代码规则检查。因为它有助于我们代码的可读性，并减少格式化的代码与实际业务代码更改混合在一起导致的混乱问题。

- 一旦确定将发布一个版本（功能上满足，或者时间上满足）则从当前的`develop`分支（注意：已经 merge 当前所有新特性分支）上开一个`release`分支。此时的`release`分支不再接受任何合并，集中测试、bug 修复、功能完善。满足要求后将`release`分支合并到`master`分支上，并在`master`分支上分布对应的版本。而后将`release`分支合并到`develop`，此时即可酌情删除`release`分支。

## 流程

- 初始化：初始化一个 git 项目

```bash
git init
```

通过`git status`查看当前仓库的信息：

```sh
README.md
novels/
standard.md
```

`novels`目录是当前小说仓库的存放目录，通过`ls`查看该目录的内容

```sh
$ ls novels/
cp1.txt
```

`cp1.txt`中的内容为：

```sh
如同千万年天上的宫阙中等待苍天何时寂。
```

此时，整个仓库仅仅只存在`master`一个分支，接下来，我们提交当前所有内容，并添加远程分支，将仓库推到远程分支上。

```
git add .
git commit -m ******
```

**注意，git commit 必须要有良好的规范，请查阅相关的资料以规范的格式提交[查看](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)**

我们可以通过`git log`查看刚才的提交：

```sh
commit c75d280516f32db0a2f2901d0d65156fa4ee8de3 (HEAD -> master)
Author: PedroGao <gaopedro@163.com>
Date:   Sat Dec 29 14:21:42 2018 +0800

    docs(novels): add origin chapter

    start writing novel from first chapter
```

```bash
git remote add origin https://github.com/PedroGao/novels.git
git push origin master
```

- 开发： 进行一个项目的开发（在这里我们以小说的书写为例）

原则上`master`分支上不进行任何开发，所以我们新建`develop`分支。注意，`develop`分支也不是用于开发的，而是用于测试、合并、审核等其他操作的。

因此，我们若要继续写小说，我们需再开一个`feature`分支，当然你也可以以其它方式命名，如此时我们需要写完第一章小说，因此我们开一个`feature-cp1`的分支。

```bash
git checkout -b feature-cp1
```

此时，我们正在`feature-cp1`这个分支上，这也是我们进行开发的分支。所以我们继续写完我们的第一章：

```sh
# cp1.txt
如同千万年天上的宫阙中等待苍天何时寂。
几回恍惚与魂惜，而人间再也没有若只如初见。
```

此时，我们已经写完了第一章，如果是开发，请确保你新完成的`feature`通过了开发测试，并有良好的代码规范。

我们完成了`feature-cp1`分支的开发，那么是直接把该分支合并到`develop`上吗，当然不是，我们**不允许**直接合并到开发分支上，因此当你完成一个分支的开发时，
请提`Pull Request`。

因此，此时将你的`feature-cp1`分支推送至远程`github`（请确保你的最新`develop`分支也在远程仓库中），测试在 github 页面上点击`Compare & Pull Request`。

记住，是给`develop`分支提 Pull Request。

如下图：

![one](./imgs/one.png)

![two](./imgs/two.png)

到此，你已经提交了一个 PR，团队的其它有会收到这个 PR，并进行相关的审核。

![three](./imgs/three.png)

当审核通过后，请在本地拉取远程的`develop`分支，本地的分支会得到相应的更新。

此时，`feature-cp1`分支已经完成了它的任务，你可以选择性的删除这个分支。

- 发布：发布一个版本

`develop`分支上的新功能开发完毕后，我们新建一个`release`分支来进行某个版本的发布。注意，这里另开一个分支是为了不影响`develop`分支上的其它流程。

```bash
git checkout -b release-0.1
```

在新建的`release-0.1`这个分支上，原则上不允许新的功能的添加，应集中精力测试，修复 bug，待所有完毕后将这个 release-0.1 分支合并到`master`和`develop`分支上。

此时，通过`git log`查看提交历史，你会发现 commit 的记录有太多的东西重复甚至错误，因此我们需要使用`git rebase`来合并一些没有必要的 commit message。生成一个干净的 log。详细操作参考[rebase](https://www.jianshu.com/p/4a8f4af4e803)。

并在 master 分支发布一个新版本——release-0.1。

[GitHub](https://github.com/PedroGao/novels)
