## 简单例子

```c
/*
 * 
 * 使用gcc扩展支持lambda表达式（不能在macOS下运行，不支持clang）
 *  - Statements and Declarations in Expressions：https://gcc.gnu.org/onlinedocs/gcc-8.1.0/gcc/Statement-Exprs.html
 *  - Nested Functions：https://gcc.gnu.org/onlinedocs/gcc-8.1.0/gcc/Nested-Functions.html
 */

#include <stdio.h>

#define LAMBDA(return_type, function_body) \
({return_type lambda_func function_body lambda_func;})

int main( int argc, const char **argv) {
    printf( "Sum = %d\n", LAMBDA(int, (int x, int y){ return x + y; })(3, 4) );
    return 0;
}
```

编译和运行试试，运行结果为：Sum = 7

上例中的 LAMBDA 宏定义编译过后如下：

```c
({int lambda_func (int x, int y){ return x + y; } lambda_func;})(3, 4)
```

这个例子是以来GNC C的扩展功能：Nested Functions 和 Statements and Declarations 完成的，在macOS下不能运行（因为clang不支持）。

## 引入访问外部变量的例子