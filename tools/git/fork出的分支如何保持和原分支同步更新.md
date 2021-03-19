## 先用Git remote 这个仓库管理工具 看一下有没有添加upstream

或者git remote -v 可以显示仓库名称和他的地址

这个upstream 就表示上游版本库，如果发下没有upstream 的话，那就自己添加喽，git remote add upstream xxx.git

## git pull upstream

假设当前工作分支是master，那么该操作会把upstream合并到当前分支。如果本地没有修改和冲突，本地仓库就被更新了 本步骤操作相当于：

> git fetch upstream git merge upstream/master

## git push

提交本地仓库的更新到远程仓库