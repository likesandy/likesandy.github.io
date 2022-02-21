# 链表

## 一、链表是什么？

1. 多个元素组成的列表
2. 元素存储不连续，用 next 指针连在一起

![](https://gitee.com/itsandy/picgo-img/raw/master/数据结构与算法/20220219155652.png)

## 二、数组 vs 链表

数组：增删非首尾元素时往往需要移动元素

链表：增删非首尾元素，不需要移动元素，只需要更改 next 的指向即可

## 三、JS 模拟链表

在 JS 中可以使用 Object 模拟链表

```js
// a就是整个链表的头部
// d就是整个链表的尾部
const a = { val: "a" };
const b = { val: "b" };
const c = { val: "c" };
const d = { val: "d" };

a.next = b;
b.next = c;
c.next = d;

// 遍历链表
let p = a;
while (p) {
  console.log(p.value); // a,b,c,d
  p = p.next;
}

// 插入链表
// 比如我们需要在c的后面插入一个e
const e = { value: "e" };
c.next = e;
e.next = d;

// 删除链表(删除e)
c.next = d;
```
