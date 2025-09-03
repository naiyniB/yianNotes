---
title: TypeScript 编译工具 `tsconfig.json`配置项说明
date: 2025-09-03 13:00
---


> tsc作用是 检查类型 和 编译成js . 为了更好地控制编译行为（比如目标 JS 版本、是否严格检查类型等），建议创建一个配置文件。


### target
  - **作用：** 指定编译后的 JavaScript 目标版本。
  - 常见值：
    - "ES3"（最老，兼容性最好，但功能少）
    - "ES5"（经典，兼容所有浏览器）
    - "ES2015" 或 "ES6"（现代浏览器支持）
    - "ES2016"、"ES2017"、"ES2020"、"ESNext"（最新语法）
### module:
  - **作用：** 指定模块系统的代码生成格式。
  - 常见值：
    - "commonjs" → 用于 Node.js（使用 require / module.exports）
    - "es2015" / "esnext" → 用于浏览器（使用 import / export）
    - "amd"、"umd" → 用于老项目或库打包
### strict: true
  - **作用：** 开启所有严格类型检查模式
### outDir:
  - **作用：** 指定编译后生成的 .js 文件输出到哪个文件夹
### rootDir: "./src" 
  - **作用：** 指定源码结构的根目录，用于控制输出时的目录结构 
  - **核心逻辑** 输出结构 = 源文件相对于 rootDir 的路径，不决定是否编译哪些文件
  - include 是指定哪些文件被编译
  - 如果有文件在rootDir之外也会被编译就会报错`TS6059`
### removeComments:true
  - **作用：** 编译时是否移除代码中的注释
### noEmit:false
  - **作用：** 是否生成输出文件：`false` = 正常编译，`true` = 只检查类型
### esModuleInterop:true