# 1. 安装git-filter-repo

[官方Git库有很详细的说明](https://github.com/newren/git-filter-repo/blob/main/INSTALL.md)

这里选择通过pip安装，windows需要手动安装python或者conda

```bash
pip install git-filter-repo
```

# 2. 找出要删除的大文件

按照文件大小升序排列并取最后40个文件



```bash
git rev-list --objects --all | grep "$(git verify-pack -v .git/objects/pack/*.idx | sort -k 3 -n | tail -40 | awk '{print$1}')"
```

注意嵌套语句会导致排序错乱，可以拆开逐个寻找文件



```ruby
git verify-pack -v .git/objects/pack/*.idx | sort -k 3 -n | tail -40
git rev-list --objects --all | grep 文件对应的id
```

# 3. 彻底删除大文件

[官方文档列出了各种功能，在此就不一一展示了](https://htmlpreview.github.io/？https://github.com/newren/git-filter-repo/blob/docs/html/git-filter-repo.html)

由于本人不小心上传了大量csv文件，因此使用正则匹配将所有csv文件删除



```bash
git filter-repo --force --invert-paths --path-regex .+\.csv
```

# 4. 强制推送到远端

由于修改了历史的commit，因此仓库无法正常推送到远端，需要进行强制推送

```bash
git push -f origin master
```

# 5. 额外说明

以上命令都是在linux下，如果使用windows系统的话，可以先通过 conda 安装 git-filter-repo，再通过git自带的MINGW运行带有`|`这种cmd不兼容的命令，最后通过 conda 运行`git filter-repo