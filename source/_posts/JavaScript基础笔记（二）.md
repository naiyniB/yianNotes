---
title: 📚 JavaScript 对象与原型链
date: 2025.9.1
---


> 深入理解对象、函数、`[[Prototype]]`、`prototype`、`constructor` 与属性查找机制

---

## 一、核心概念速览

| 概念 | 类型 | 所有者 | 作用 |
|------|------|--------|------|
| `[[Prototype]]` | 内部槽（Internal Slot） | 所有对象（除 `null`） | 指向原型，用于属性查找 |
| `prototype` | 属性（Property） | 函数对象（可构造函数） | 作为实例的“模板”对象 |
| `constructor` | 属性（Property） | `prototype` 对象默认属性 | 指向构造函数本身 |
| 原型链 | 机制 | —— | 通过 `[[Prototype]]` 链式查找属性 |

---

## 二、`[[Prototype]]`：对象的“原型指针”

### ✅ 是什么？

- 每个对象（除 `null`）都有一个内部属性 `[[Prototype]]`
- 它是一个**指针**，指向该对象的“原型”（另一个对象或 `null`）
- 它是实现**继承**和**属性查找**的核心

### ✅ 如何访问？

- **推荐**：`Object.getPrototypeOf(obj)`
- **不推荐**：`obj.__proto__`（已废弃，仅用于调试）

```js
const obj = {};
Object.getPrototypeOf(obj) === Object.prototype; // true
```

---

## 三、`prototype`：函数的“原型对象”

### ✅ 是什么？

- 只有**函数**（特别是可构造函数）才有 `prototype` 属性
- 它是一个**普通对象**，默认包含 `constructor` 属性
- 它的作用是：作为 `new` 出来的实例的“原型模板”

```js
function Person(name) {
    this.name = name;
}
// Person.prototype 是一个对象
// Person.prototype.constructor → Person
```

> ⚠️ 箭头函数没有 `prototype` 属性（不能用 `new`）

---

## 四、`new` 操作符的四步机制

当你执行 `new Constructor()` 时，引擎自动执行：

1. **创建新对象**  
   → `const obj = {}`

2. **链接原型**  
   → `obj.[[Prototype]] = Constructor.prototype`

3. **绑定 `this` 并执行构造函数**  
   → `Constructor.call(obj, ...args)`

4. **返回对象**  
   → `return obj`  
   （如果构造函数返回一个对象，则返回该对象）

```js
const p = new Person("Alice");
// p.[[Prototype]] === Person.prototype → true
```

> ✅ 第 2 步是建立原型链的关键！

---

## 五、`constructor`：反向引用

### ✅ 是什么？

- `Constructor.prototype.constructor` 默认指向 `Constructor` 本身
- 它是 `prototype` 对象被创建时自动添加的

```js
Person.prototype.constructor === Person; // true
```

### ✅ 作用

- 让实例能追溯“我是由谁创建的”
- 支持动态创建同类实例

```js
p.constructor === Person; // true（通过原型链找到）
```

> ⚠️ 注意：`constructor` 不是实例的自有属性，可被修改，不绝对可靠

---

## 六、原型链（Prototype Chain）：属性查找机制

### ✅ 查找流程

当访问 `obj.prop` 时，JS 按以下顺序查找：

1. **自有属性**：`obj` 自身是否有 `prop`
2. **原型**：`obj.[[Prototype]]` 上是否有 `prop`
3. **原型的原型**：继续向上查找 `[[Prototype]]` 链
4. **直到 `null`**：查不到返回 `undefined`

```js
p.toString(); // 查找路径：
```

```
p 
  └─ 自有？ ❌
  ↓ [[Prototype]]
Person.prototype 
  └─ 有 toString？ ❌
  ↓ [[Prototype]]
Object.prototype 
  └─ 有 toString？ ✅ 找到！
  ↓ [[Prototype]]
null
```

> ✅ 所有普通对象的原型链最终都指向 `Object.prototype → null`

---

## 七、关键关系图解

```
         prototype (属性)
Fn (函数) ————————————————————→ Fn.prototype (对象)
                                   ↑
                                   | [[Prototype]] (内部指针)
                                   |
                               新对象 obj
                                   ↑
                                   | constructor
                                   |
                          obj.constructor → Fn
```

- `Fn.prototype` 是“模板”
- `obj.[[Prototype]]` 指向 `Fn.prototype`
- `Fn.prototype.constructor` 指向 `Fn`
- `obj.constructor` 通过原型链找到 `Fn`

---

## 八、常见误区纠正

| 误区 | 正确理解 |
|------|----------|
| “所有对象都有 `prototype`” | ❌ 只有函数有 `prototype` 属性 |
| “原型链是 `prototype` 的链” | ❌ 是 `[[Prototype]]` 的链 |
| “`constructor` 是实例的属性” | ❌ 是原型上的属性，通过继承访问 |
| “`prototype` 指向下一个原型” | ❌ `[[Prototype]]` 才是原型指针 |
| “`__proto__` 是标准” | ❌ 应使用 `Object.getPrototypeOf()` |

---

## 九、代码验证

```js
function Person(name) {
    this.name = name;
}

const p = new Person("Alice");

// 核心连接
console.log(p.__proto__ === Person.prototype); // true
console.log(Object.getPrototypeOf(p) === Person.prototype); // true

// constructor
console.log(Person.prototype.constructor === Person); // true
console.log(p.constructor === Person); // true
console.log(p.hasOwnProperty('constructor')); // false

// 原型链
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null); // true
```

---

## ✅ 总结

> - **`[[Prototype]]` 是链，`prototype` 是源**
> - **函数有 `prototype`，对象有 `[[Prototype]]` , 函数也是一个对象**
> - **`new` 四步：创建 → 链接 → 绑定 → 返回**
> - **链接关键：`obj.[[Prototype]] = Fn.prototype`**
> - **属性查找走 `[[Prototype]]` 链，不走 `prototype`**
> - **`constructor` 指向构造函数，不是实例本身**
> - **原型链终点是 `null`，查不到就 `undefined`**

---
