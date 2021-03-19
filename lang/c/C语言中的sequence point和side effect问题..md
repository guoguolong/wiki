这个就是sequence point和side effect问题. func(f1(),g1());

上面的表达式到底先执行那个函数 顺序是不确定的. 是Unspecified.

函数调用会产生一个side effect, 而调用函数之前是一个Sequence Point. 所以可以确定func()将会在f1(), g1()之后执行. 同时两个Sequence Point之间的side effect执行顺序是unspecified的. 所以先执行f1()还是先执行g1() 是不确定的.

在macOS下用gcc（clang）编译得到的编译器警告就说的很清楚了,

```c
int func(int a,int b)
{
    return 0;
}
int main(void)
{
    int i =0;
    i = ++i + 1;
    func(i,i++);
    return 0;
}
```

运行警告：

> ```
> test.c:6:9: warning: multiple unsequenced modifications to 'i' [-Wunsequenced]
>    i = ++i + 1;
>      ~ ^
> test.c:7:14: warning: unsequenced modification and access to 'i' [-Wunsequenced]
>    func(i, i++);
>         ~   ^
> ```

关于顺序点, side effect的定义和说明在ISO/IEC 9899:1990的5.1.2.3节和 C