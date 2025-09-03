---
title:  npm package "入口"
date: 2025-09-01 10:00:00
updated: 2025-09-02 11:00:00
---

> **“入口”是别人使用你的 npm 包时，程序从哪个文件开始加载。它是包的“正门”。**



## 🔑 一、核心入口字段

| 字段 | 用途 | 使用场景 |
|------|------|----------|
| `main` | CommonJS 入口（Node.js 默认） | `require('pkg')` |
| `module` | ES Module 入口（打包工具优先） | `import 'pkg'`（Webpack/Vite） |
| `exports` | 精细控制多入口（现代推荐） | 现代项目首选 |
| `browser` | 浏览器环境入口（旧方式） | 兼容老项目 |

---

## 🧩 二、字段详解

### 1. `main` —— Node.js 的“老朋友”
- ✅ 作用：定义 `require('your-pkg')` 时加载的文件
- ✅ 默认值：`index.js`（不推荐依赖默认）
- ✅ 格式：CommonJS（`module.exports`）
- ❌ 问题：不支持 tree-shaking（打包工具无法“摇掉”未用代码）

```json
{
  "main": "dist/index.js"
}
```

---

### 2. `module` —— 打包工具的“新宠”
- ✅ 作用：为 Webpack、Vite 等工具提供 **ES Module** 入口
- ✅ 格式：ES6 `export` / `import`
- ✅ 优势：支持 **tree-shaking**，减小最终打包体积
- ⚠️ 注意：必须提供 `.js` 文件（ESM 语法）

```json
{
  "module": "dist/esm/index.js"
}
```

---

### 3. `exports` —— 现代推荐方式（Node.js 12+）
- ✅ 作用：**精确控制入口行为**，支持条件导出
- ✅ 优势：
  - 支持 `import` / `require` 分别指向不同文件
  - 可定义多个子入口（如 `pkg/helpers`）
  - 隐藏内部文件（如 `src/`）
- ✅ 推荐新项目使用

```json
{
  "exports": {
    ".": {
      "import": "./dist/esm/index.js",   // ESM 用户
      "require": "./dist/cjs/index.js"  // CJS 用户
    },
    "./helpers": "./dist/esm/helpers.js"
  }
}
```

使用：
```js
import main from 'pkg';           // 走 "."
import { helper } from 'pkg/helpers'; // 走 "./helpers"
```

---

## 🔄 三、入口查找优先级

### 1. 打包工具（Webpack/Vite）查找顺序：
```
exports → module → main → index.js
```

### 2. Node.js 查找顺序：
```
exports → main → index.js
```

> ✅ 建议：同时提供 `exports` + `main`，确保最大兼容性。

---

## 🌐 四、不同用户如何使用你的包？

| 用户类型 | 使用方式 | 加载哪个字段？ |
|--------|----------|----------------|
| Node.js `require` 用户 | `const pkg = require('pkg')` | `main` 或 `exports.require` |
| Webpack/Vite `import` 用户 | `import pkg from 'pkg'` | `module` 或 `exports.import` |
| 浏览器原生 `import` | `<script type="module">import from 'pkg'</script>` | `module` 或 `exports.import` |
| TypeScript 用户 | `import { x } from 'pkg'` | 同上 + `types` 字段 |

---

## ⚠️ 五、常见误区澄清

| 误区 | 正确认知 |
|------|----------|
| “只写 `main` 就行” | ✅ 功能正常，但前端用户无法 tree-shaking ❌ |
| “浏览器能用 `require`” | ❌ 原生不支持！必须用打包工具转换 |
| “Node.js 不能用 `import`” | ❌ 可以！需 `.mjs` 或 `"type": "module"` |
| “`module` 字段是给浏览器用的” | ❌ 是给**打包工具**用的，不是浏览器原生用 |

---

## ✅ 六、最佳实践建议

1. **新项目必用 `exports`**  
   → 精细控制，未来兼容

2. **通用库必须同时提供 `main` 和 `module`**  
   → 服务 Node 用户 和 前端用户

3. **前端库优先优化 `module` / `exports.import`**  
   → 确保支持 tree-shaking

4. **入口文件只暴露公共 API**  
   → 不要让用户直接引用 `src/utils.js`

5. **发布前用 `npm pack --dry-run` 验证**  
   → 看看 `.tgz` 里有没有入口文件

---

## 🎯 总结：入口设计原则

> - **兼容性**：让 `require` 和 `import` 用户都能用
> - **性能**：让前端项目能 tree-shaking
> - **安全**：用 `exports` 隐藏内部实现
> - **清晰**：入口文件只导出该导出的内容

---

✅ **一句话收尾**：  
**“入口”不是技术细节，而是你与使用者的“契约”——你决定他们如何开始使用你的代码。**

---
