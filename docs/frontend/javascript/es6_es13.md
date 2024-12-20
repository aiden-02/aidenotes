---
title: ES6~ES13 新特性
order: 8
---

# ES6~ES13 新特性

只列常用的

## 词法环境

词法环境（`Lexical Environment`）是 JavaScript 中的一种内部机制，用于管理变量和函数的`作用域`。它是一个对象，包含了当前`执行上下文`中的所有变量和函数声明。

每个函数在创建时都会创建一个词法环境，用于存储函数内部的变量和函数声明。词法环境由两个部分组成：

1. 环境记录（`Environment Record`）：存储变量和函数声明的实际位置。
2. 外部环境引用（`Outer Environment Reference`）：指向外部词法环境，通常是创建该函数的外部函数的词法环境。

当 JavaScript 引擎查找变量时，会首先在`当前词法环境`中查找，如果找不到，则会沿着`外部环境引用`逐级向上查找，直到`全局环境`。

:::warning 不要和原型链混淆
只有在查找`对象的属性`时，才会涉及原型链的查找。

具体来说：

`变量查找`：先在`当前词法环境中`查找，如果找不到，则沿着`外部词法环境`逐级向上查找，直到全局环境。

`对象属性查找`：先在`对象自身的属性`中查找，如果找不到，则沿着对象的`原型链`逐级向上查找。
:::

全局环境（`Global Environment`）。全局环境是`最外层`的词法环境，它包含了全局作用域中的所有变量和函数声明。

全局环境由以下两部分组成：

1. 全局对象（`Global Object`）：在浏览器中是 `window` 对象，在 Node.js 中是 `global` 对象。全局对象包含了所有全局变量和全局函数。
2. 全局环境记录（`Global Environment Record`）：存储全局变量和函数声明的实际位置。

当 JavaScript 代码在`全局作用域`中执行时，会创建一个`全局环境`。所有在全局作用域中声明的变量和函数都会被添加到全局环境中。

## let、const

`let` 关键字：

- 从直观的角度来说，let 和 var 没有太大区别，都用于声明变量

`const` 关键字：

- 若赋值的是`简单类型`，则`不能被修改`
- 若赋值的是`引用类型`，那么可以通过`引用`找到对应对象，修改对象内容

### 暂时性死区

暂时性死区是指在使用 `let` 和 `const` 声明变量时，从变量所在的`词法环境被实例化到变量声明之间`的这段时间内，变量是不可访问的。如果在这段时间内尝试访问变量，会抛出 `ReferenceError` 错误。

也就是说，代码在`执行阶段`，执行到该`变量声明之前`，该变量都是不可访问的

但其实，在代码`编译阶段`，`let` 和 `const` 声明的变量，已经在环境记录中创建了一个条目，但区别于 `var`，let 和 const 的变量`不会被初始化`

所以，let、const 变量在声明之前不能被访问，称之为`没有作用域提升`

### 块级作用域

块级作用域是指在一个代码块（由 `{}` 包围的代码区域）内部声明的变量只能在该`代码块内部`访问。ES6 引入了 let 和 const 关键字来创建块级作用域。if 语句、for 循环等都可以创建块级作用域。

### var、let 和 const 的区别是什么？

`var` 声明的变量具有函数作用域或全局作用域，没有块级作用域，且存在变量提升。

`let` 声明的变量具有块级作用域，没有变量提升，在声明之前访问会导致 `ReferenceError`。

`const` 声明的变量具有块级作用域，没有变量提升，声明时必须初始化，且不能重新赋值。

## Symbol

Symbol 值是通过 Symbol 函数来生成的，生成后可以作为属性名

Symbol 函数每次执行后的值都是`独一无二的`

那如果我们想创建相同的 Symbol 该怎么办

- 可通过 `Symbol.for` 来创建相同值
- 也可通过 `Symbol.keyFor` 来获取对应 key

```js
const s1 = Symbol.for('abc')
const s2 = Symbol.for('abc')
console.log(s1 === s2) //true
const key = Symbol.keyFor(s1)
console.log(key) // abc
```

## Set 和 WeakSet

`Set` 是一个新增的数据结构，可以用来保存数据，类似于数组，但是和数组的区别是元素`不能重复`

创建 Set 我们需要通过 Set 构造函数（暂时没有字面量创建的方式）

Set 有一个非常常用的功能就是给`数组去重`

Set 常见用法

- `属性 size`：返回 Set 中元素个数
- `add(value)`：添加某个元素，返回 Set 对象本身
- `delete(value)`：从 set 中删除和这个值相等的元素，返回 `boolean` 类型；
- `has(value)`：判断 set 中是否存在某个元素，返回 `boolean` 类型；
- `clear()`：清空 set 中所有的元素，没有返回值；

另外，Set 支持`for of`遍历

和`Set`类似的另外一个数据结构称之为`WeakSet`，也是内部元素不能重复的数据结构。

与`Set`的区别是

- WeakSet 中只能存放`对象类型`，不能存放基本数据类型；
- WeakSet 对元素的引用是`弱引用`，如果没有其他引用对某个对象进行引用，那么 GC 可以对该对象进行`回收`

WeakSet 常见方法

- add(value)：添加某个元素，返回 `WeakSet 对象本身`；
- delete(value)：从 WeakSet 中删除和这个值相等的元素，返回 `boolean` 类型；
- has(value)：判断 WeakSet 中是否存在某个元素，返回 `boolean` 类型；

`WeakSet无法被遍历`

WeakSet 的设计初衷是为了存储对象的`弱引用`，以便垃圾回收机制能够更有效地回收不再使用的对象。因此，WeakSet `不提供遍历其元素的方法`。因为遍历操作需要保持对元素的强引用，这与 WeakSet 的设计目标相冲突。

## Map 和 WeakMap

另外一个新增的数据结构是 Map，用于存储`映射关系`

Map 常用方法

- 属性`size`：返回 Map 中元素的`个数`；
- `set(key, value)`：在 Map 中添加 key、value，并且返回整个 Map 对象；
- `get(key)`：根据 key 获取 Map 中的 value；
- `has(key)`：判断是否包含某一个 key，返回`Boolean`类型；
- `delete(key)`：根据 key 删除一个键值对，返回`Boolean`类型；
- `clear()`：清空所有的元素；
- Map 也可以通过 `for of` 进行遍历。

`WeakMap` 与 `Map` 的区别

- 区别一：WeakMap 的 key 只能使用`对象`，不接受其他的类型作为 key；
- 区别二：WeakMap 的 key 对对象的引用是`弱引用`，如果没有其他引用引用这个对象，那么 GC 可以回收该对象；

### Map 和 Object 的区别

1. 键的类型：

- `对象`的键只能是字符串或 Symbol。
- `Map`的键可以是任意类型，包括对象、函数、基本类型等。

2. 键值对的顺序：

- `对象`的键值对没有顺序。
- `Map`的键值对是有序的，按照插入顺序进行迭代。

3. 迭代：

- `对象`需要手动获取键数组然后迭代。
- `Map`可以直接迭代，支持 `for...of` 循环。

4. 性能：

- `Map`在频繁增删键值对的场景下性能更好。
- `对象`在频繁增删键值对时性能较差。

5. 默认键：

- `对象`有一些默认的键（如 `__proto__`）。
- `Map`没有默认的键，所有键都是显式添加的。

6. 大小：

- `对象`没有内置的属性来获取大小。
- `Map`有 `size` 属性来获取键值对的数量。

## Proxy

如果我们希望监听一个`对象`的相关操作，那么我们可以先创建要给代理对象(`Proxy`对象)

- 首先，我们需要 new `Proxy` 对象，并且传入需要侦听的对象以及一个处理对象，可以称之为 `handler`；

```js
const p = new Proxy(target, handler)
```

- 其次，我们之后的操作都是直接对 `Proxy` 的操作，而不是原有的对象，因为我们需要在 `handler` 里面进行侦听；

相比于 `Object.defineProperty` 只有 set， get 属性描述符来监听对象，Proxy 有多达 13 种[捕捉器（`Trap`）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy#handler_%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%96%B9%E6%B3%95)

## Reflect

[`Reflect`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect) 也是 ES6 新增的一个 API，它是一个对象，字面的意思是`反射`。

它提供了很多操作 Javascript 对象的方法，有点像 Object 中操作对象的方法

尽管 `Reflect` 和 `Object` 都提供了一些相似的方法，但它们的设计目标和用途有所不同。`Reflect` 更加注重与 `Proxy` 的集成和一致性，而 `Object` 更加注重对象的创建和属性操作。
