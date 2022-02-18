# Array

## 一、数组的遍历

### 1.1 for 循环

```js
const arr = [1, 2, 3, 4, 5];

for (let i = 0; i < arr.length; i++) {
  arr[i] = arr[i] + 1;
}
console.log(arr); // [ 2, 3, 4, 5, 6 ]
```

### 1.2 forEach

[Array.prototype.forEach](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 方法表示对数组的每个元素都运行一个函数。

```js
const arr = [1, 2, 3, 4, 5];

arr.forEach((value, index, array) => {
  arr[index] = value + 1;
});

console.log(arr); // [ 2, 3, 4, 5, 6 ]
```

手写 forEach

```js
const arr = [1, 2, 3, 4, 5];

Array.prototype.t_forEach = function (callback) {
  for (let i = 0; i < this.length; i++) {
    callback(this[i], i, this);
  }
};

arr.t_forEach((value, index, array) => {
  arr[index] = value + 1;
});

console.log(arr); // [ 2, 3, 4, 5, 6 ]
```

这就解决了我在看 mdn 文档时候的疑惑,为什么除了抛出异常以外，没有办法中止或跳出 forEach() 循环

因为我们发现每一个 callback 都是独立的,所以我们无法跳出整个循环

### 1.3 map

[Array.prototype.map](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 方法表示对数组的每个元素都调用函数，并返回结果放在一个新数组中。

```js
const arr = [1, 2, 3, 4, 5];

const newArr = arr.map((value, index, array) => {
  return value + 1;
});

console.log(newArr); // [ 2, 3, 4, 5, 6 ]
```

手写 map

```js
const arr = [1, 2, 3, 4, 5];

/**
 * 解析:
 * - 我们需要返回一个新的数组
 * - 先在循环外定义一个数组,将来将它返回
 * - map第一个回调函数(自定义callback)返回什么就往前面定义的数组里面push什么
 * @param {Function} callback
 * @returns {Array}
 */
Array.prototype.t_map = function (callback) {
  const result = [];
  for (let i = 0; i < this.length; i++) {
    result.push(callback(this[i], i, this));
  }
  return result;
};

const newArr = arr.t_map((value, index, array) => {
  return value + 1;
});

console.log(newArr); // [ 2, 3, 4, 5, 6 ]
```

### 1.4 for...in

for...in 表示遍历一个对象的 key

for...in 是为遍历对象属性而构建的，**不建议**与数组一起使用

如果想用 for...in 对数组进行遍历,遍历值并不是 item,而是 index

```js
const arr = [1, 2, 3, 4, 5];
const newArr = [];

for (const key in arr) {
  newArr.push(arr[key] + 1);
}

console.log(newArr); // [ 2, 3, 4, 5, 6 ]
```

上面的例子看上去没有什么问题,但是如果有一些特别的操作,就会变得不一样

```js
const arr = [1, 2, 3, 4, 5];
arr.name = "tao";

for (const key in arr) {
  console.log(key); // 0,1,2,3,4,name
}
```

再比如， 假如对数组原型做一些操作， 那么也会被遍历出来。

```js
const arr = [1, 2, 3, 4, 5];

Array.prototype.t_hello = function () {
  console.log("hello world");
};

for (const key in arr) {
  console.log(key); // 0,1,2,3,4,t_hello
}
```

### 1.5 for...of

[for...of](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of)表示对一个**可迭代对象**进行遍历

?> 可迭代对象会在后续的章节进行讲解,这个阶段你可以理解为数组和对象

```js
const arr = [1, 2, 3, 4, 5];
const newArr = [];

for (const item of arr) {
  newArr.push(item + 1);
}

console.log(newArr); // 2,3,4,5,6
```

### 1.6 find

[Array.prototype.find](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/find)方法表示对数组中的每一个元素调用一个函数,内部函数如果返回 true，则 find 返回 item 并停止迭代

```js
const arr = [1, 2, 3, 4, 5];

const newArr = arr.find((value, index, array) => {
  // 如果找到了符合value>3的值就会立马停止搜索,并返回item
  // return true返回对应的item
  // return false返回undefined
  return value > 3;
});

console.log(newArr); // 4
```

手写 find

```js
const arr = [1, 2, 3, 4, 5];

/**
 * 解析:
 * - 如果find第一个回调函数(自定义的callback函数)返回true,那么返回对应的item
 * - 反之返回false,那么返回undefined
 * @param {Function} callback
 * @returns {any}
 */
Array.prototype.t_find = function (callback) {
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) return this[i];
  }
  return undefined;
};

const newArr = arr.t_find((value, index, array) => {
  return value > 3;
});

console.log(newArr); // 4
```

### 1.7 findIndex

[Array.prototype.findIndex](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)同上,只是返回元素的 index

```js
const arr = [1, 2, 3, 4, 5];

const newArr = arr.findIndex((value, index, array) => {
  // return true返回对应的index
  // return false返回-1
  return value > 3;
});

console.log(newArr); // 4
```

手写 findIndex

```js
const arr = [1, 2, 3, 4, 5];

/**
 * 解析:
 * - 我们最终返回的是一个index
 * - 如果findIndex第一个回调函数(自定义的callback函数)返回true,那么findIndex返回对应的index
 * - 反之返回的是一个false,那么findIndex返回一个-1
 * @param {Function} callback
 * @returns {Number}
 */
Array.prototype.t_findIndex = function (callback) {
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) return i;
  }
  return -1;
};

const newArr = arr.t_findIndex((value, index, array) => {
  return value > 3;
});

console.log(newArr); // 3
```

### 1.8 filter

[Array.prototype.filter](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)方法跟 find 大致相同,find 只返回第一个匹配到的元素,而 filter 返回所有匹配到的元素然后放在一个数组里

```js
const arr = [1, 2, 3, 4, 5];

const newArr = arr.filter((value, index, array) => {
  return value > 3;
});

console.log(newArr); // [4,5]
```

手写 filter

```js
const arr = [1, 2, 3, 4, 5];

/**
 * 解析:
 * - 我们最终是需要返回一个数组的
 * - 所以我们可以在循环外面定义一个数组,将来将它返回
 * - 如果filter第一个回调函数(自定义的callback函数)返回true,那么就把对应的item放入到前面我们定义的数组里
 * - 反之返回false,那么就啥也不干
 * @param {Function} callback
 * @returns {Array}
 */
Array.prototype.t_filter = function (callback) {
  const result = [];
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) result.push(this[i]);
  }
  return result;
};

const newArr = arr.t_filter((value, index, array) => {
  return value > 3;
});

console.log(newArr); // [4,5]
```

### 1.9 some

[Array.prototype.some](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some)方法表示对每一个元素调用一个函数,如果有一个返回的结果为 true,那么就返回 true,反之返回 false

```js
const arr = [1, 2, 3, 4, 5];

const flag = arr.some((value, index, array) => {
  return value > 3;
});

console.log(flag); // true
```

手写 some

```js
const arr = [1, 2, 3, 4, 5];

/**
 * 解析:
 * - 如果some第一个回调函数(自定义的callback函数)有一项返回true,那么some返回true
 * - 反之返回false
 * @param {Function} callback
 * @returns {Boolean}
 */
Array.prototype.t_some = function (callback) {
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) return true;
  }
  return false;
};

const flag = arr.t_some((value, index, array) => {
  return value > 3;
});

console.log(flag); // true
```

### 1.10 every

[Array.prototype.every](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every)方法表示跟 some 差不多,只是 every 需要每一项(所有)都返回 true,那么才会返回 true,反之返回 false

```js
const arr = [1, 2, 3, 4, 5];

const flag = arr.every((value, index, array) => {
  return value > 3;
});

console.log(flag); // false
```

手写 every

```js
const arr = [1, 2, 3, 4, 5];

/**
 * 解析:
 * - every跟some相反(你可以看做 || 和 && 的区别)
 * - 如果every的第一个回调函数(自定义的callback函数)有一项返回false,那么every就返回false
 * - 反之every返回true
 * @param {Function} callback
 * @returns {Boolean}
 */
Array.prototype.t_every = function (callback) {
  for (let i = 0; i < this.length; i++) {
    if (!callback(this[i], i, this)) return false;
  }
  return true;
};

const flag = arr.t_every((value, index, array) => {
  return value > 3;
});

console.log(flag); // false
```

### 1.11 reduce

[Array.prototype.reduce](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)方法表示我们需要遍历并根据数组计算某个值,并将结果返回

```js
const arr = [1, 2, 3, 4, 5];

/*
 * accumulator表示上一次函数的结果
 * 这里的0代表第一次运行的默认值(就是第一次accumulator的值)
 */
const newArr = arr.reduce((accumulator, item, index, array) => {
  return accumulator + item;
}, 0);

console.log(newArr); // false
```

![](https://gitee.com/itsandy/picgo-img/raw/master/JavaScript/Snipaste_2022-02-18_16-46-46.png)

手写 reduce

```js

```

### 1.12 Object.keys

[Object.keys](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)方法表示返回一个包含该对象所有的 key 的**字符串数组**

```js
// 数组的key就是index
const arr = [1, 2, 3, 4, 5];

console.log(Object.keys(arr)); // ['0','1','2','3','4']
```

### 1.13 Object.values

[Object.values](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/values) 方法表示返回一个包含该对象所有的值的数组

```js
const arr = [1, 2, 3, 4, 5];

console.log(Object.values(arr)); // [1, 2, 3, 4, 5]
```

### 1.14 Object.entries

[Object.entries](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)方法表示返回一个包含该对象所有 key, value 键值对的数组。

```js
const arr = [1, 2, 3, 4, 5];

console.log(Object.entries(arr)); // [['0',1], ['1',2], ['2',3], ['3',4], ['4',5]]
```

## 二、数组常用的方法

手写 fill

```js
const arr = [1, 2, 3, 4, 5];

/**
 * 解析:
 * - fill替换的item包含头部不包含尾部(包头不包尾)
 * - 我们需要返回传进来的数组
 * - 从start开始循环,直到end结束
 * - 然后把对应的item改为item
 * - start的默认值为0
 * - end的默认值为数组的长度
 * - 如果 start 是个负数, 则开始索引会被自动计算成为 length+start
 * - 如果 end 是个负数, 则结束索引会被自动计算成为 length+end
 * @param {any} value
 * @param {Number} start
 * @param {Number} end
 * @returns {any}
 */
Array.prototype.t_fill = function (
  value,
  start = 0,
  end = this.length
) {
  start = start < 0 ? start + this.length : start;
  end = end < 0 ? end + this.length : end;
  for (let i = start; i < end; i++) {
    this[i] = value;
  }
  return this;
};

console.log(arr.t_fill(4, 2, 3)); // [1,2,4,4,5]
```

?>😊 持续更新,且不断整理..
