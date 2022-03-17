有时运行 xcodebuild 等相关工具出现错误：

```
xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```

如果已经安装了xcode（可能都不用安装），可以执行下面命令
```
sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer
```