# Babel

支持ES6， React.js, jsx代码高亮

# codeFormatter

一款可以对html、JS、CSS、PHP、python代码格式化的sublime插件

默认快捷键ctrl+alt+F，默认可以对html、js、css格式代码， 如果想对PHP格式化，需要PHP5.6以上版本，而且需要配置sublime的(package setttings > codeFormatter > settings user)中设定php.exe的路径 ，如下：

```json
{
    "codeformatter_php_options":
    {
        "syntaxes": "php", // Syntax names which must process PHP formatter
        "php_path": "/usr/local/bin/php", // Path for PHP executable, e.g. "/usr/lib/php" or "C:/Program Files/PHP/php.exe". If empty, uses command "php" from system environments
        "format_on_save": false, // Format on save
        "php55_compat": false, // PHP 5.5 compatible mode
        "psr1": false, // Activate PSR1 style
        "psr1_naming": false, // Activate PSR1 style - Section 3 and 4.3 - Class and method names case
        "psr2": true, // Activate PSR2 style
        "indent_with_space": 4, // Use spaces instead of tabs for indentation
        "enable_auto_align": true, // Enable auto align of = and =>
        "visibility_order": true, // Fixes visibility order for method in classes - PSR-2 4.2
        "smart_linebreak_after_curly": true, // Convert multistatement blocks into multiline blocks
        // Enable specific transformations. Example: ["ConvertOpenTagWithEcho", "PrettyPrintDocBlocks"]
        // You can list all available transformations from command palette: CodeFormatter: Show PHP Transformations
        "passes": [],
        // Disable specific transformations
        "exclude": []
    }
}
```

# Nunjucks

插件下载地址： https://github.com/mogga/sublime-nunjucks/blob/master/Nunjucks.tmLanguage

打开Sublime Text，Preferences -> Browse Packages然后将会打开Finder显示Sublime Text的Packages目录（或者直接打开/Users/***/Library/Application Support/Sublime Text 3/Packages），你可以在这里新建一个文件夹，命名为Nunjucks，然后将下载的Nunjucks.tmLanguage文件复制到新建的Nunjucks文件夹中， 需要修改该文件，使其支持.njk扩展名。

重启Sublime Text，再次打开.njk文件的时候，已经可以高亮显示了。

# phpfmt

一款格式化PHP的插件 安装后，打开Sulime Text3的Preference->Browse Packages进入文件目录，打开phpfmt.sublime-settings，配置修改如下：

```json
{
	"autocomplete": true,
	"autoimport": true,
	"enable_auto_align": false,
	"format_on_save": true,
	"ignore": "Parent",
	"indent_with_space": 4,
	"passes":
	[
		"ReindentSwitchBlocks",
		"OrderAndRemoveUseClauses",
		"PSR2EmptyFunction",
		"StripSpaceWithinControlStructures",
		"ShortArray",
		"SpaceBetweenMethods",
		"AutoSemicolon"
	],
	"php_bin": "/usr/local/bin/php",
	"psr2": true,
	"smart_linebreak_after_curly": true,
	"version": 4,
	"visibility_order": true,
	"yoda": true
}
```