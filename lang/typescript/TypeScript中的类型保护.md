## 类型断言

* lib.ts - 一段 ts 定义和 `isTeacher`方法实现

 ```typescript
 interface Teacher {
   teach(): void;
 }
 interface Student {
   learn(): void;
 }
 
 function isTeacher(p: Student|Teacher) {
   return p.teach === undefined;
 }
 ```

上述代码转译失败，因为 `p.teach` 中的`p`可能是`Student`类型，可能没有`teach`方法，因此，要加`类型断言`

* 类型断言语法I: `<类型>变量名`

```diff
...
function isTeacher(p: Student|Teacher) {
-  return p.teach === undefined;
+  return (<Teacher>p).teach === undefined;
}
```

类型断言语法 II: `变量名 as 类型`

```typescript
...
function isTeacher(p: Student|Teacher) {
-  return p.teach === undefined;
+  return (p as Teacher).teach === undefined;
}
```



## 类型保护

有了前面的函数定义，下面开始测试

```typescript
function getPerson(): Teacher | Student {
  return {} as Teacher;
}
// 创建一个 person 实例，但并不确定是Teacher还是Student
const person = getPerson();

// 所以先调用 isTeacher 判断是 Teacher，再调用 teach()
if (isTeacher(person)) {
  person.teach();  
}

```

上述代码转译仍然失败，报错内容是：

```
Property 'teach' does not exist on type 'Teacher | Student'.
...
```

此时需要给`isTeacher`函数定义增加类型保护，修改lib.ts

```diff
- function isTeacher(p: Student | Teacher) {
+ function isTeacher(p: Student | Teacher): p is Teacher {
  return (<Teacher>p).teach === undefined;
}
```

完整代码如下：

```typescript
interface Teacher {
  teach(): void;
}
interface Student {
  learn(): void;
}

function isTeacher(p: Student|Teacher): p is Teacher {
  return (<Teacher>p) === undefined;
}
/*** 下面是调用测试 ***/
function getPerson(): Teacher | Student {
  return {} as Teacher;
}
const person = getPerson();

if (isTeacher(person)) {
  person.teach();  
}
```



## 更多关于类型保护

`typeof` 和 `instanceof` 也可以进行类型保护

* `typeof`只能匹配基本类型时，才会启用类型保护。

```typescript
function isNumber(padding: number | string): padding is number {
  return typeof padding === 'number';
}

function isString(padding: number | string): padding is string {
  return typeof padding === 'string';
}

function padLeft(value: string, padding: number | string) {
  if (isNumber(padding)) {
    return Array(padding + 1).join(' ') + value;
  }
  if (isString(padding)) {
    return padding + value;
  }
  throw new Error(`Expected string or number , got '${padding}'.`);
}

console.log(padLeft('Hello World', 4));
```

* `Instanceof` 通过构造函数来区分类型的一种方式

```typescript
interface Person {
  talk(): void;
}

class Teacher implements Person {
  constructor(name: String) {}
  talk() {}
  teach() {
    console.log('Teach');
  }
}

class Student implements Person {
  constructor(name: String, age: number, classRoom: string) {}
  talk() {}
  learn() {
    console.log('Learn');
  }
}
/*** 下面是调用测试 ***/
function getPerson() {
  return Math.random() < 0.5
    ? new Teacher('刘老师')
    : new Student('小明', 8, '三班');
}

const person = getPerson();
if (person instanceof Teacher) {  // 没有该判断条件, person.teach() 报错
  person.teach();
}

if (person instanceof Student) { // 没有该判断条件, person.learn() 报错
  person.learn();
}
```

