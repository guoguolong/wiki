由于getch输入不需要按回车键，故在一些需要快速输入的场合很有用，但是该函数不在标准库里，用到各系统的系统库。

## Windows用conio.h

```c
#include <conio.h>

int main() {
    getch();
    return 0;
}
```

## Unix-like（包括macOS）系统用 curses.h

```c
#include <curses.h>
#include <stdio.h>

int main() {
    initscr(); //生成屏幕并初始化库，使用Curses库时必须最先调用该函数
    
    char code = getch();
    
    endwin();
    printf("code: %c\n", code);
    
    return 0;
}
```

编译时指定库文件名：
```
gcc -lcurses main.c
```
比如，对于macOS，链接阶段自动找到 /usr/lib/libcurses.dylib，因此 -lcurse 不可或缺。

在Unix like系统下使用 getch 必须被 initscr() 和 endwin(); 包裹。