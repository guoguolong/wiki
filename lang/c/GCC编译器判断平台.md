写跨平台c/c++程序的时候，最终使用的操作系统不确定。对于有些特殊的函数来说，标准库里没有，但在windows和linux下函数名称不一样。

比如：字符串忽略大小写比较，windows下使用stricmp，linux下使用strcasecmp，这时为了能在两种操作系统中都能正确使用，就需要判断操作系统的类型，在不同的OS下调用相应的函数。

```c
#include <stdio.h>
#include <stdlib.h>

#ifdef _WIN32
    // define something for Windows (32-bit and 64-bit, this part is common)
    #ifdef _WIN64
        #define OS "_WIN64" // define something for Windows (64-bit only)
    #else
        #define OS "_WIN32" // define something for Windows (32-bit only)
        
    #endif
#elif __APPLE__
    #include "TargetConditionals.h"
    #if TARGET_IPHONE_SIMULATOR
        // iOS Simulator
    #elif TARGET_OS_IPHONE
        // iOS device
    #elif TARGET_OS_MAC
        #define OS "MACOS"
        // Other kinds of Mac OS
    #else
    #   error "Unknown Apple platform"
    #endif
#elif __ANDROID__
    // android
#elif __linux__
    // linux
#elif __unix__ // all unices not caught above
    // Unix
#elif defined(_POSIX_VERSION)
    // POSIX
#else
    #error "Unknown compiler"
#endif

#ifndef OS
#define OS "UNKOWN_OS"
#endif

int main() {
    printf("OS: %s\n", OS);
    return 0;
}
```

解释：

- windows32/64平台_WIN32都会被定义，而_WIN64只在64位windows上定义，因此要先判断_WIN64
- 所有的apple系统都会定义 **APPLE**，包括MacOSX和iOS
- TARGET_IPHONE_SIMULATOR 是 TARGET_OS_IPHONE 的子集， TARGET_OS_IPHONE 是 TARGET_OS_MAC的子集。也就是说iOS模拟器上会同时定义这三个宏。因此判断的时候要先判断子集。
- 另外mac上可以用以下命令行获取GCC定义的预编译宏： gcc -arch i386 -dM -E - < /dev/null | sort （i386可替换为arm64等）