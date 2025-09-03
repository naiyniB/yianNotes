---
title: JavaScript 执行期上下文 & 闭包
date: 2025.9.1
---
## 一、执行上下文（Execution Context）

JavaScript 执行代码时，会为每一段可执行代码创建一个 **执行上下文**，它是代码运行的“环境”。

### 三种类型

1. **全局执行上下文**：脚本启动时创建，全局作用域。
2. **函数执行上下文**：每次调用函数时创建。
3. **eval 执行上下文**：`eval` 中执行代码时创建（不常用）。

### 执行上下文的结构

每个执行上下文在运行时包含三个核心部分：

| 组件 | 说明 |
|------|------|
| **LexicalEnvironment** | 当前上下文的词法环境（管理 `let`/`const`） |
| **VariableEnvironment** | 当前上下文的变量环境（管理 `var`/函数声明） |
| **ThisBinding** | `this` 的值 |

> ✅ 简记：**执行上下文 = 词法环境 + 变量环境 + this**

---

## 二、词法环境 vs 变量环境

| 名称 | 负责 | 特点 |
|------|------|------|
| **LexicalEnvironment** | `let`、`const` | 有块级作用域，有“暂时性死区”（TDZ） |
| **VariableEnvironment** | `var`、函数声明 | 有变量提升，无块级作用域 |

> ⚠️ 注意：函数开始执行时，`LexicalEnvironment` 和 `VariableEnvironment` 通常相同，但后续可能分离（如 `eval`）。

---

## 三、词法环境（Lexical Environment）的内部结构

每个词法环境是一个抽象结构，包含两个部分：

| 部分 | 说明 |
|------|------|
| **Environment Record** | 存储变量和函数声明的实际位置（如对象环境记录、声明性环境记录） |
| **Outer Environment Reference** | 指向**外层词法环境**的引用，形成作用域链 |

> ✅ 作用域链 = 通过 `Outer` 一级一级向上查找

---

## 四、函数的内部属性：`[[Environment]]`

- **定义**：函数对象的一个**内部槽（Internal Slot）**
- **创建时机**：函数**定义时**自动创建
- **值**：指向函数**定义时所处的词法环境**
- **作用**：实现闭包和词法作用域的核心

```js
function outer() {
    let x = 10;
    function inner() { console.log(x); }
    // inner.[[Environment]] = outer 的词法环境
}
```

> ✅ `[[Environment]]` 是函数的“出生地记忆”

---

## 五、`Outer Environment Reference` 是谁初始化的？

> **关键问题：词法环境的 `Outer` 从哪来？**

### ✅ 答案

`Outer Environment Reference` 是在**创建词法环境时**，由 JavaScript 引擎根据以下规则自动设置：

| 场景 | `Outer` 的来源 |
|------|----------------|
| **函数调用** | 来自函数的 `[[Environment]]` 属性 |
| **块级作用域**（如 `if`、`for`） | 来自当前执行上下文的词法环境（词法嵌套结构） |

### 🎯 核心流程（函数调用时）

1. 调用函数 `fn()`
2. 创建 `fn` 的执行上下文
3. 创建 `fn.LexicalEnvironment`
4. 设置 `fn.LexicalEnvironment.Outer = fn.[[Environment]]`

> ✅ 所以：`[[Environment]]` 是 `Outer` 的“源头”

---

## 六、闭包（Closure）

### 1. 什么是闭包？
>
> 一个函数能够访问其**定义时所在的作用域**中的变量，即使该函数在外部执行。

### 2. 闭包的形成条件

- 内层函数引用了外层函数的变量
- 内层函数被传递到外层函数之外（如返回、赋值）

### 3. 闭包的本质
>
> **闭包 = 函数 + 函数的 `[[Environment]]` 引用**

### 4. 例子

```js
function counter() {
    let count = 0;
    return function() {
        count++;
        return count;
    };
}
const inc = counter();
inc(); // 1
inc(); // 2 → count 被闭包保留
```

> 🔗 原因：`inner.[[Environment]]` 指向 `counter` 的词法环境，`count` 不会被回收。

---

## 七、this 的指向

| 函数类型 | this 指向 |
|--------|----------|
| **普通函数** | 谁调用，谁就是 `this`<br>`obj.fn()` → `this = obj` |
| **箭头函数** | 继承**定义时外层作用域的 `this`**<br>（没有自己的 `this`） |
| **全局环境** | 浏览器：`window`，Node.js：`global` |

> ✅ 箭头函数的 `this` 是词法绑定，普通函数是动态绑定。

---

## 八、变量提升（Hoisting）

| 声明方式 | 是否提升 | 初始化 | 访问时机 |
|--------|----------|--------|---------|
| `var` | ✅ 是 | `undefined` | 声明前可访问 |
| `let` / `const` | ✅ 声明提升 | 不初始化（TDZ） | 声明前访问报错 |

> ⚠️ `let`/`const` 也有“提升”，但不能在声明前使用（暂时性死区）。

---

## 九、未声明变量

- `x = 10` → 自动成为**全局变量**
- 存储位置：全局执行上下文
- 应避免，防止污染全局

---

## 十、开发者工具中的观察

| 概念 | 是否可在代码中访问 | 是否可在 DevTools 中观察 |
|------|------------------|------------------------|
| `[[Environment]]` | ❌ 否 | ❌ 否 |
| `[[Scopes]]` | ❌ 否 | ✅ 是（Chrome 调试用） |
| 执行上下文 | ❌ 否 | ✅ 是（Scope 面板） |
| `this` | ✅ 可通过 `console.log(this)` | ✅ 是 |

> 🛠️ DevTools 的 `[[Scopes]]` 是 `[[Environment]]` 的调试快照。

---

## 🧩 核心图解（文字版）

```
函数定义时：
    inner.[[Environment]] → outer 的词法环境

函数执行时：
    创建 inner 的执行上下文
        LexicalEnvironment:
            Environment Record: { d: 4 }
            Outer → inner.[[Environment]] → outer 的词法环境
        VariableEnvironment: ...
        ThisBinding: ...

变量查找：
    inner → outer → 全局 → 找不到 → 报错
```

---

## ✅ 总结

> - **定义时确定作用域，调用时确定 this**
> - `var` 提升为 `undefined`，`let/const` 有暂时性死区
> - 闭包是函数 + `[[Environment]]`
> - `[[Environment]]` 是函数的，`Outer` 是词法环境的
> - `Outer` 的值来自 `[[Environment]]`
> - 作用域链 = `Outer` 连成的链
> - 执行上下文有 `LexicalEnvironment` 和 `VariableEnvironment`，分工明确

---
