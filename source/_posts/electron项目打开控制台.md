---
title: electron项目打开控制台
date: 2025-08-13 08:50:50
tags: 工具
categories: 前端
---
## 实现描述
1、使用asar解压程序的asar压缩包 asar e app.asar app
2、在释放的文件中找到主进程入口文件 在package.json的main中为主入口文件，可以对压缩后的代码进行json格式化后在
3、找到创建窗口的代码，在后面添加 [Window].webContents.openDevTools()
4、将代码重新打包  asar p app app.asar
5、重新启动软件即可打开控制台
## 代码示例
let {BrowserWindow} = require('electron')
let timer = null
timer = setInterval(()=>{
let windows = BrowserWindow.getAllWindows()
if(windows.length > 0){
windows.forEach(v =>{
if(v){
v.webContents.openDevTools()
}
})
clearInterval(timer)
}
},5000)

### 防范措施——喜马拉雅
1、监听打开控制台的事件，闪退

### 应对措施
1、在开启前，移除打开控制台事件闪退
v.webContents.removeAllListener('devtools-opened')

