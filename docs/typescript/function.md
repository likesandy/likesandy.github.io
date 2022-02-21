# 函数

## 一、函数的参数和返回值类型

函数是 JavaScript 非常重要的组成部分，TypeScript 允许我们指定函数的参数和返回值的类型。

声明函数时，可以在每个参数后添加类型注解，以声明函数接受的参数类型：

我们也可以添加返回值的类型注解，这个注解出现在函数列表的后面：

```ts
function sum(num1: number, num2: number): number {
  return num1 + num2;
}
```

和变量的类型注解一样，我们通常情况下不需要返回类型注解，因为 TypeScript 会根据 return 返回值推断函数的
返回类型：

某些第三方库处于方便理解，会明确指定返回类型，但是这个看**个人喜好**；

## 二、上下文函数的参数类型

当一个函数出现在可以决定如何调用它的地方时，该函数的参数将自动给定类型。

什么意思喃?我们来看下面这个例子

```ts
const names = ["tao", "sandy", "zm"];
names.forEach((item) => {
  console.log(item.abc()); // item.abc is not a function
});
```

我们并没有指定 item 的类型，但是 item 是一个 string 类型：

- 这是因为 TypeScript 会根据 forEach 函数的类型以及数组的类型推断出 item 的类型；'
- 这个过程称之为**上下文类型**（contextual typing），因为函数执行的上下文可以帮助确定参数和返回值的类型；

## 三、对象类型

除了基本类型之外，最常见的类型是对象类型。这指的是任何带有属性的 JavaScript 值，这几乎是所有的属性！要定义对象类型，只需列出其属性及其类型。

```ts
function foo(pt: { x: number; y: String }) {
  console.log(pt.x); // 1
  console.log(pt.y); // '8'
}
foo({ x: 1, y: "8" });
```

在这里，我们用具有两个属性 x 和 y 参数进行了注解，x 为 number 类型,y 为 string 类型

对象之间您可以使用`，`或`;` 来分隔属性，最后一个分隔符是可选的

每个属性的类型部分也是可选的。如果没有指定类型，则假定它是任何类型。

演示一下

```ts
function foo(pt: { x: number; y }) {
  console.log(pt.x); // 1
  console.log(pt.y); // '8'
}
foo({ x: 1, y: "8" });
```

## 三、函数类型

在 JavaScript 开发中，函数是重要的组成部分，并且函数可以作为一等公民（可以作为参数，也可以作为返回值进
行传递）。

那么在使用函数的过程中，函数是否也可以有自己的类型呢？

- 我们可以编写函数类型的表达式（Function Type Expressions），来表示函数类型；

```ts
function sum(num1: number, num2: number): number {
  return num1 + num2;
}

function mul(num1: number, num2: number): number {
  return num1 * num2;
}

type CalcFnType = (num1: number, num2: number) => void;
function calc(fn: CalcFnType) {
  console.log(fn(20, 30));
}

calc(sum); // 50
calc(mul); // 600
```

在上面的语法中 (num1: number, num2: number) => void，代表的就是一个函数类型：

- 接收两个参数的函数：num1 和 num2，并且都是 number 类型；
- 并且这个函数是没有返回值的，所以是 void；

在某些语言中，可能参数名称 num1 和 num2 是可以省略，但是 TypeScript 是不可以的：

## 四、参数的可选类型

我们可以指定某个参数是可选的：

```ts
function foo(name: string, age?: number) {
  console.log(name, age);
}
```

这个时候这个参数 age 依然是有类型的，它是什么类型呢？ number | undefined

另外可选类型需要在必传参数的后面：

![14.png](https://img11.360buyimg.com/ddimg/jfs/t1/202688/20/678/29426/61122a1bEd334751b/457930a11c190a90.png)

## 三、联合类型

联合类型是由两个或多个其他类型组成的类型，表示可能是这些类型中任何一个类型的值

```ts
function foo(message: string | number) {
  console.log(message.indexOf()); // 类型“number”上不存在属性“indexOf”
}
```

传入给一个联合类型的值是非常简单的：只要保证传入的参数是联合类型中的某一个类型的值即可

- 但是我们拿到这个值之后，我们应该如何使用它呢？因为它可能是任何一种类型。
- 比如我们拿到的值可能是 number，我们就不能对其调用 string 上的一些方法；

那么我们怎么处理这样的问题呢？

- 我们需要使用**缩小**（narrow）联合（后面还会细讲缩小相关的功能）；
- TypeScript 可以根据我们缩小的代码结构，推断出更加具体的类型；

```ts
function foo(message: string | number) {
  if (typeof message === "string") {
    console.log(message.indexOf("abc")); // 0
  } else {
    console.log(message); // 123
  }
}

foo("abc");
foo(123);
```

如果联合中的每个成员都有一个共同属性，则可以使用该属性而不进行收缩

```ts
// 数组和字符串都有slice方法,所以不会进行缩小
function getFirstThree(x: number[] | string) {
  return x.slice(0, 3);
}
```

官方说法: 如果我们有一个房间的高个子戴着帽子，而另一个房间的西班牙人戴着帽子，在合并了这些房间之后，我们唯一知道的就是每个人都必须戴着帽子

联合类型的缺点:

- 需要进行很多的逻辑判断(类型缩小)
- 返回值的类型是不容易确定的

## 五、类型别名

我们通过直接在类型注释中编写**对象类型**和**联合类型**来使用它们。这很方便，但是通常希望多次使用同一类型并使用单个名称引用它。

比如我们可以给对象类型起一个别名：

```ts
type CoordinateType = {
  x: number;
  y: number;
  z?: number;
};
function coordinate(coordinate: CoordinateType) {
  console.log(coordinate.x);
  console.log(coordinate.y);
  console.log(coordinate.z);
}

coordinate({ x: 12, y: 123 });
```

实际上，您可以使用类型别名为任何类型提供名称，而不仅仅是对象类型。

```ts
type ID = number | string;
type name = Boolean;
```

## 五、默认参数

从 ES6 开始，JavaScript 是支持默认参数的，TypeScript 也是支持默认参数的：

```ts
function foo(name: string, age: number = 19) {
  console.log(name, age);
}

foo("tao");
```

这个时候 age 的类型其实是 undefined 和 number 类型的联合。

:::tip 提示
个人习惯:开发中我会选择先写必传参数->默认值参数->可选参数
:::

## 六、剩余参数

从 ES6 开始，JavaScript 也支持剩余参数，剩余参数语法允许我们将一个不定数量的参数放到一个数组中。

```ts
function sum(...nums: number[]) {
  nums.forEach((item) => {
    console.log(item);
  });
}

sum(20, 30, 40);
```

## 七、可推导的 this

this 是 JavaScript 中一个比较难以理解和把握的知识点：

那么，TypeScript 是如何处理 this 呢？我们先来看一个例子：

```ts
const info = {
  name: "tao",
  say() {
    console.log(this.name);
  },
};
info.say();
```

上面的代码是可以正常运行的，也就是 TypeScript 在编译时，认为我们的 this 是可以正确去使用的：

- TypeScript 认为函数 say 有一个对应的 this 的外部对象 info，所以在使用时，就会把 this 当做该对象。

## 八、不确定的 this

但是对于某些情况来说，我们并不知道 this 到底是什么？

```ts
function say() {
  console.log(this.name);
}

const info = {
  name: "tao",
  say,
};
info.say();
```

这段代码运行会报错的：

- 这里我们再次强调一下，TypeScript 进行类型检测的目的是让我们的代码更加的安全；
- 所以这里对于 say 的调用来说，我们虽然将其放到了 info 中，通过 info 去调用，this 依然是指向 info 对象的；
- 但是对于 TypeScript 编译器来说，这个代码是非常不安全的，因为我们也有可能直接调用函数，或者通过别的对象来
  调用函数；

## 九、指定 this

这个时候，通常 TypeScript 会要求我们明确的指定 this 的类型：

```ts
type SayThisType = { name: string };
function say(this: SayThisType, age: number) {
  console.log(this.name, age);
}

const info = {
  name: "tao",
  say,
};

// 直接调用是不行的
// say({ name: 'tao' })

// 隐式调用
info.say(19);

// 显式调用
say.call({ name: "sandy" }, 21);
say.apply({ name: "zm" }, [20]);
```

## 十、函数重载

简单来说：函数重载就是函数的名称相同，但是参数不同的几个函数

在 TypeScript 中，如果我们编写了一个 add 函数，希望可以对字符串和数字类型进行相加，应该如何编写呢？

我们可能会这样来编写，但是其实是错误的：

```ts
function add(a1: number | string, a2: number | string) {
  return a1 + a2;
}
```

![15.png](https://img14.360buyimg.com/ddimg/jfs/t1/177213/6/18395/27289/61122a1bEf0f376ae/99d73fd6b4d11dcd.png)

那么这个代码应该如何去编写呢？

- 在 TypeScript 中，我们可以去编写不同的重载签名（overload signatures）来表示函数可以以不同的方式进行
  调用；
- 一般是编写两个或者以上的重载签名，再去编写一个通用的函数以及实现；

比如我们对 sum 函数进行重构：

在我们调用 sum 的时候，它会根据我们传入的参数类型来决定执行函数体时，到底执行哪一个函数的**重载签名**；

```ts
// 声明函数
function add(a1: number, a2: number): number;

function add(a1: string, a2: string): string;

// 实现函数
function add(a1: any, a2: any): any {
  if (typeof a1 === "string" && typeof a2 === "string") {
    return a1.length + a2.length;
  }
  return a1 + a2;
}

console.log(add(20, 30)); // 50
console.log(add("abc", "cba")); // 6
```

但是注意，有实现提的函数，是不能直接被调用的：

![16.png](https://img13.360buyimg.com/ddimg/jfs/t1/196232/25/17613/90422/61122a1bE7e38dd38/43abc1d9a35f2b28.png)

调用重载的函数,是通过从上往下执行查看函数签名是否符合要求,符合的话则执行下面的实现函数,不符合则报错

## 十一、练习

我们现在有一个需求：定义一个函数，可以传入字符串或者数组，获取它们的长度。

这里有两种实现方案：

- 使用联合类型来实现；
- 实现函数重载来实现；

```ts
function getLength(a: string | any[]): number {
  return a.length;
}

console.log(getLength("abc")); // 3
console.log(getLength([123, 321, 1234567])); // 3
```

```ts
function getLength(a: string): number;
function getLength(a: any[]): number;

function getLength(a: any): any {
  return a.length;
}

console.log(getLength("abc")); // 3
console.log(getLength([123, 321, 1234567])); // 3
```

在开发中我们选择使用哪一种呢？

- 在可能的情况下，尽量选择使用联合类型来实现(简单用联合,复杂用重载)
