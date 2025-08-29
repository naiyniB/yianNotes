---
title: git的相关命令的解析
---

# git的相关命令的解析

## ✅ **撤回（add）添加的内容**
### 语法
```bash
  git reset [filename]
```
### 📌 作用： 
撤回添加到暂存区文件，不会对工作区的内容产生变化。因为add的时候会将文件转成blob对象，所以这里的reset只是将二进制的对象变成未引用的，具体的内容会由gc处理，reset并不会删除二进制对象。


---

## ✅ **查看当前仓库连接的远程仓库地址**
### 语法
```bash
git remote -v
```
**示例输出**：
```
origin  https://github.com/naiyniB/yian.git (fetch)
origin  https://github.com/naiyniB/yian.git (push)
```

###  📌 作用：  
列出所有远程仓库的名称和 URL（`-v` 表示 verbose，显示详细地址）。

---

## ✅ **查看当前所在的本地分支**
### 语法
```bash
git branch --show-current
```
💡 也可以用：
```bash
git status
```
###  📌 作用：  
直接输出当前分支名，如 `main` 或 `master`。

第一行会显示：`On branch main`

---

### ✅ 3. **查看所有本地分支**

```bash
git branch
```
**示例输出**：
```
  dev
* main
  feature/user-login
```


###  📌 作用：  
列出所有本地分支，当前分支前会有一个 `*` 号。
---

## ✅ **查看远程分支（必须先同步）**

### 第一步：获取最新远程分支信息
#### 语法
```bash
git fetch --all
```
> ⚠️ 如果不先 fetch，可能看不到别人新建的分支。

### 第二步：查看所有远程分支
#### 语法
```bash
git branch -r
```
**示例输出**：
```
  origin/main
  origin/dev
  origin/feature/user-login
  origin/release/v1.0
```
### 📌 作用：  
列出所有远程分支，通常以 `origin/` 开头。

---

## ✅  **查看本地 + 远程所有分支**
### 语法：
```bash
git branch -a
```

### 📌 作用：  
同时显示本地分支和远程分支（带 `remotes/origin/...`）。

---

## ✅**查看远程仓库的详细信息（含分支跟踪关系）**
### 语法：
```bash
git remote show origin
```
### 📌 作用：  
显示远程仓库 `origin` 的详细情况，包括：
- 远程 URL
- 所有远程分支
- 哪些分支已关联本地分支（tracking）
- 哪些分支在远程已被删除

---
