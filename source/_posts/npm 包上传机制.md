---
title: 📚 npm 包上传机制笔记
date: 2025-09-01 10:00:00
updated: 2025-09-02 11:00:00
---
> **核心思想：`npm publish` 就是把本地项目打包并上传到 npm 服务器，别人通过 `package.json.name` 安装和引用。而 `npm pack` 是理解这一过程的“关键钥匙”。**

---

## 1️⃣ 什么是 `npm pack`？

`npm pack` 是一个 **本地打包命令**，它会：

1. 读取当前目录下的 `package.json`
2. 根据配置打包项目文件
3. 生成一个 `.tgz` 压缩包文件（tarball）
4. **不会上传到任何地方**，只在本地生成文件

> 🔧 类比：就像你写了一本书，`npm pack` 相当于把你写的书打包成一个 ZIP 文件，存在自己电脑上，还没寄给出版社。

---

## 2️⃣ `npm pack` 能做什么？（核心用途）

| 用途 | 说明 |
|------|------|
| ✅ **预演发布** | 检查最终发布的内容是否正确 |
| ✅ **验证打包范围** | 看看哪些文件被打包了（有没有误包含 `node_modules`？） |
| ✅ **检查包名和版本** | 确认生成的 `.tgz` 文件名是否符合预期 |
| ✅ **离线分发** | 把 `.tgz` 文件发给同事，他们可以用 `npm install ./xxx.tgz` 安装 |
| ✅ **CI/CD 流程测试** | 在自动化流程中验证包的完整性 |

---

## 3️⃣ `npm pack` 详细执行流程

当你运行：

```bash
npm pack
```

npm 会执行以下步骤：

### 步骤 1：读取 `package.json`
- 获取 `name` → 决定包名
- 获取 `version` → 决定版本
- 获取 `main`, `bin`, `files` 等配置

### 步骤 2：确定要打包的文件
npm 会根据以下规则决定哪些文件进入包中：

| 规则 | 说明 |
|------|------|
| 1. `.npmignore` 文件 | 如果存在，按它列出的规则忽略文件（优先级最高） |
| 2. `package.json` 中的 `files` 字段 | 显式声明要包含的文件/目录 |
| 3. 默认规则 | 除了以下文件，其他都打包：<br>• `node_modules`<br>• `.git`<br>• `npm-debug.log`<br>• `.env`<br>• `*.log`<br>• `*.tgz` |

> 💡 建议：使用 `files` 字段精确控制打包内容，避免误传敏感文件。

### 步骤 3：生成 `.tgz` 文件
- 文件名格式：`<name>-<version>.tgz`
- 例如：`my-utils-1.0.0.tgz`
- 生成在当前目录

### 步骤 4：输出打包详情
```bash
npm notice 
npm notice 📦  my-utils@1.0.0
npm notice === Tarball Contents === 
npm notice 234B  package.json
npm notice 88B   index.js
npm notice 45B   lib/helper.js
npm notice === Tarball Details === 
npm notice name:      my-utils
npm notice version:   1.0.0
npm notice filename:  my-utils-1.0.0.tgz
npm notice total files: 3
npm notice 
my-utils-1.0.0.tgz
```

---

## 4️⃣ 实战：使用 `npm pack` 验证你的包

### ① 先“干跑”查看会打包哪些文件
```bash
npm pack --dry-run
```
👉 输出打包内容列表，但不生成 `.tgz` 文件，安全预览。

### ② 正式打包
```bash
npm pack
```
👉 生成 `my-utils-1.0.0.tgz`

### ③ 解压查看内容（验证真相）
```bash
mkdir unpacked
tar -xzf my-utils-1.0.0.tgz -C unpacked
ls unpacked/package
# 输出：package.json  index.js  lib/
```
✅ 看见了吧？包的内容就是你项目里的文件！

---

## 5️⃣ `npm pack` 与 `npm publish` 的关系

| 对比项 | `npm pack` | `npm publish` |
|--------|-----------|---------------|
| 是否生成 `.tgz` | ✅ 是 | ✅ 是（在服务器） |
| 是否上传 | ❌ 否 | ✅ 是 |
| 是否需要登录 | ❌ 否 | ✅ 是（`npm login`） |
| 是否影响他人 | ❌ 否 | ✅ 是（全球可安装） |
| 是否修改本地文件 | ❌ 否 | ❌ 否 |
| **核心作用** | 🔍 **预演、验证、调试** | 🚀 **正式发布** |

> ✅ **结论：`npm publish` = `npm pack` 的内容 + 上传到 npm 服务器**

---

## 6️⃣ 使用建议：发布前必做

```bash
# 1. 干跑，查看打包内容
npm pack --dry-run

# 2. 正式打包，生成 .tgz
npm pack

# 3. 检查生成的文件名
ls *.tgz

# 4. （可选）解压验证
tar -tzf my-utils-1.0.0.tgz

# 5. 确认无误后发布
npm publish
```

---


