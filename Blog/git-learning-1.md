---
title: 《Git三剑客》学习笔记：个人使用场景
mathjax: false
copyright: true
comment: true
date: 2020-02-26 14:55:17
tags:
- Git
categories:
- Tools
- Git

---

{% note primary %}
Git个人使用的场景，需要的命令和知识点。
{% endnote %}

<!-- more -->

---

## 前言

本篇主要总结一下一些个人使用Git时遇到的常见场景问题及需要的常见命令。


## 场景分析

### 0x01 删除不必要的分支

```bash
git branch -d branch-name #只能删除完全merge的分支
git branch -D branch-name #强制删除分支
```

### 0x02 修改最新commit的message

```bash
git commit --amend
```

### 0x03 修改老旧commit的message

利用交互式的`rebase`命令，其中rebase的对象是目标commit的父commit。**交互式rebase的时候需要使用`reword`命令将目标commit选取出来并进行message的修改。**
**因为修改了一个commit的message，因此会更新这个commit的id**
```bash
git rebase -i parentCommitId
```

下面是一个实例：

```bash
$ git log -3
commit c3d6d73712cabfb21df172d2f4f673cbf909f974 (HEAD -> master)
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 16:35:04 2020 +0800

    Modify style.scss content

commit abb8e578c1ceba076fa23fdfdc9ae2f302ae2877 (tag: testTag)
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:27:21 2020 +0800

    Move style.css to style.scss                       #### 将 Move 改为 Rename

commit f4d1584be08bf36f31b3c4442604a233ba811eb9
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:13:33 2020 +0800

    Add style.css

##########################################################################################

$ git rebase -i f4d1584be08bf36f31b3c4442604a233ba811eb9
[detached HEAD 575eb32] Rename style.css to style.scss
 Date: Sat Feb 22 13:27:21 2020 +0800
 1 file changed, 0 insertions(+), 0 deletions(-)
 rename style.css => style.scss (100%)
Successfully rebased and updated refs/heads/master.

###########################################################################################
$ git log -3
commit 4689f4d16251bd9c692f182ddcf8fa744e7c3040 (HEAD -> master)
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 16:35:04 2020 +0800

    Modify style.scss content

commit 575eb324d6933565c26c75d8e198c9348b3f482f  ### commitId发生变化
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:27:21 2020 +0800

    Rename style.css to style.scss  ########### 已经修改

commit f4d1584be08bf36f31b3c4442604a233ba811eb9
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:13:33 2020 +0800

    Add style.css

```

### 0x04 合并多个连续的commit

和修改之前的commit message的情景有些类似。也是使用`rebase`命令的交互式。**选择要合并连续commit的父commit进行rebase，并把将要合并的commit进行`squash`命令操作（挤压）。**

```bash
git rebase -i parentCommitId
```

以下是一个实例
```bash
$ git log
commit 4689f4d16251bd9c692f182ddcf8fa744e7c3040 (HEAD -> master)
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 16:35:04 2020 +0800

    Modify style.scss content

commit 575eb324d6933565c26c75d8e198c9348b3f482f
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:27:21 2020 +0800

    Rename style.css to style.scss

commit f4d1584be08bf36f31b3c4442604a233ba811eb9
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:13:33 2020 +0800

    Add style.css

commit 8df90bcccc65ee27aee90166ca65887d6415bc3b
Author: ScarboroughCoral <3249977074@qq.com>
Date:   Sat Feb 22 13:09:25 2020 +0800

    Add index+logo
##################################################################
$ git rebase -i 8df90 #################################### 将除了最早的commit合并
[detached HEAD ae9bbaf] create a complete web page
 Date: Sat Feb 22 13:13:33 2020 +0800
 1 file changed, 7 insertions(+)
 create mode 100644 style.scss
Successfully rebased and updated refs/heads/master.
###########################################################################
$ git log
commit ae9bbaf72e24972246579de21136c72328a3c94b (HEAD -> master)
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:13:33 2020 +0800

    create a complete web page

    Add style.css

    Rename style.css to style.scss

    Modify style.scss content

commit 8df90bcccc65ee27aee90166ca65887d6415bc3b
Author: ScarboroughCoral <3249977074@qq.com>
Date:   Sat Feb 22 13:09:25 2020 +0800

    Add index+logo

```

### 0x05 合并多个间隔的commit
和合并连续的commit类似。还是对commit的父commit进行reabse。**在rebase交互界面中将需要合并的间隔的commit放在一起（连续），将后续的commit进行squash即可。**
**不过在第二步添加commit message时出现问题跳出交互界面，按照提示进行`git rebase --continue`进行操作即可。**

```bash
git rebase -i parentCommitId
```

下面是一个实例：

```bash
$ git log
commit 6e5d8a876236c8bff77508be608303b600f83a74 (HEAD -> temp)
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:41:29 2020 +0800 

    Add test ################################################# 将当前commit和最早的一个commit合并

commit f4d1584be08bf36f31b3c4442604a233ba811eb9
Author: lmy <lmy@lmy.lmy>
Date:   Sat Feb 22 13:13:33 2020 +0800

    Add style.css

commit 8df90bcccc65ee27aee90166ca65887d6415bc3b
Author: ScarboroughCoral <3249977074@qq.com>
Date:   Sat Feb 22 13:09:25 2020 +0800

    Add index+logo
############################################################################

$ git rebase -i 8df90bcc
interactive rebase in progress; onto 8df90bc
Last command done (1 command done):
   pick 8df90bc
Next commands to do (2 remaining commands):
   squash 6e5d8a8 Add test
   pick f4d1584 Add style.css
You are currently rebasing branch 'temp' on '8df90bc'.

nothing to commit, working tree clean
The previous cherry-pick is now empty, possibly due to conflict resolution.
If you wish to commit it anyway, use:

    git commit --allow-empty

Otherwise, please use 'git reset'
Could not apply 8df90bc... 

####################################################################
$ git status
interactive rebase in progress; onto 8df90bc
Last command done (1 command done):
   pick 8df90bc
Next commands to do (2 remaining commands):
   squash 6e5d8a8 Add test
   pick f4d1584 Add style.css
  (use "git rebase --edit-todo" to view and edit)
You are currently rebasing branch 'temp' on '8df90bc'.
  (all conflicts fixed: run "git rebase --continue") ############################ 出现问题，使用 rebase --continue命令继续交互
#######################################################################
$ git rebase --continue
[detached HEAD 5c248b3] add index & test
 Author: ScarboroughCoral <3249977074@qq.com>
 Date: Sat Feb 22 13:09:25 2020 +0800
 2 files changed, 19 insertions(+)
 create mode 100644 index.html
 create mode 100644 readme.md
Successfully rebased and updated refs/heads/temp.
```



## Reference

- 苏玲，《Git三剑客》