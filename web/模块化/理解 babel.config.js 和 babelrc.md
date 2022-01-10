# 理解 babel.config.js 和 babelrc

之前遇到过一个 babel 转换的问题，在下面这个 monorepo 的目录结构中

```text
.
├── common
│   └── utils.js
├── package.json
└── packages
    └── modulea
        ├── .babelrc
        ├── index.js
        ├── package.json
        └── webpack.config.js

3 directories, 6 files
```

`modulea` 引用了外层 `common/utils.js`，结果在打包时发现`common/utils.js` 的代码并没有被翻译，但是 `modulea` 目录内的代码却能正常转换，觉得很奇怪，直到我将 babel 的配置文件由 `.babelrc` 改成了 `babel.config.js` 才能正常工作。于是就深究了一下两个配置文件的区别，觉得有点东西理解起来有点绕，于是写下这篇博客记录一下。

babel 7.x 以上开始支持两种类型的配置文件, 分别是`.babelrc` 和 `babel.config.js` ，在官方的描述里:

- `babel.config.js` 当前项目维度 (Project Wide)的配置文件，相当于一份全局配置，如果 babel 决定应用这个配置文件，则一定会应用到所有文件的转换。
- `.babelrc` 相对文件(File Relative)的配置文件， `babel` 决定一个 js 文件应用哪些配置文件时,会执行如下策略: 如果要转换的这个 js 文件在当前项目内，则会先递归向上搜索最近的一个 `.babelrc` 文件(直到遇到package.json目录)，将其与全局配置合并。如果这个 js 文件不在当前项目内，则只应用全局配置，忽略与这个文件相关的 `.babelrc` 。

这两个我更愿意将其称为全局配置 (babel.config.js) 和局部配置 (.babelrc) 便于理解一些。

由此便引入了一个非常关键的对 **当前项目** 的定义问题。

这个定义在官方文档就有说明

> New in Babel 7.x, Babel has a concept of a "root" directory, which defaults to the current working directory. For project-wide configuration, Babel will automatically search for a babel.config.json file, or an equivalent one using the supported extensions, in this root directory.

大白话就是: 当前所在目录即为***当前项目***根目录，babel 会读取当前所在目录的 babel.config.js 作为全局配置。

下面我们分为几种 case 分别看一下 babel 的应用路径.

### Case 1 所有代码都在一个单一工程中

这种情况下 babel.config.js 和 .babelrc 作用几乎一样，唯一不同是 babelrc 可以针对子目录分别配置.

```text
.
├── .babelrc
├── babel.config.js
├── package.json
└── src
    ├── index.js
    └── subdir
        ├── .babelrc
        └── utils.js

2 directories, 6 files
```

这里, 我们在顶层目录运行 `babel src/**/*.js --out-dir dist` 。此时会读取当前目录的 `babel.config.js` 作为全局配置，根据前文所述规则应用的配置如

- `src/index.js` 会应用 `babel.config.js` + `.babelrc`。
- `src/subdir/utils.js` 会应用 `babel.config.js` + `src/subdir/.babelrc`

如果我们在 src 目录执行 babel **/*.js --outdir ，此时则不会应用 babel.config.js 的规则，但会应用 src 外层 .babelrc 的配置文件。

此时：

- `index.js` 会应用 `../.babelrc`
- `subdir/index.js` 会应用 `subdir/.babelrc`

这里貌似有点不对劲， index.js 为什么会应用外层 .babelrc , 这个配置文件明明在当前项目"外"。

这里再回顾一下之前说的。

> babel 决定一个 js 文件应用哪些配置文件时,会执行如下策略: 如果这个 js 文件在当前项目内，则会先递归向上搜索最近的一个 .babelrc 文件(直到遇到package.json目录)，将其与全局配置合并。如果这个 js 文件不在当前项目内，则只应用全局配置，并忽略与这个文件相关的 .babelrc 。

babel决定是否执行向上搜索 .babelrc 这个策略时依据的是当前 js 文件的位置，而不是 .babelrc 的位置。此时的 index.js 在项目范围内，所以会执行向上搜索 .babelrc 的策略。

### Case 2 多工程下的项目配置问题

对于 monorepo 项目，全局配置文件就有用的多了。首先看我开头遇到的问题

```text
.
├── common
│   └── utils.js
├── package.json
└── packages
    └── modulea
        ├── .babelrc
        ├── index.js
        ├── package.json
        └── webpack.config.js

3 directories, 6 files
```

这里我在 packages/modulea 目录里执行打包命令时， .babelrc 只会应用 packages/modulea 里的文件，对于 common 目录下的文件则无法执行转换。即便我们在 common 目录中添加一个 .babelrc 也无济于事，因为 common 里的代码在当前的项目范围外面，只会应用全局配置。所以这种场景就非常适合使用 babel.config.js 。

```text
.
├── common
│   └── utils.js
├── package.json
└── packages
    └── modulea
        ├── babel.config.js <--- 
        ├── index.js
        ├── package.json
        └── webpack.config.js

3 directories, 6 files
```

改成 babel.config.js 之后 common 目录就能正确进行转换了。

### 为了清晰记忆上面的规则我来稍微总结一下:

babel 会在当前执行目录搜索 babel.config.js, 若有则读取并作为全局配置，若无则全局配置为空。然后在转换一个具体的js文件时会去判断，如果这个文件在当前执行目录外面，则只应用全局配置。如果这个文件在当前执行路径内，则会去基于这个文件向上搜索最近的一个 .babelrc ，将其与全局配置合并作为转换这个文件的配置。