`int *(*p(int))[3]` 今天有人问这个是啥？我一看直接就懵逼了……

下面做一些简单的分析。

```c
int p; //这是整数型变量p
int *p; //这是整数型指针p
int *p[3]; //这是长度为3的整数型指针数组p，元素为整数型指针
int (*p)[3]; //这是一个数组指针，指向一个长度为3的整数型数组
int p(int); //这是函数声明，形参：整数型 ，返回值：整数型

等同于 int p(int x);

int *p(int); //这是函数声明，形参：整数型 ，返回值：整数型指针

等同于 int *p(int x);

int (*p)(int); //这是函数指针，指向有一个整数型形参和整数型返回值的函数

int (*p[3])(int);//这是函数指针数组，每个元素指向有一个整数型形参和整数型返回值的函数

int *(*p(int)); //这是函数声明，形参：整数型 ，返回值：指向整数型指针的指针

等同于 int **p(int) ， int **p(int x) ， int *(*p(int x))

int *(*p(int))[3]; //这是一个函数声明，形参：整数型，返回值：一个数组指针数组，此数组内的指针，指向一个长度为3的整数型指针数组。
```

我知道这个很绕，简单说返回值就是这样： `int *i[x][3];  //x是任意数`

以上结论均在VisualStudio2010中进行测试，无误。如果我有所疏漏，或者是有什么表达不清，请留言哦，么么哒(づ￣ 3￣)づ。

