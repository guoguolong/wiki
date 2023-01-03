# 生成优雅的 `CHANGELOG.md`

## I. 用`auto-changelog` 生成 `CHANGELOG.md`

`auto-changelog`依据 git 管理的工程的`tag`生成`tag`版本之间的git log，生成 `CHANGELOG.md`。

```bash
npm i -g auto-changelog
```

### 使用要求

* auto-changelog 版本大于 1.7.2
* git项目必须是一个有合法 package.json 的 工程
* git项目的 tag 命名符合 [semver](https://semver.org/) 规则

### 生成 Changelog

```bash
auto-changelog
```

将会生成 `CHAGELOG.md`

### 如何写优雅的 Commit Log

[Keep A Changelog](https://keepachangelog.com/)

### 快速 git tag 命令

 ```bash
# grep -B 4 参数使查找日志时显示前面4行
git log | grep 'v0.' -B 4

# 生成轻量 tag
git tag <tag name> <commit id>
# 查看所有 tag
git tag -l
# 删除本地 tag
git tag -d <tag name>
# 删除远程 tag
git push origin --delete <tag name>
# 推所有的 tag 到远程服务器
git push origin --tags
 ```

### 参考
> https://www.npmjs.com/package/auto-changelog

## II. 最流行的：用 `@commitlint/config-conventional` 生成 `CHANGELOG.md`

https://mokkapps.de/blog/how-to-automatically-generate-a-helpful-changelog-from-your-git-commit-messages/

### 如何写优雅的 Commit Log

[Conventional Commits Specification](https://www.conventionalcommits.org)

### 参考
https://www.npmjs.com/package/@commitlint/config-conventional
