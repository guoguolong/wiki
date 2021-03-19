- git pull 总是提示输入用户和密码，怎么办？ 执行： git config --global credential.helper store

1 不安装gitweb直接安装apt-get install git-core, 可以提交／更新git仓库

1. 安装gitweb 按照文档编译好，复制到apache文档目录

- 缺少cgi perl -e shell -MCPAN 进入控制台输入install CGI
- 提示缺少Time/HiRes.pm，执行 yum install perl-Time-HiRes 步骤4、5设置了两个VirtualHost在apache配置，注意如果使用多个<VirtualHost *.80>，必须注释掉conf里的NameVirtualHost *:80，否则提示：....the first has precedence, perhaps you need a NameVirtualHost directive. 5.5. git config —global core.sharedRepository 775

1. git helloworld设置 1). 本地创建初始目录pro1, cd pro1执行git init 2). 回到上级目录，执行git clone --bare pro1 pro1.git 创建了pro1.git目录) 3). scp -r pro1.git <[username@pro.com](http://mailto:username@pro.com):/git_folder/> (复制pro1.git到远程目录) 4). 连接远程仓库到pro1目录，进入pro1目录，运行 git remote add origin <[username@pro.com](http://mailto:username@pro.com):/git_folder/> (origin相当于后面URL的别名) 5). 提交pro1目录下文件到远程仓库 git add *.php git commit -m 'initial commit' // 加到本地仓库 git push origin master //加到远程仓库 以后就只要执行git push就好了
2. git checkout -f <branch name> 切换到指定分支，并把工作目录下文件修改还原，如果新增文件则保留在原地

## git branch

1). git branch -a 查看所有分支 2). git branch <branchname> 创建分支 3). git branch -d <branchname> 删除分支

## TAG, Merge Branch.

github github上clone下来代码，如果想push,需要设置user $ git config --global github.user defnngj //github 上的用户名 $ git config --global github.token e97279836f0d415a3954c1193dba522f

1. 让fork出来的库与原仓库代码保持同步 a).github上初次下载自己的项目(guoguolong/magento)，使用 git clone http://github.com/guoguolong/magento b). 如果想加入另外一个远程仓库（如：magento/magento2），使用 git remote add upstream http://github.com/magento/magento2.git c). 现在可以从另外一个仓库(magento/magento2)合并代码到当前本地仓库（guoguolong/magento） git pull origin master d). 如果git pull origin master有冲突，需要手动解决，否则自动合并到本地仓库，然后将本地仓库内容传输给远程仓库 git push origin master 注：此时可能要求你输入github用户名和密码,输入一次，下次就不用输入了

# git常用配置：

git config core.ignorecase false 无密码提示pull仓库方法： 在家目录添加文件.netrc,内容为： machine [git.babyun.cn](http://git.babyun.cn) login zhaojia password zj123456

## 文件属性改变不要git变化

git config core.fileMode false

## git分支运用

目标： 1， 工作目录1: 创建一个本地分支命名为dev01,然后push到服务器上命名为dev01

```
git checkout -b dev01 # 创建一个本地分支(基于当前WP所在的分支)
// git checkout -b dev01 dev-0.x # 基于dev-0.x创建一个本地分支
//修改readme.md并提交
git add readme.md
git commit -m 'modify readme file'

// 同步到远程服务器
git push --set-upstream origin dev01 #经过这样的设置，下一次简写git push就可以提交到远程dev01分支里去了
```

2，工作目录2: pull服务器名为dev01的分支到本地的dev01，修改东西，然后push回服务器

```
git pull # 然后执行git branch -a 就可以看到远程分支dev01了
//接下来执行
git checkout -b dev01 origin/dev01 #这样就把本地dev01和远程dev01分支关联起来了
```

3，删除分支

```
// work1工作目录的操作
//删除远程分支
git branch -r -d origin/dev01
git push origin :dev01 #同步到远程仓库 （以被其他clone仓库感知）

// 删除本地分支
git branch -D dev01
```

对于其他clone的仓库，如法炮制，如果大家来自同一个上游仓库，就不必执行git push这一步了

# git tag运用

```
## 克隆1: 添加tag，并同步到远程仓库.
git tag -a v0.1.0 -m 'Intial Release v0.1.0'
也可以轻量级创建tag： git tag v0.1.0
git push origin --tags #提交所有tags
git push origin 0.1.0 #提交指定tag

## 克隆2: 从远程仓库同步tag，并查看
git pull
git tag -l #查看仓库里有多少个tag
git checkout v0.1.0 #也可以进入tag看下源代码

// 这时候 git 可能会提示你当前处于一个"detached HEAD" 状态.
// 因为tag相当于是一个快照，是不能更改它的代码的，
// 如果要在tag代码的基础上做修改，你需要一个分支： 
git checkout -b branch_name tag_name
这个例子可以是：
git checkout -b v0.1.0-fix v0.1.0
 
这样会从 tag 创建一个分支，然后就和普通的 git 操作一样了。 
## 打包下载tag
git archive --format --output=v0.1.0.zip 0.1.0

## 删除 tag
git tag -d 0.1.0 #删除本地tag
git push origin :refs/tags/0.1.0 #删除远程tag
```

## git config配置用户和邮件

```
git config --global user.name "Your Name"
git config --global user.email you@example.com
全局的通过vim ~/.gitconfig来查看

git config user.name "Your Name"
git config user.email you@example.com
局部的通过当前路径下的 .git/config文件来查看
```

=====================================

## git 比较文件的历史版本差异

git diff e2a904 f66eee package.json

这个命令可以查看所有历史及详细哦 git log --follow -p package.json

## GIT丢弃所有本地修改的方法

```
git checkout . #本地所有修改的。没有的提交的，都返回到原来的状态
git stash #把所有没有提交的修改暂存到stash里面。可用git stash pop回复。
git reset --hard HASH #返回到某个节点，不保留修改。
git reset --soft HASH #返回到某个节点。保留修改

git clean -df #返回到某个节点
git clean 参数
    -n 显示 将要 删除的 文件 和 目录
    -f 删除 文件
    -df 删除 文件 和 目录
```

## git 显示中文文件名

不对0x80以上的字符进行quote，解决git status/commit时中文文件名乱码

```
git config --global core.quotepath false
```

## cherry-pick

http://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html