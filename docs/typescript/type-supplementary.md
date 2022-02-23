# 类型补充

## 一、对象类型

如果我们希望限定一个函数接受的参数是一个对象，这个时候要如何限定呢？

- 我们可以使用对象类型；

```ts
function coordinate(coordinate: { x: number; y: number; z: number }) {
  console.log(coordinate.x);
  console.log(coordinate.y);
  console.log(coordinate.z);
}

coordinate({ x: 12, y: 123, z: 213 });
```

## 二、可选类型

当我们设立形参的时候,在 ts 中设立了几个就必须传几个,但是我们希望有些属性可以传也可以不传

```ts
function coordinate(coordinate: {
  x: number;
  y: number;
  z?: number;
}) {
  console.log(coordinate.x);
  console.log(coordinate.y);
  console.log(coordinate.z);
}

coordinate({ x: 12, y: 123 });
```

## 四、可选类型和联合类型的关系

其实上，可选类型可以看做是 类型 和 undefined 的联合类型：

```ts
function foo(name?: string) {
  console.log(name);
}

foo();
foo(undefined);
```

## 六、类型断言

有时候 TypeScript 无法获取具体的类型信息，这个我们需要使用类型断言（Type Assertions）。

比如在开发中我们需要获取元素,通过 document.querySelector 获取元素的时候,ts 只知道函数会返回一个 Element,但是不知道具体的类型

我们明确需要获取 img 元素,img 可以设置 src 属性,但是默认的话我们是不能设置 src 的,因为 Element 类型上不存在 src 属性

![11.png](https://img14.360buyimg.com/ddimg/jfs/t1/197606/40/2555/14448/61122a18E05977046/16ce5e31bb92029f.png)

这个时候我们就可以使用类型断言让它变成 imgElement

```ts
const imgEl = document.querySelector(".tao") as HTMLImageElement;
imgEl.src = "src地址";
```

您还可以使用尖括号语法(除非代码位于.tsx 文件中) ，这是等效的:

```ts
const imgEl = <HTMLImageElement>document.querySelector(".tao");
imgEl.src = "src地址";
```

!>提醒: 因为类型断言是在编译时删除的，所以不存在与类型断言关联的运行时检查。如果类型断言错误，则不会生成异常或 null。

TypeScript 只允许类型断言转换为 更具体 或者 不太具体 的类型版本，此规则可防止不可能的强制转换：

```ts
const age = 18 as String;
// 类型 "number" 到类型 "String" 的转换可能是错误的，因为两种类型不能充分重叠。如果这是有意的，请先将表达式转换为 "unknown"
```

有时这个规则可能过于保守，不允许更复杂的有效强制。如果发生这种情况，你可以使用两个断言，首先是对任何(或者未知，我们将在后面介绍) ，然后是所需的类型:

```ts
const name = "tao";
const age: number = name as any as number;
```

还有一个小案例可以看看:

```ts
class Person {}

class Student extends Person {
  say() {}
}

function sayHello(p: Person) {
  // 类型“Person”上不存在属性“say”
  p.say();
}

const stu = new Student();
sayHello(stu);
```

sayHello 接收一个 Person 类型的参数

stu 是 Student 的实例,继承于 Person,当然可以传进去

本质上你还是 Student 类型,但是因为继承的原因,它在编译的时候会帮你转为 Person 类型

Person 里没有 say 方法,当然你调用的时候可以会报错啊

但是这不合情理,我是 Student 的实例,我不能使用我自己的方法,这就不行

这个时候我们就可以通过类型断言的方法把它变为 Student

```ts
class Person {}

class Student extends Person {
  say() {}
}

function sayHello(p: Person) {
  (p as Student).say();
}

const stu = new Student();
sayHello(stu);
```

## 七、非空类型断言

TypeScript 还有一种特殊的语法，用于在不进行任何显式检查的情况下从类型中删除 null 和未定义的内容。写作！在任何表达式之后都是一个类型断言，该值不是 null 或未定义的:

```ts
function liveDangerously(x?: number | null) {
  console.log(x.toFixed());
}
```

非空断言使用的是 `!` ，表示可以确定某个标识符是有值的，跳过 ts 在编译阶段对它的检测；

就像其他类型断言一样，这不会改变代码的运行时行为，因此只使用它是很重要的！当您知道该值不能为空或未定义时。

## 八、可选链

可选链事实上并不是 TypeScript 独有的特性，它是 ES11（ES2020）中增加的特性：

- 可选链使用可选链操作符 `?.`；
- 它的作用是当对象的属性不存在时，会短路，直接返回 undefined，如果存在，那么才会继续执行；

```ts
type Person = {
  name: string;
  friend?: {
    name: string;
  };
};

const info: Person = {
  name: "tao",
};

console.log(info.name);
console.log(info.friend.name);
```

虽然在编译的时候代码是没问题的,但是在代码执行的时候是会报错的

因为我们 info.friend 是 undefined,从 undefined 从取 name 属性,肯定是会报错的

因为我们之前说过,**错误发现的越早越好**,能在编译的时候发现错误当然是大于在执行的时候发现错误好

所以这里我们可以使用可选链

```ts
type Person = {
  name: string;
  friend?: {
    name: string;
  };
};

const info: Person = {
  name: "tao",
};

console.log(info.name);
console.log(info.friend?.name);
```

诶你会发现非空类型断言好像跟可选链是差不多的耶,其实两者是有区别,面试也可能会作为冷门题来考你

!作为编译阶段的非空判断,?.不仅仅会在编译时有效,而且会在运行时也有有效

## 九、??和!!

### 9.1 ??

它是 ES11 增加的新特性

空值合并操作符（??）是一个逻辑操作符，当操作符的左侧是 null 或者 undefined 时，返回其右侧操作数，
否则返回左侧操作数；

```ts
const name: string | null | undefined = null;
const message = name ?? "sandy";
console.log(message); // sandy
```

### 9.2 !!

将一个其他类型转换成 boolean 类型；类似于 Boolean(变量)的方式；

```ts
const name = "tao";
let flag = !!name;
console.log(flag); // true
```

## 十、文字类型(字面量类型)

除了一般类型字符串和数字之外，我们还可以在类型位置中引用特定的字符串和数字。

```ts
// 其实你会发现你使用let和const定义变量是不同的
const message = "tao";
```

你肯定会以为 message 是一个 string 类型,其实它是一个'tao'类型(字面量类型)

![12.png](https://img14.360buyimg.com/ddimg/jfs/t1/176869/20/18569/20766/61122a1bE3efaa1db/5e4e88e98acc5021.png)

文字类型本身并不是很有价值

但是，通过将文字组合成联合，可以表达一个更有用的概念——例如，只接受一组已知值的函数:

```ts
function foo(params: "a" | "b" | "c") {
  console.log("foo");
}

// 只能传入a或者b或者c
foo("a");
foo("d"); // 类型“"d"”的参数不能赋给类型“"a" | "b" | "c"”的参数。
```

当然，你可以把这些类型和非文字类型结合起来:

```ts
interface Person {
  name: string;
  age: number;
}

function foo(params: Person | "dog") {
  console.log("hhh");
}
foo({ name: "tao", age: 18 });
foo("dog");
foo(123); // 类型“123”的参数不能赋给类型“Person | "dog"”的参数。
```

还有一种文字类型: 布尔文字。只有两种布尔文字类型，正如您可能猜到的，它们是 true 和 false 类型。类型 boolean 本身实际上只是 true | false 的别名。

```ts
function foo(params: true | false) {}
// 等价于
function foo(params: boolean) {}
```

## 十一、字面量推理

我们先来看一个案例:

```ts
type MethodType = "Get" | "Post";
function request(url: string, method: MethodType) {}

const options = {
  url: "http://www.codertao.work/",
  method: "Get",
};

//  这样肯定是会报错的,options.method是一个字符串类型
request(options.url, options.method);
```

我们当然有其它方法让它成功调用

```ts
// 给options设置类型(推荐)
type OptionsType = {
  url: string;
  method: MethodType;
};
const options: OptionsType = {
  url: "http://www.codertao.work/",
  method: "Get",
};

// 或者我们也可以进行类型断言
request(options.url, options.method as MethodType);

// 也可以使用 const 将整个对象转换为文本类型
const options = {
  url: "http://www.codertao.work/",
  method: "Get",
} as const;
```

而且我们会发现通过字面量推理 options 中的属性是只读的了(后面会讲对象的相关知识)

![13.png](https://img10.360buyimg.com/ddimg/jfs/t1/191714/14/17735/28567/61122a1bEbf95a7e9/228621285c462ebe.png)

## 十二、类型缩小

什么是类型缩小呢？

- 类型缩小的英文是 Type Narrowing；
- 我们可以通过类似于 typeof padding === "number" 的判断语句，来改变 TypeScript 的执行路径；
- 在给定的执行路径中，我们可以缩小比声明时更小的类型，这个过程称之为 **缩小**;
- 而我们编写的 typeof padding === "number 可以称之为 **类型保护**（type guards）；

常见的类型保护有如下几种：

- typeof
- 平等缩小（比如===、!==）
- instanceof
- in
- 等等

## 十三、typeof

在 TypeScript 中，检查返回的值 typeof 是一种类型保护：因为 TypeScript 对如何 typeof 操作不同的值进行编码。

```ts
type MessageType = number | string;
function foo(message: MessageType) {
  if (typeof message === "string") {
    console.log(message.toString());
  } else {
    console.log(message);
  }
}
```

## 十四、平等缩小

我们可以使用 Switch 或者相等的一些运算符来表达相等性（比如===, !==, ==, and != ）：

```ts
type Direction = "left" | "right" | "top" | "bottom";
function printDirection(direction: Direction) {
  // 1.if判断
  // if (direction === 'left') {
  //   console.log(direction)
  // } else if ()
  // 2.switch判断
  // switch (direction) {
  //   case 'left':
  //     console.log(direction)
  //     break;
  //   case ...
  // }
}
```

## 十五、instanceof

JavaScript 有一个运算符来检查一个值是否是另一个值的“实例”：

```ts
function printTime(time: string | Date) {
  if (time instanceof Date) {
    console.log(time.toUTCString());
  } else {
    console.log(time);
  }
}

class Student {
  studying() {}
}

class Teacher {
  teaching() {}
}

function work(p: Student | Teacher) {
  if (p instanceof Student) {
    p.studying();
  } else {
    p.teaching();
  }
}

const stu = new Student();
work(stu);
```

## 十六、in

Javascript 有一个运算符，用于确定对象是否具有带名称的属性：in 运算符

- 如果指定的属性在指定的对象或其原型链中，则 in 运算符返回 true；

```ts
type Fish = {
  swimming: () => void;
};

type Dog = {
  running: () => void;
};

function walk(animal: Fish | Dog) {
  if ("swimming" in animal) {
    animal.swimming();
  } else {
    animal.running();
  }
}

const fish: Fish = {
  swimming() {
    console.log("swimming");
  },
};

walk(fish);
```
