To enable PHP in Apache add the following to httpd.conf and restart Apache:

```
LoadModule php7_module /usr/local/opt/php70/libexec/apache2/libphp7.so

<FilesMatch .php$>

    SetHandler application/x-httpd-php

</FilesMatch>
```

Finally, check DirectoryIndex includes index.php

```
DirectoryIndex index.php index.html
```

The php.ini file can be found in: /usr/local/etc/php/7.0/php.ini

## ✩✩✩✩ Extensions ✩✩✩✩

If you are having issues with custom extension compiling, ensure that you are using the brew version, by placing /usr/local/bin before /usr/sbin in your PATH:

```
PATH="/usr/local/bin:$PATH"
```

PHP70 Extensions will always be compiled against this PHP. Please install them

using --without-homebrew-php to enable compiling against system PHP.

## ✩✩✩✩ PHP CLI ✩✩✩✩

If you wish to swap the PHP you use on the command line, you should add the following to ~/.bashrc,

~/.zshrc, ~/.profile or your shell's equivalent configuration file:

```
export PATH="$(brew --prefix homebrew/php/php70)/bin:$PATH"
```

## ✩✩✩✩ FPM ✩✩✩✩

==

To launch php-fpm on startup:

```
mkdir -p ~/Library/LaunchAgents

cp /usr/local/opt/php70/homebrew.mxcl.php70.plist ~/Library/LaunchAgents/

launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php70.plist
```

The control script is located at /usr/local/opt/php70/sbin/php70-fpm

OS X 10.8 and newer come with php-fpm pre-installed, to ensure you are using the brew version you need to make sure /usr/local/sbin is before /usr/sbin in your PATH:

PATH="/usr/local/sbin:$PATH"

You may also need to edit the plist to use the correct "UserName".

Please note that the plist was called 'homebrew-php.josegonzalez.php70.plist' in old versions

of this formula.

To have launchd start homebrew/php/php70 now and restart at login:

brew services start homebrew/php/php70

==> Summary

🍺 /usr/local/Cellar/php70/7.0.9: 332 files, 49.3M

## 启动php-fpm

> brew services start homebrew/php/php70