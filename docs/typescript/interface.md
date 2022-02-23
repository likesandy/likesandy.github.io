# 接口

## 一、接口的声明

在前面我们通过 type(类型别名) 可以用来声明一个对象类型：

接口声明是命名对象类型的另一种方式

```ts
// 在接口名称前面+I(算是一种规范)
interface IInfoType {
  name: string;
  age: number;
}
const info: InfoType = {
  name: "tao",
  age: 19,
  numebr: number,
};
```

因为 JavaScript 支持类和面向对象程序设计，所以 TypeScript 也是如此，你可以对类使用一个接口声明:

```ts
interface User {
  name: string;
  id: number;
}

class UserAccount {
  name: string;
  id: number;

  constructor(name: string, id: number) {
    this.name = name;
    this.id = id;
  }
}

const user: User = new UserAccount("Murphy", 1);
```

## 二、可选属性

接口中我们也可以定义可选属性：

```ts
interface InfoType {
  name: string;
  age: number;
  friend?: {
    name: string;
  };
}
const info: InfoType = {
  name: "tao",
  age: 19,
  friend: {
    name: "sandy",
  },
};

console.log(info.friend?.name);
```

## 三、只读属性

接口中也可以定义只读属性：

- 这样就意味着我们再初始化之后，这个值是不可以被修改的；

```ts
interface InfoType {
  readonly name: string;
  age: number;
}
const info: InfoType = {
  name: "tao",
  age: 19,
};

info.name = "sandy"; // 不能赋值给'name'，因为它是一个只读的属性。
```

## 四、索引类型

前面我们使用 interface 来定义对象类型，这个时候其中的属性名、类型、方法都是确定的，但是有时候我们会遇
到类似下面的对象：

```ts
interface IFrontEnd {
  [index: number]: string;
}

const frontEnd: IFrontEnd = {
  0: "HTML",
  1: "CSS",
  2: "JavaScript",
  3: "Vue.js",
};

interface ILanguageYear {
  [name: string]: number;
}

const languageYear: ILanguageYear = {
  C: 1972,
  Java: 1995,
  JavaScript: 1996,
  TypeScript: 2014,
};
```

## 五、函数类型

前面我们都是通过 interface 来定义对象中普通的属性和方法的，实际上它也可以用来定义函数类型：

```ts
interface ISumFn {
  (num1: number, num2: number): number;
}

const sum: ISumFn = (num1, num2) => {
  return num1 + num2;
};
```

当然，除非特别的情况，还是推荐大家使用类型别名来定义函数：

```ts
type SumFnType = (num1: number, num2: number) => number;
```

## 六、接口继承

接口和类一样是可以进行继承的，也是使用 extends 关键字：

- 并且我们会发现，接口是支持多继承的（类不支持多继承）

```ts
interface IInfo {
  name: string;
}

interface IAge {
  age: number;
}

interface IPerson extends IInfo, IAge {
  say(): void;
}

const info: IPerson = {
  name: "tao",
  age: 19,
  say() {
    console.log("Hello World");
  },
};
```

## 七、接口的实现

接口定义后，也是可以被类实现的：

- 如果被一个类实现，那么在之后需要传入接口的地方，都可以将这个类传入；
- 这就是**面向接口**开发；

```ts
interface ISay {
  say: () => void;
}

interface IEat {
  eat: () => void;
}

function foo(bar: ISay) {}

// 继承:只能实现单继承
// 接口:可以实现多个接口
class Person implements ISay, IEat {
  say() {}
  eat() {}
}
```

```ts
// 编写一些共同的API就可以使用面向接口开发
// 面向接口开发属于面向对象中的概念
interface ISay {
  say: () => void;
}

interface IEat {
  eat: () => void;
}

class Person implements ISay, IEat {
  say() {}
  eat() {}
}

function foo(bar: Person) {
  bar.say();
  bar.eat();
}

// 所有实现了接口的类对应的对象,都是可以传入的
foo(new Person());
```

## 八、interface 和 type 区别

我们会发现 interface 和 type 都可以用来定义对象类型，那么在开发中定义对象类型时，到底选择哪一个呢？

如果是定义非对象类型，通常推荐使用 type，比如 Direction、Alignment、一些 Function；

如果是定义对象类型，那么他们是有区别的：

- interface 可以重复的对某个接口来定义属性和方法,而 type 定义的是别名，别名是不能重复的；
- 如果需要对原来的类型进行一个扩展,interface 可以使用`extends`进行继承,type 只能通过`&`扩展；

```ts
interface Person {
  name: string;
}

// 定义同名的多个接口的时候,接口内部的属性会进行合并
interface Person {
  age: number;
}

// 继承
interface Man extends Person {
  height: Number;
}
```

```ts
type Person = {
  name: String;
};

// 报错 重复的标识符'Person'。
// type Person {
// age: Number;
// }

// 扩展

type Man = Person & {
  age: Number;
};
```

当然官网文档中也有提到

在大多数情况下，你可以根据**个人喜好**来选择，TypeScript 会告诉你，它是否需要其他类型的声明。如果你想要一个启发式的方法，请使用 `interface`，直到你需要使用来自 `type` 的特性。

## 九、字面量赋值

和字面量类型大致相似,但还是有不同点

```ts
interface IPerson {
  name: string;
  age: number;
  say: () => void;
}

const info: IPerson = {
  name: 'tao',
  age: 19,
  say() {}
  // 报错,IPerson没有eat
  eat(){}
};


```

这是因为 TypeScript 在字面量直接赋值的过程中，为了进行类型推导会进行严格的类型限制。

但是之后如果我们是将一个 变量标识符 赋值给其他的变量时，会进行 freshness **擦除**操作。

```ts
interface IPerson {
  name: string;
  age: number;
  say: () => void;
}

const info = {
  name: "tao",
  age: 19,
  say() {},
  eat() {},
};

const p: IPerson = info;
console.log(p);
```

## 十、交叉类型

前面我们学习了联合类型：

- 联合类型表示多个类型中一个即可

还有另外一种类型合并，就是交叉类型（Intersection Types）：

- 交叉类似表示需要满足多个类型的条件；
- 交叉类型使用 & 符号；

我们来看下面的交叉类型：

- 表达的含义是 number 和 string 要同时满足；
- 但是有同时满足是一个 number 又是一个 string 的值吗？其实是没有的，所以 MyType 其实是一个 never 类型；

```ts
type MyType = number & string;
```

所以，在开发中，我们进行交叉时，通常是对对象类型进行交叉的：

就像接口的继承,当我们有多个接口需要组合的时候,可以使用接口继承的方法的,也可以使用交叉类型的方式

```ts
interface IInfo {
  name: string;
}

interface IAge {
  age: number;
}

type IPerson = IInfo & IAge;

const info: IPerson = {
  name: "tao",
  age: 19,
};
```

## 十一、枚举类型

枚举类型是为数不多的 TypeScript 特性有的特性之一：

- 枚举其实就是将一组可能出现的值，一个个列举出来，定义在一个类型中，这个类型就是枚举类型；
- 枚举允许开发者定义一组命名常量，常量可以是数字、字符串类型...；
- 枚举是一种值类型

```ts
enum Direction {
  LEFT,
  TOP,
  RIGHT,
  BOTTOM,
}

function turnDirection(direction: Direction) {
  switch (direction) {
    case Direction.LEFT:
      console.log("向左转动");
      break;
    case Direction.TOP:
      console.log("向上转动");
      break;
    case Direction.RIGHT:
      console.log("向右转动");
      break;
    case Direction.BOTTOM:
      console.log("向下转动");
      break;
    default:
      const myDirection: never = direction;
  }
}

turnDirection(Direction.LEFT); // 向左转动
turnDirection(Direction.TOP); // 向上转动
turnDirection(Direction.RIGHT); // 向右转动
turnDirection(Direction.BOTTOM); // 向下转动
```

枚举类型默认是有值的，比如上面的枚举，默认值是这样的：

```ts
enum Direction {
  LEFT = 0,
  TOP = 1,
  RIGHT = 2,
  BOTTOM = 3,
}
```

当然，我们也可以给枚举其他值：

- 这个时候会从 100 进行递增；

```ts
enum Direction {
  LEFT = 100,
  TOP = 1,
  RIGHT = 2,
  BOTTOM = 3,
}
```

我们也可以给他们赋值其他的类型：

```ts
enum Direction {
  LEFT = "LEFT",
  TOP = "TOP",
  RIGHT = "RIGHT",
  BOTTOM = "BOTTOM",
}
```

枚举的值也可以作为类型

```ts
enum Person {
  name = 1,
  age,
}

interface Man {
  name: Person.name;
  age: number;
}

function foo(params: Man) {
  if (params.name === Person.name) {
    console.log("aaa");
    return;
  }
  console.log("bbb");
}

foo({ name: 1, age: 18 }); // aaa
foo({ name: 2, age: 18 }); // bbb
```

枚举是运行时真正存在的对象

```ts
enum random {
  x,
  y,
  z,
}

function foo(params: { x: number }) {
  console.log(params.x);
}

foo(random); // 0
```

可以从 name->value,也可以从 value->name(反向映射)

```ts
enum Person {
  man,
  woman,
}

const man = Person.man;
console.log(man); // 0
const woman = Person[1];
console.log(woman); // woman
```

请记住，字符串枚举成员根本不会得到反向映射

```ts
enum Person {
  man = "2",
  woman = "1",
}

const man = Person.man;
console.log(man); // '2'
const woman = Person["2"];
console.log(woman); // undefined
```

const 让枚举更加成熟

官方文档明确写出“大多数情况下，枚举是十分有效的方案。 然而在某些情况下需求很严格。 为了避免在额外生成的代码上的开销和额外的非直接的对枚举成员的访问，我们可以使用 const 枚举”

假如仅仅通过通过 const enum 定义了枚举类型而没有其它地方调用，这段代码将在**编译时**被直接抹掉。

```ts
const enum Person {
  man,
  woman,
}

console.log(Person.man, Person.woman); // 0 1

// 编译后
("use strict");
console.log(0 /* man */, 1 /* woman */); // 0 1
```

Const 枚举只能使用常量枚举表达式

```ts
let height = 1.83;
let age = 18;

const enum Person {
  man = height,
  woman = age,
}
// 常量枚举成员初始值设定项只能包含文字值和其他计算的枚举值
```

在现代 TypeScript 中，你可能不需要一个枚举，因为一个对象的常量就足够了

```ts
enum Person {
  man,
  woman,
}
// 等价于
const person = {
  man: 0,
  woman: 1,
} as const;
```

与 TypeScript 的枚举相比，支持这种格式的最大理由是，它使你的代码库与 JavaScript 的状态保持一致，如果枚举被添加到 JavaScript 中，那么你可以转移到额外的语法。
