## Windows平台方便

```c
#include <stdio.h>
#include <string.h>
#include <windows.h>
#include <stdlib.h>
 
int main(int argc,char *argv[]){
    printf("%s\n",argv[0]); //用主函数参数自带的 argv[0] 输出路径
 
    char path[100];
    GetModuleFileName(NULL, path, 100); //调用win api 获得路径
    printf("%s\n",path);
 
    char *p = strrchr(path,'\\'); //截取到最后的"\",之后是XXX.exe文件名
    printf("%s\n",p);
 
    char *name = p + 1; //截去"\",只留下文件名
    printf("%s\n",name); //输出文件名
 
    char path2[100];
    GetCurrentDirectory(100, path2); //获取当前进程目录路径，与上面的不同，具体可以自己试试
    printf("%s\n",path2);
 
    system("pause");
    return 0;
}
```

## macOS怎么做？

## linux怎么做