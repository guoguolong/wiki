## 1 继承性

```c
#include <stdio.h>

typedef struct _Animal {
    char *family; // 科
    char *name;
} Animal;

typedef struct _Tiger {
    Animal parent;
    int age;
} Tiger;

int main() {
    Tiger tiger = { "猫科", "跳跳虎", 12 };

    printf("I'm %s，%d years old，belong with %s.\n", tiger.parent.name, tiger.age, tiger.parent.family);
    return 0;
}
```

## 2 封装性

```c
#include <stdio.h>

typedef void (*ProductList)(int);
typedef void (*ProductDetail)(char *);

void product_list(int length) {
    printf("Total products: %d\n", length);
    return;
}

void product_detail(char* code) {
    printf("Product code : %s\n", code);
    return;
}

typedef struct TypeProductService {
    char *name;
    ProductList getList;
    ProductDetail getDetail;
} ProductService;

ProductService makePS() {
    ProductService srv = {"MP3", product_list, product_detail};
    return srv;
}

int main() {
    ProductService srv = makePS();
    srv.getList(8987);
    srv.getDetail("MP3");
    return 0;
}
```

## 3. 多态性

在C++中，多态通常都是使用虚函数来实现的，但是C语言中并没有虚函数，如何实现重载呢？

答案也显而易见，也是函数指针的扩展：

```c
#include <stdio.h>
#include <stdlib.h>

void base_dance(void *this) {
    printf("Animal Dance\n");
}

void base_jump(void *this) {
    printf("Animal Jump\n");
}

// 虚函数表结构
struct BaseVtbl {
    void(*dance)(void *);
    void(*jump)(void *);
};

// 基类
typedef struct _Animal {
    struct BaseVtbl *vptr; /*virtual table*/
} Animal;


/* global vtable for base */
struct BaseVtbl animal = {
    base_dance,
    base_jump
};

// 基类的构造函数
Animal * animal_factory() {
    Animal *aobj = (Animal *)malloc(sizeof(Animal));
    aobj->vptr = &animal;
    return aobj;
}

// 派生类
struct Tiger {
    Animal super;
    int high; /* derived members */
};

char* tiger_dance(void * this) {
    printf("Tiger Dance\n"); /* implementation of Tiger's dance function */
    return "Tango";
}

void tiger_jump(void * this) {
    struct Tiger* tobj = (struct Tiger *)this;
    printf("Tiger jump: %d\n", tobj->high); /* implementation of Tiger's jump function */
}

/* global vtable for Tiger */
struct BaseVtbl tiger_table = {
    (void *) tiger_dance, // TODO: 难点
    (void(*)(void *)) &tiger_jump,
};

// 派生类的构造函数
struct Tiger * tiger_factory(int h) {
    struct Tiger * tiger = (struct Tiger *)malloc(sizeof(struct Tiger));
    tiger->super.vptr = &tiger_table;
    tiger->high = h;
    return tiger;
}

int main(void) {
    Animal * animalObj = animal_factory();
    // 这里调用的是基类的成员函数
    animalObj->vptr->dance((void *)animalObj);
    animalObj->vptr->jump((void *)animalObj);

    struct Tiger * tigerObj = tiger_factory(99);
    animalObj = (Animal *)tigerObj; // 基类指针指向派生类, // TODO: 难点

    // 这里调用的其实是派生类的成员函数
    animalObj->vptr->dance((void *)animalObj);
    animalObj->vptr->jump((void *)animalObj);
    return 0;
}
```