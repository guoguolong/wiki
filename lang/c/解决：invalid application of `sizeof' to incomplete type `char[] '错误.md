最近做一个DIY玩具，遇到的这个问题： 解决：invalid application of sizeof' to incomplete typechar[] ‘错误

```c
//a.c文件
#include "a.h"
char array[]={0XED,0X34,0X40,0X34};
//a.h文件
#ifndef _A_H
#define _A_H
#define size   (sizeof(array)/sizeof(array[0]))
//main.c
#include "a.h"
extern char zimo1[];
u16 tmp;
char *p;
int main(void) {  
    int  i;
    while(1)
    {
        p=array;
        for(i=0;i<size;i++)//error:invalid application of `sizeof' to incomplete type `char[] '
        {   
        }
    } 
}
```

这个错误的原因：

sizeof不能用在extern变量， sizeof 的计算发生在代码编译 的时刻。。 extern 标注的符号 在链接的时刻解析。。。 所以 sizeof 不知道 这个符号到底占用了多少空间。**

就我这个例子的解决办法：

①：把用到for(i=0;i< size; i+ +)的函数和定义数组的文件写到一块；
②：不要用宏定义`#define size (sizeof(array)/sizeof(array[0]))` 了，直接在a.c文件里定义 `size=(sizeof(array)/sizeof(array[0]));` 然后在main.c里用

```c
extern int zimo1_size; 
extern char zimo1[];
```