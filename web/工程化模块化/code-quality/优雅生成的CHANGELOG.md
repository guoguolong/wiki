[toc]
# 优雅生成 `CHANGELOG.md`

## I. `@commitlint/config-conventional` 

本文参考：https://mokkapps.de/blog/how-to-automatically-generate-a-helpful-changelog-from-your-git-commit-messages/

###  1. `commitlint`检查字符串是否符合`@commitlint/config-conventional`声明的规则

```bash
npm i @commitlint/cli @commitlint/config-conventional -D
```

创建 commitlnt 配置文件 `.commitlintrc.json`：

```json
{
  "extends": ["@commitlint/config-conventional"]
}
```

运行 commitlint：

```bash
echo 'initial commit' | npx commitlint
```

其检查'initial commit'字符串不符合`@commitlint/config-conventional`声明的规则， 返回错误：

```
⧗   input: initial commit
✖   subject may not be empty [subject-empty]
✖   type may not be empty [type-empty]

✖   found 2 problems, 0 warnings
ⓘ   Get help: https://github.com/conventional-changelog/commitlint/#what-is-commitlint
```

运行
```bash
echo 'fix: initial commit' | npx commitlint
```

不返回错误，说明符合`@commitlint/config-conventional`声明的规则。

* 也可以检查一个文件的内容是否符合 commitlint声明的规则

假设有文件 `message.md`，内容如下：

```md
initial commit
```

运行

```
npx commitlint --edit ./message.md
```

同上，会返回错误提示；如果`initial commit`添加前缀`fix:`则没有任何错误信息返回。

### 2. `husky` 使提交信息（message字符串）进行 commitlint检查

安装和初始化 husky

```bash
npx husky-init && npm install
```

修改 .husky/pre-commit：

```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx --no-install commitlint --edit "$1"
```

然后下次提交git的时候，就可以触发 commitlint执行，检查 commit msg是否满足 `@commitlint/config-conventional` 规则要求，不符合不予提交。

> 注：实际使用过程中发现：第一条非法commit信息可以提交，但是当提交第二条信息时，会检查上一条信息，如果不合法会给出错误提示，此时不得不通过
>
> ```bash
> git commit --amend --no-verify
> ```
>
> 来修正上一条信息。

### 3. `standard-version`抽取commit log生成CHANGELOG.md

前面两步确保 git commit信息符合fix，feat等开头的 [Conventional Commits Specification](https://www.conventionalcommits.org)，接下来就可以从这些commit log里产生规整的 CHANGELOG了

```bash
npm i standard-version -D
```

* package.json增加便利的 `scripts`

```json
 "scripts": {
    "release": "standard-version",
    "release:minor": "standard-version --release-as minor",
    "release:patch": "standard-version --release-as patch",
    "release:major": "standard-version --release-as major"
  },
```

* 创建`.versionrc.json`(或在 package.json中创建 standard-version 节点) ，声明不同前缀的commit log是否显示到对应文档、显示的标签名等信息。

```json
{
    "types": [
      {"type": "feat", "section": "Features"},
      {"type": "fix", "section": "Bug Fixes"},
      {"type": "chore", "hidden": true},
      {"type": "docs", "hidden": true},
      {"type": "style", "hidden": true},
      {"type": "refactor", "hidden": true},
      {"type": "perf", "hidden": true},
      {"type": "test", "hidden": true}
    ],
    "commitUrlFormat": "https://github.com/mokkapps/changelog-generator-demo/commits/{{hash}}",
    "compareUrlFormat": "https://github.com/mokkapps/changelog-generator-demo/compare/{{previousTag}}...{{currentTag}}"    
}
```

> 注：commitUrlFormat 和 compareUrlFormat应填入实际项目的git地址前缀。

#### A. 第一次产生 CHANGELOG.md

首先，工程的package.json声明version首次版本名，如: 1.0.0

```bash
npx standard-version --first-release
```

或执行 scripts `npm run release -- --first-release`

将产生类似下面这样的 CHANGELOG.md，同时创建了名字为`v1.0.0`的git tag。

```markdown
# Changelog

All notable changes to this project will be documented in this file. See [standard-version](https://github.com/conventional-changelog/standard-version) for commit guidelines.

## 1.0.0 (2023-01-04)


### Bug Fixes

* add section to Home components fd3ee03
* h1 tags in App 872000e
* how the last message 8cfc1a0
* remove message 8be1866
* section appear in app.tsx 4054ea9
* update App d72d06e
* update app.js 09f6597
* update README 9961683

### Feaures

* add api.html f2424c9
* add home.html 8f90cc2
* add nav to Home componnents 6a0e409
* add release-log f5a7dd0
* initial commit da4f2a3
```

#### B. 增量产生 CHANGELOG.md

第二次产生新版本的CHANGELOG，执行 `npx standard-version`时，不再需要 `-- --first-release`参数，可选使用 `--release-as`，结果会：

1. 自动将 `package.json`中的`version`增1（以下称下一版本）；
2. 创建名字为`v<下一版本>` 的git tag
3. 收集归并上一版本和`下一版本`之间的commit log，更新 `CHANGELOG.md`

接上面的例子：进行一次git提交，commit log是：`feat: 改进APP`，然后执行 

```bash
npx standard-version --release-as patch
```

版本号从 `1.0.0`增加到`1.0.1`，可以看到`CHANGELOG.md`更新如下：
```markdown
# Changelog

All notable changes to this project will be documented in this file. See [standard-version](https://github.com/conventional-changelog/standard-version) for commit guidelines.

### [1.0.1](https://github.com/mokkapps/changelog-generator-demo/compare/v1.0.0...v1.0.1) (2023-01-04)


### Feaures

* 改进APP 141c769

## 1.0.0 (2023-01-04)


### Bug Fixes

* add section to Home components fd3ee03
* h1 tags in App 872000e
* how the last message 8cfc1a0
......
```

### 4. 定制前缀类型
现在，想在 `@commitlint/config-conventional` 基础上增加对名字为`improvement`的前缀支持，并能产生并显示在 `CHANGELOG.md`文件中，方法如下：
* `commitlint.config.js`
```javascript
module.exports = {
  extends: [
    '@commitlint/config-conventional'
  ],
   rules: {
      'type-enum': [
        2, 'always', ['upd', 'feat', 'fix', 'refactor', 'docs', 'chore', 'style', 'revert', 'improvement']
      ],
    }
}
```

* `.versionrc.json`说明`improvement`前缀commit log的显示规则
```diff
{
  "standard-version": {
    "types": [
      {
        "type": "feat",
        "section": "Feaures"
      },
      {
        "type": "fix",
        "section": "Bug Fixes"
      },
+      {
+        "type": "improvement",
+        "section": "Improvments"
+      },
   ....
  }
}
```

再次执行 `npx standard-version`， `CHANGELOG.md`的内容可能如下：

```markdown
# Changelog

All notable changes to this project will be documented in this file. See [standard-version](https://github.com/conventional-changelog/standard-version) for commit guidelines.

## [1.2.0](https://github.com/mokkapps/changelog-generator-demo/compare/v1.1.1...v1.2.0) (2023-01-04)


### Improvments

* a batch update 389080b
* add improvment 2e542f5
...
```


## II. 用`auto-changelog` 生成 `CHANGELOG.md`

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

