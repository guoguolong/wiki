# git rebase应用-多个commit合并一个

开发时为了安全保存代码成果，可能频繁提交很多次 commit 到 git 仓库，等到功能开发完希望将这些commit合并为一个有意义的commit，可以使用`git rebase`

比如，最近3次的commit提交希望合并为一个commit

### 查看最近3次的commit提交记录

```bash
git log -3
```

结果如下：

```
Author: Koda <elon.guo@gmail.com>
Date:   Wed Mar 15 16:46:42 2023 +0800

    commit 3

commit eee3e147dda108f1052a0e06a4d79f9556689833
Author: Koda <elon.guo@gmail.com>
Date:   Wed Mar 15 16:46:09 2023 +0800

    commit 2

commit ac77664d8916e70bef32b2e344bfbd826fb09c0f
Author: Koda <elon.guo@gmail.com>
Date:   Wed Mar 15 16:45:46 2023 +0800

    commit 1
```

## 变基 (git rebase -i HEAD~3)

```bash
git rebase -i HEAD~3
```

进入了vi编辑器窗口

```
pick ac77664 commit 1
pick eee3e14 commit 2
pick 0ad90c5 commit 3

# Rebase 444dda9..0ad90c5 onto 444dda9 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified); use -c <commit> to reword the commit message
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

* pick: 保留该 commit
* squash: 将该 commit 和前一个 commit合并

### 1. 合并策略

所以，这里我们将 commit2和commit3 前面的 `pick` 修改为 `squash` (缩写 `s` 也可)：

```diff
pick ac77664 commit 1
- pick eee3e14 commit 2
+ s eee3e14 commit 2
- pick 0ad90c5 commit 3
+ s 0ad90c5 commit 3

# Rebase 444dda9..0ad90c5 onto 444dda9 (3 commands)
#
# Commands:
.....
```

修改后按wq 保存后进入下一步。

### 2. 编辑commit信息

又进入了vi编辑器，显示原有 commit 信息

```
 This is a combination of 3 commits.
# This is the 1st commit message:

commit 1

# This is the commit message #2:

commit 2

# This is the commit message #3:

commit 3

# Please enter the commit message for your changes. Lines starting
....
```

修改为一个有意义的commit信息内容：

```
Feature 1
# Please enter the commit message for your changes. Lines starting
....
```

修改后按wq 保存退出。屏幕显示：

```
Successfully rebased and updated refs/heads/master.
```

## 查看 rebase 结果

```bash
git log	
```

显示如下：

```
commit dad36f4ae37c8b50d09f003d9fa611ea69ebe552 (HEAD -> master)
Author: Koda <elon.guo@gmail.com>
Date:   Wed Mar 15 16:45:46 2023 +0800

    Feature 1

commit 444dda90fcd28a3fca046690eba7c5d37675f029
Author: Koda <elon.guo@gmail.com>
Date:   Wed Mar 15 16:44:12 2023 +0800

    initial commit
```



## 提交 commit 到远程服务器

必须强制推送到远程

```
git push -f
```

通常我们将 dev 分支会一个完整功能的多次commit合并为一个，然后在通过`cherry-pick`到master分支上。