---
title: '不要害怕 Rebase'
category: 技术
tags:
  - 译文
  - Git
date: '2018-03-20'
cover: https://hackernoon.com/hn-images/1*09KWDWnv1JDeZ-LEkXpL7g.png
author: 根号三
---

> - 原文地址：[Don’t Fear The Rebase](https://hackernoon.com/dont-fear-the-rebase-bca683888dae)
> - 原文作者：本文已获原作者 [Jared Ready](https://hackernoon.com/@jared.ready) 授权，转载请注明出处。
> - 译者：[根号三](https://github.com/sqrthree)

Git 的 `rebase` 命令是 Git 用户感到害怕和迷惑的一个常见原因，特别是那些来自可能更集中的版本控制系统的用户。这很正常。Rebase 是一个不可思议又充满魔力的怪兽，一上来不管三七二十一就改变历史。

Rebase 有点像指针。它是这样一个令人困惑的结构：每个人都在谈论它，但是你并不清楚为什么会有人使用它，然后突然一切都“啪嗒”一下，整个想法都变得显而易见和难以置信的简单。

在这篇文章中我会迫使你“啪嗒”一下，这样你就可以回到工作中并传播 `git rebase` 的神奇。

### 究竟什么是 Rebase？

> Git Rebase 是一个很简单的工具，用来取出一些在某个地方创建的提交，并假装它们一直是在另一个地方创建的。

**好的，我知道了。可是这意味着什么呢？**

让我们来看一个例子。我们在这个仓库中有两个分支：`master` 和 `feature/foo`。`feature/foo` 是基于 `master` 分离出去的分支，并且在 `feature/foo` 分支上产生了一些提交。`master` 也发生了移动，就像世界不会因为少了你的关注而停滞不前。

![](https://hackernoon.com/hn-images/1*RQdhYt4nNVFKlpw_q_IYow.png)

这是目前的状态

我们想将一些更改从 `master` 整合进 `feature/foo` 中，但是我们不想每次执行这个整合时都处理一次令人讨厌的合并提交。

**Rebase 就是一个让你有能力整合发生在源分支上的更改而不需要执行合并（merge）从而不会产生合并提交的工具。**

![](https://hackernoon.com/hn-images/1*PZLwva5O5UoPxcrV68oYgQ.png)

这是 rebase 之后的情况。（fast-forward 版本）

_D_ 和 _F_ 两个提交已经被**重新放在**了 `master` 的顶部，即当前指向的 _G_ 提交。你可能会注意到这两个提交实际上已经被重命名为了 _D\`_ 和 _F\`_，并且提交的 SHA-1 值也不一样。这是为什么呢？

#### Git 中的提交不可变更

一个提交具有一些与之相关的属性：一个父提交、一个时间戳、提交时仓库的快照（提交不仅仅是变更集）。这些值是 Git 在计算标识一个提交的 SHA-1 时所用到的。

由于提交是不可变的，并且一个 SHA-1 应该唯一标识一个提交，因此 Git 需要创建一个新的提交来包含原始提交中相同的仓库快照，但是每个提交都有一个**不同的父提交和时间戳**。

这导致新的提交看起来与原始提交相同，但是具有不同的 SHA-1。

---

### 找出提交

当我们从 `feature/foo` 分支上运行 `git rebase master` 时，Git 怎么知道哪些提交需要移动呢？

让我们先看看每个分支上的提交的文氏图（Venn diagram）。

![](https://hackernoon.com/hn-images/1*HbxYqw71A8ehVCTGkNOyWw.png)

从上图中我们可以看到每一个分支都有 _A_、_B_ 和 _C_ 这几个提交。`master` 分支还拥有 _E_ 和 _G_ 提交但是 `feature/foo` 分支没有。`feature/foo` 拥有 _F_ 和 _D_ 提交但是 `master` 分支没有。

Git 会做一个减法：`{commits on feature/foo} — {commits on master}`，来找出正确的提交。这个结果就是 _D_ 和 _F_。

![](https://hackernoon.com/hn-images/1*qkRc0FH6CzwSse5CNrscfA.png)

#### 我们能证明这一点吗？

当然，一个简单方式是使用 `git log` 来看我们从这组减法中得到的确切提交。

`git log master..feature/foo` **会** 向我们展示 `bc1f36b` 和 `640e713` 提交。

![](https://hackernoon.com/hn-images/1*g3VrmbNmzlpuOm3Fl9Fe8w.png)

如果你在 .. 后省略了一个分支，那么会默认为是当前分支。

看起来不错。让我们来看看更广泛的视角以确保我不是在糊弄。

![](https://hackernoon.com/hn-images/1*U2qcOyvEF6CiZycntHQ_6g.png)

这些 SHA-1 看起来很熟悉。

![](https://hackernoon.com/hn-images/1*zUQkjOT3zHCNp_6LjilQ4A.png)

这里并没有 76f5fd1 和 22033eb，因为我们是从 master 分支的 7559a0b 提交开始分离的。

---

如果我们现在执行一个 `rebase` 到 `master`，我们会立即看到 `76f5fd1` 和 `22033eb` 出现在我们在 `feature/foo` 分支上创建出的提交的前面。

![](https://hackernoon.com/hn-images/1*VLXh6HY221LdULI_i79RyQ.png)

Git 正在像我们期望中的那样重新应用提交。

![](https://hackernoon.com/hn-images/1*cCRyFq-dsWmZWWQ-8a-RJg.png)

看起来熟悉吗？

![](https://hackernoon.com/hn-images/1*PZLwva5O5UoPxcrV68oYgQ.png)

我们之前见过这个了。

我们现在有一个很好的线性历史。你应该能够想到在此刻 fast-forward 的合并会如何发生。

> rebase 策略还有一个已知的额外好处，就是如果你的 CI 管道（CI pipeline）在功能分支上通过了，那么在合并后的主分支上它也会通过。如果是一个非线性的合并策略，你就不能保证这一点。

---

### 使用强制手段

如果 `feature/foo` 分支已经被推送过（push），并且在 rebase 之后尝试进行另一个推送，Git 会很委婉地拒绝推送。这是为什么呢？

**Git 会尽其所能来防止意外覆盖历史，这是一件好事。**

我们来看一下 Git 所认为的 `feature/foo` 分支在远程仓库中是什么样的？

![](https://hackernoon.com/hn-images/1*6v_6goRTKnPduN6q_x4Vpw.png)

现在我们来看一下我们告诉 Git 要做的事情。

![](https://hackernoon.com/hn-images/1*3zndxVsC81_e7okV0aQbVg.png)

从 Git 的角度来看，提交 _D_ 和 _F_ 即将丢弃。Git 会给你这样一行友好的信息：`Updates were rejected because the tip of your current branch is behind`。

你或许会说，“但是我可以在你这个很棒的图片中清晰地看到，`feature/foo` 分支比之前更进一步了啊。” 这是一个很好的观察结果，但是 Git 只会看到远程仓库中的 `feature/foo` 包含 `bc1f36b` 和 `640e713`，但是你本地的 `feature/foo` 不包含这些提交。因此为了不丢失这些提交，Git 会委婉地拒绝一个正常的 `git push`，并要求你执行 `git push --force`。

---

如果你从这篇文章中带走一件东西，那么请记住，rebase 只是简单的查找出在某个分支上创建的提交，然后使用相同的内容但是新的父提交或基础提交（_base_ commit）来创建新的提交。

---

如果你喜欢我的文章，请为我点赞。

关注 [Hackernoon](https://medium.com/@hackernoon) 和 [Jared Ready](https://medium.com/@jared.ready) 来获取更多高质量的软件工程相关的内容吧。

[![](https://cdn-images-1.medium.com/max/1600/1*PZjwR1Nbluff5IMI6Y1T6g@2x.png)](https://goo.gl/w4Pbea)

---

> 本文根据 [Jared Ready](https://hackernoon.com/@jared.ready) 的《[Don’t Fear The Rebase](https://hackernoon.com/dont-fear-the-rebase-bca683888dae)》所译，整个译文带有自己的理解与思想，如果译得不好或有不对之处还请同行朋友指点。如需转载此译文，需注明英文出处：[https://hackernoon.com/dont-fear-the-rebase-bca683888dae](https://hackernoon.com/dont-fear-the-rebase-bca683888dae)
