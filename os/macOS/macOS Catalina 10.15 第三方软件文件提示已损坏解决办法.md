最近有小伙伴升级了 macos Catalina 10.15 系统，除了一些软件不兼容外，很多升级的Mac用户会发现在新安装的软件在运行时会提示“已损坏”、“无法验证开发者”等问题，下面详细说下解决方法。

1. 首先确保系统安全设置已经改为任何来源。

```
sudo spctl --master-disable
```

1. 打开任何来源后，到应用程序目录中尝试运行软件，如果仍提示损坏，请进行第3步。
2. 如果前面两步仍有问题，请打开终端，在终端中粘贴下面命令：

```
sudo xattr -r -d com.apple.quarantine <应用所在目录>
```

快捷方法找到应用所在目录：打开应用程序目录，找到应用图标，拖拽到终端xattr命令后面，按回车后输入密码执行，比如Sketch的命令是 sudo xattr -r -d com.apple.quarantine /Applications/Sketch.app/