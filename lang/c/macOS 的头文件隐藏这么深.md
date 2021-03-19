## linux 下引用C、C++标准库、其他库的头文件一般都在：

- /usr/include
- /usr/local/include
- /usr/lib/gcc-lib/xxx/xxx/include

一般安装的开源库也都会往这几个目录下放，都还是挺好找的。

但 macOS 上就完全不一样了，上面这几个目录要么没有，要么只有几个文件，完全找不到想要的。

在哪儿呢？

macOS 上的头文件、库文件都被 XCode 接管了，也就是说不安装 XCode 很多开发都是做不了的，安装了 XCode 后一切都妥妥的。 开发通用的头文件都在这里：

```
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include
```

很多库在这里：

```
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/System/Library/Frameworks
```

Frameworks 里面基本都有 Headers 文件夹，也是些头文件，和 include里的基本相同，比如比较 python 的：