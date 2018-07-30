---
title: Git命令复习笔记
tags: 
	- git 
---


初始化一个Git仓库，使用git init命令。

添加文件到Git仓库，分两步：

第一步，使用命令git add <file>，注意，可反复多次使用，添加多个文件；

第二步，使用命令git commit，完成。

---

要随时掌握工作区的状态，使用git status命令。

如果git status告诉你有文件被修改过，用git diff可以查看修改内容。

---
HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。

穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。

要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

<!-- more -->

---
每次修改，如果不add到暂存区，那就不会加入到commit中。

---
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

---

要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；

关联后，使用命令git push -u origin master第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

---

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

---
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

用git log --graph命令可以看到分支合并图

---
### 分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

---

概览：master合并merge解决好的bug后，不要先把dev解印，先合并master，获取里面的bug方案后，在解印。解印时会有提示冲突，需手动改一次文件。

1：在  dev 下正常开发中，说有1个bug要解决，首先我需要把dev分支封存stash

2：在master下新建一个issue-101分支，解决bug，成功后

3：在master下合并issue-101

4：在 dev  下合并master，  这样才同步了里面的bug解决方案

5：解开dev封印stash pop，系统自动合并 & 提示有冲突，因为封存前dev写了东西，此时去文件里手动改冲突

6：继续开发dev，最后add，commit

7：在master下合并最后完成的dev

代码过程如下：

```
在此插入代码

1： $ git stash

2： $ git checkout master
   $ git checkout -b issue-101
    //去文件里修bug
    $ git add README.md
    $ git commit -m "fix-issue-101"

3： $ git checkout master
   $ git merge --no-ff -m "m-merge-issue-101" issue-101
   $ git branch -d issue-101

4： $ git checkout dev
    $ git merge --no-ff -m "dev-merge-m" master

5： $ git stash pop
            //提示冲突，去文件手动改正
            Auto-merging README.md
            CONFLICT (content): Merge conflict in README.md

6： //继续开发 ... ... ，完成后一并提交
    $ git add README.md
    $ git commit -m "fixconflict & append something"

7： $ git checkout master
    $ git merge --no-ff -m "m-merge-dev" dev
    $ git branch -d dev
```

---

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。  

---

### 多人协作

首先，可以试图用git push origin branch-name推送自己的修改；

如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

如果合并有冲突，则解决冲突，并在本地提交；

没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！

如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。

---
查看远程库信息，使用git remote -v；

本地新建的分支如果不推送到远程，对其他人就是不可见的；

从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；

在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；

建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；

从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。

---
### TAG
命令git tag <name>用于新建一个标签，默认为HEAD，也可以指定一个commit id；

git tag -a <tagname> -m "blablabla..."可以指定标签信息；

git tag -s <tagname> -m "blablabla..."可以用PGP签名标签；

命令git tag可以查看所有标签。

---
（05.11追加）
### 撤销一个公共修改 Undo a “public” change

场景：你刚刚用git push将本地修改推送到了GitHub，这时你意识到在提交中有一个错误。你想撤销这次提交。

使用撤销命令：git revert

发生了什么：git revert将根据给定SHA的相反值，创建一个新的提交。如果旧提交是“matter”，那么新的提交就是“anti-matter”——旧提交中所有已移除的东西将会被添加进到新提交中，旧提交中增加的东西将在新提交中移除。

这是Git最安全、也是最简单的“撤销”场景，因为这样不会修改历史记录——你现在可以git push下刚刚revert之后的提交来纠正错误了。

---

### 撤销本地更改 Undo “local” changes

场景：你正在编辑的文件被保存了但是你的编辑器恰在此时崩溃了。此时你并没有提交过代码。你期望撤销这个文件中的所有修改——将这个文件回退到上次提交的状态。

使用撤销命令：git checkout —

发生了什么：git checkout将工作目录（working directory）里的文件修改成先前Git已知的状态。你可以提供一个期待回退分支的名字或者一个确切的SHA码，Git也会默认检出HEAD——即：当前分支的上一次提交。

注意：用这种方法“撤销”的修改都将真正的消失。它们永远不会被提交。因此Git不能恢复它们。此时，一定要明确自己在做什么！（或许可以用git diff来确定）

---

重置本地修改 Reset “local” changes

场景：你已经在本地做了一些提交（还没push），但所有的东西都糟糕透了，你想撤销最近的三次提交——就像它们从没发生过一样。

使用撤销命令：git reset或git reset –hard

发生了什么：git reset将你的仓库纪录一直回退到指定的最后一个SHA代表的提交，那些提交就像从未发生过一样。默认情况下，git reset会保留工作目录（working directory）。这些提交虽然消失了，但是内容还在磁盘上。这是最安全的做法，但通常情况是：你想使用一个命令来“撤销”所有提交和本地修改——那么请使用–hard参数吧。

---

撤销本地后重做 Redo after undo “local”

场景：你已经提交了一些内容，并使用git reset –hard撤销了这些更改（见上面），突然意识到：你想还原这些修改！

使用撤销命令：git reflog和git reset, 或者git checkout

发生了什么：git reflog是一个用来恢复项目历史记录的好办法。你可以通过git reflog恢复几乎任何已提交的内容。

你或许对git log命令比较熟悉，它能显示提交列表。git reflog与之类似，只不过git reflog显示的是HEAD变更次数的列表。

一些说明：

1. 只有HEAD会改变。当你切换分支时，用git commit提交变更时，或是用git reset撤销提交时，HEAD都会改变。但当你用git checkout –时， HEAD不会发生改变。（就像上文提到的情形，那些更改根本就没有提交，因此reflog就不能帮助我们进行恢复了）

2.  git reflog不会永远存在。Git将会定期清理那些“不可达（unreachable）”的对象。不要期望能够在reflog里找到数月前的提交记录。

3.  reflog只是你个人的。你不能用你的reflog来恢复其他开发者未push的提交。

因此，怎样合理使用reflog来找回之前“未完成”的提交呢？这要看你究竟要做什么：

1. 如果你想恢复项目历史到某次提交，那请使用git reset –hard

2. 如果你想在工作目录（working direcotry）中恢复某次提交中的一个或多个文件，并且不改变提交历史，那请使用git checkout–

3. 如果你想确切的回滚到某次提交，那么请使用git reset

---

### 与分支有关的那些事 Once more, with branching

场景：你提交了一些变更，然后你意识到你正在master分支上，但你期望的是在feature分支上执行这些提交。

使用撤销命令：git branch feature, git reset –hard origin/master, 和 git checkout feature

发生了什么：你可能用的是git checkout -b来建立新的分支，这是创建和检出分支的便捷方法——但实际你并不想立刻切换分支。git branch feature会建立一个叫feature的分支，这个分支指向你最近的提交，但是你还停留在master分支上。

git reset –hard将master回退至origin/master，并忽略所有新提交。别担心，那些提交都还保留在feature上。

最后，git checkout将分支切换到feature，这个分支原封不动的保留了你最近的所有工作。

---

### 事半功倍处理分支 Branch in time saves nine

场景：你基于master新建了一个feature分支，但是master分支远远落后与origin/master。现在master分支与origin/master同步了，你期望此刻能在feature下立刻commit代码，并且不是在远远落后master的情况下。

使用撤销命令：git checkout feature和git rebase master

发生了什么：你也许已经敲了命令：git reset（但是没用–hard,有意在磁盘上保存这些提交内容），然后敲了git checkout -b，之后重新提交更改，但是那样的话，你将失去本地的提交记录。不过，一个更好的方法：

使用git rebase master可以做到一些事情：

1.首先，它定位你当前检出分支和master之间的共同祖先节点（common ancestor）。

2.然后，它将当前检出的分支重置到祖先节点（ancestor），并将后来所有的提交都暂存起来。

3.最后，它将当前检出分支推进至master末尾，同时在master最后一次提交之后，再次提交那些在暂存区的变更。
