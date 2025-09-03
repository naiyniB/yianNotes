---
title: npm `package.json` 文件说明
date: 2025-09-01 10:00:00
updated: 2025-09-02 11:00:00
---

---

## ✅ 一句话总结

> `package.json` 是一个 **项目说明书**，它告诉：
>
> - **你**：这个项目依赖哪些包、怎么运行
> - **npm / yarn / pnpm**：怎么安装、发布、管理这个项目
> - **别人**：这是个什么项目、怎么使用它

它就像一本书的“封面 + 目录 + 前言”。

---

## 🧩 举个生活例子：开餐馆

| 类比 | 对应 `package.json` |
|------|---------------------|
| 餐馆名字 | `"name": "小王面馆"` |
| 菜单（有什么菜） | `"scripts"`：`start`, `build` 等命令 |
| 食材从哪进货 | `"dependencies"`：用了哪些 npm 包 |
| 厨房用什么工具（炒菜用燃气灶） | `"devDependencies"`：开发时用的工具 |
| 客人怎么点餐 | `"main"`：别人 `require` 时从哪开始 |
| 餐馆地址、电话、营业时间 | `"version"`, `"description"`, `"author"`, `"license"` |

---

## 🔍 核心作用分类

### 1️⃣ 项目元信息（基本信息）

这些是“介绍项目”的字段：

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "一个简单的计算器应用",
  "author": "小明",
  "license": "MIT",
  "homepage": "https://github.com/xiaoming/my-app#readme",
  "repository": {
    "type": "git",
    "url": "https://github.com/xiaoming/my-app.git"
  }
}
```

> ✅ 作用：别人一看就知道这是什么项目、谁写的、能不能商用（license）、代码在哪。

---

### 2️⃣ 依赖管理（自动安装第三方包）

这是 `package.json` 最强大的功能！

#### ✅ `dependencies`：项目运行必须的包
```json
"dependencies": {
  "lodash": "^4.17.21",
  "react": "^18.0.0"
}
```
- 运行 `npm install` 时，会自动下载这些包到 `node_modules`
- 别人拿到你的项目，一运行 `npm install`，所有依赖自动装好

#### ✅ `devDependencies`：开发时用的工具
```json
"devDependencies": {
  "vite": "^4.0.0",
  "eslint": "^8.0.0"
}
```
- 比如打包工具、代码检查工具
- 发布包时，别人不需要这些（但你开发时需要）

> 🔑 核心价值：**告别“手动下载 JS 文件”时代，实现依赖自动化管理**

---

### 3️⃣ 脚本命令（快捷方式）

```json
"scripts": {
  "start": "node index.js",
  "build": "babel src -d lib",
  "test": "jest"
}
```

你可以运行：

```bash
npm run start   → 执行 node index.js
npm run build   → 执行 babel src -d lib
```

> ✅ 作用：把复杂命令简化成一句话，团队协作时统一操作方式。

---

### 4️⃣ 包入口（别人怎么引用你）

当你发布一个 npm 包时，这些字段告诉别人“从哪开始用”：

```json
{
  "main": "lib/index.js",           // Node.js require 用
  "browser": "dist/index.js",       // 浏览器打包用
  "module": "es/index.js"           // 支持 tree-shaking 的格式
}
```

> ✅ 作用：让别人能正确加载你的代码。

---

### 5️⃣ 发布控制

```json
{
  "files": ["lib", "dist"],         // 只发布这些文件
  "private": true,                  // 设为 true 就不能发布到共有仓库 防止不小心发包
  "publishConfig": {
    "registry": "https://my-registry.com"
  }
}
```

> ✅ 作用：控制包的发布行为。

---

### 6️⃣ 其他工具配置（一配置多用）

很多工具会读 `package.json`，比如：

```json
"eslintConfig": {
  "rules": {
    "semi": "error"
  }
},
"browserslist": [
  "> 1%",
  "last 2 versions"
]
```

> ✅ 作用：不用单独写 `.eslintrc`、`.browserslistrc` 文件，全在 `package.json` 里配。

---

## 📌 总结：`package.json` 的 6 大作用

| 作用 | 关键字段 | 举例 |
|------|----------|------|
| 1. 项目介绍 | `name`, `version`, `description` | 别人一看就知道这是啥 |
| 2. 依赖管理 | `dependencies`, `devDependencies` | `npm install` 自动装包 |
| 3. 命令快捷方式 | `scripts` | `npm run build` 打包 |
| 4. 包入口 | `main`, `browser` | 别人 `require` 时从哪开始 |
| 5. 发布控制 | `files`, `private` | 控制发布内容 |
| 6. 工具配置 | `eslintConfig`, `browserslist` | 统一开发规范 |

---

## ✅ 什么时候会用到 `package.json`？

| 场景 | 用到 `package.json` 吗？ |
|------|--------------------------|
| 创建新项目 | ✅ `npm init` 生成它 |
| 安装包 | ✅ `npm install xxx` 会写入 `dependencies` |
| 运行项目 | ✅ `npm start` 读 `scripts` |
| 发布包 | ✅ 必须有 `name`, `version`, `main` |
| 别人使用你的项目 | ✅ 他们靠它知道怎么运行 |

---

## 🎯 最后一句话

> `package.json` 是 **现代 JavaScript 项目的“身份证 + 操作手册 + 依赖清单”三位一体的核心文件**。  
> 没有它，npm 就不知道你是谁、用什么、怎么运行。