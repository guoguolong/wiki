## 概览仓库情况: 多少分支、多少tag

git clone代码后进入工作目录：

```
git branch -a #查看远程仓库所有分支 。
git tag -l #查看远程仓库所有标签 。

以下命令也可以
git ls-remote --tags origin
git ls-remote --heads origin
git ls-remote origin
```

## 导出干净的某个分支代码.

```
git archive --format zip --output "./express.zip" master -0 
# 将代码导出并zip 打包后放在当前目录下，`output.zip`就是需要的文件，`-0`的意思是不压缩
```



## 概览仓库远程连接情况

```
git remote -v
```

## rebase

使远程仓库和本地仓库完全一致：本地的分支和远程分支一致 比较远程仓库和本地仓库的差别

## git revert

就是让你working copy恢复到某一个版本，但是并不改变代码仓库的版本； 在你revert之后，你经过一系列修改在git add/git commit就会产生一个新的版本

```
git revert 9511208 
// 在这个版本上可以做必要的修改,然后 
git add . 
git commit -m 'Fix bugs'
```

## git diff

```
git diff
working copy和staging区域对比

git diff --cached (等同--staged)
stging区域和仓库对比

git diff HEAD
working copy和staging和仓库一起对比

git diff 6720722 b28b566
明确比较两个版本差异，一般选前者为更早的历史版本

git diff master ^origin/master
比较本地master分支和远程master的差别
```