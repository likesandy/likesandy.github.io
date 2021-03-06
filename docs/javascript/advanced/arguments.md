## 认识 arguments

arguments 是一个 对应于 传递给函数的参数 的 **类数组**(array-like)对象。

```js
function foo() {
  console.log(arguments);
}

foo(10, 20);
```

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/81395/2/17501/3571/613d8d89Ec4a444a6/93439b059dfd128e.png)

array-like 意味着它看起来是一个数组，本质上是一个对象

- 但是它却拥有数组的一些特性，比如说 length，比如可以通过 index 索引来访问；
- 但是它却没有数组的一些方法，比如 forEach、map 等；

## arguments 转成 array

在以前开发中经常会让我们把 arguments 转成 array

> 方式一

手动遍历 arguments，然后把结果放在一个新的数组里面

```js
function foo() {
  var newArr = [];
  for (var i = 0; i < arguments.length; i++) {
    newArr.push(arguments[i]);
  }
  console.log(newArr);
}

foo(10, 20);
```

> 方式二

通过显式绑定 arguments 为数组的 slice 方法中

这其实就要理解 slice 的内部原理了

这里我也就简单用 js 模拟一下 slice（还是那句话边界情况不过度考虑）

```js
Array.prototype.gtslice = function (start = 0, end) {
  var arr = this;
  end = end ?? arr.length;
  var newArr = [];
  for (var i = start; i < end; i++) {
    newArr.push(arr[i]);
  }
  return newArr;
};

var arr = [1, 2, 3];
var result = arr.gtslice(1, 3);
console.log(result);
```

其实内部也就是通过遍历来进行操作的

```js
function foo() {
  var arr1 = Array.prototype.slice.call(arguments);
  console.log(arr1);
}

foo(10, 20);
```

> 方式三

我们可以通过 Array 的原型上获取，当然也可以从 Array 的实例上获取

```js
function foo() {
  var arr2 = [].slice.call(arguments);
  console.log(arr2);
}

foo(10, 20);
```

> 方式四

ES6 的from方式

```js
function foo() {
  var arr3 = Array.from(arguments);
  console.log(arr3);
}

foo(10, 20);
```

> 方式五

也可以通过扩展运算符的方式

```js
function foo() {
  var arr4 = [...arguments];
  console.log(arr4);
}

foo(10, 20);
```

## 箭头函数不绑定arguments

箭头函数是不绑定arguments的，所以我们在箭头函数中使用arguments会去上层作用域查找：

```js
var foo = () => {
  console.log(arguments);
}

foo()
```

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/201729/13/6489/5148/613d9758Ea7b7423f/b8b380cd47a2c88d.png)

在浏览器中全局是没有arguments，但是在node中是有arguments的

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/200203/19/8114/28020/613d9796E5c06dc1f/75b76eb0afb66ab8.png)


> [!Danger]
> arguments算是早期JavaScript的东西，ES6之前会用到arguments，但是在ES6之后基本上会使用剩余参数来替代arguments
