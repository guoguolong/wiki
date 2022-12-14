# Mac终端bash显示git分支名记命令自动补全

## 命令行显示git分支名

此方法终端必须使用 bash。首先进入 home 目录：

编辑 ~/.bash_profile文件：

```bash
vim ~/.bash_profile
```

添加如下代码
```shell
function git_branch {
   branch="`git branch 2>/dev/null | grep "^\*" | sed -e "s/^\*\ //"`"
   if [ "${branch}" != "" ];then
       if [ "${branch}" = "(no branch)" ];then
           branch="(`git rev-parse --short HEAD`...)"
       fi
       echo " ($branch)"
   fi
}

export PS1='\u@\h \[\033[01;36m\]\W\[\033[01;32m\]$(git_branch)\[\033[00m\] \$ '

```

保存后重新打开一个终端窗口（等同`source ~/.bash_profile`)，就显示 git 分支就完成了。

## 自动补全命令

用 `homebrew` 安装 `bash-completion` 软件包：

```bash
brew install bash-completion
```

然后把下面内容添加到你的`~/.bash_profile`，与上边添加操作一样。

```shell
if [ -f $(brew --prefix)/etc/bash_completion ]; then
    . $(brew --prefix)/etc/bash_completion
fi
```

使用 Tab 键自动补全。
