## 强制解锁

> svn unlock --force app/config/env/local/config.tsp.js

## 查看svn仓库的所有作者.

> svn log|grep '^r'|awk '{print $3}'|sort|uniq

## svn切换分支

> svn switch ^/branches/dev

类似与git checkout [branch_name]

## svn revert 还原整个目录

svn revert -R .

## ignore多个目录或文件

```
svn propset svn:ignore "node_modules"$'\n'"package-lock.json" .
```