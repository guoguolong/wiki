最近学习c语言，发现==在比较字符串的时候有点意思。总结如下：

1. 如果比的是字符串指针，有可能是相等的
2. 如果比较的是字符串本身（通过*p比)，只会比较第一个字符
3. 比较字符串数组的话，一定是不等的，*arr的话比较的是第一个字符

所以还是用strcmp()吧

```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include <malloc.h>

int main(int argc, char const *argv[])
{
    char *str1 = "isGood";
    char *str2 = "isGood";
    char *str3 = "isGood3";
    char *str4 = "notisGood3";


    
    printf("１、%d\n", str1==str2);
    printf("２、%d\n", (*str1)==(*str2));
    printf("３、%d\n", str1==str3);
    printf("４、%d\n", (*str1)==(*str3));
    printf("５、%d\n", (*str1)==(*str4));
    

    char arr1[]="isGood";
    char arr2[]="isGood";
    char arr3[]="isGood3";
    char arr4[]="notisGood3";

    printf("１、%d\n", arr1==arr2);
    printf("２、%d\n", (*arr1)==(*arr2));
    printf("３、%d\n", arr1==arr3);
    printf("４、%d\n", (*arr1)==(*arr3));
    printf("５、%d\n", (*arr1)==(*arr4));

    return 0;
}
```

输出：

```
１、1
２、1
３、0
４、1
５、0
１、0
２、1
３、0
４、1
５、0
```