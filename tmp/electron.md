## 构建项目

`npm i electron -D`

**指明入口**

`"main": "./main.js"`

**配合 nodemon**

`"start": "nodemon --exec electron . --watch ./ --ext .js,.html,.css"`

```JavaScript
const { app, BrowserWindow } = require('electron')
const createWindow = () => {
    const win = new BrowserWindow({
        show: false,
        width: 1000,
        height: 80,
        x: 100,
        y: 100,
        backgroundColor: 'purple',
        frame: false
    })
    win.loadFile('index.html')
    // win.loadURL('https://www.bing.com')
    // 页面加载完成再显示页面
    win.on('ready-to-show', () => { win.show() })
}
app.on('ready', () => { createWindow })
app.whenReady().then(createWindow)
```

## 主进程与渲染进程

```Plain Text
项目启动时运行的main.js就是主进程，主进程只有一个
主进程使用BrowerWindow创建实例，主进程销毁后，对应的渲染进程也被销毁
主进程与渲染进程之间通过IPC通信
```

## 主进程生命周期

```JavaScript
// ready: app初始化完成
// did-finish-load: 导航完成时触发
// window-all-closed
// before-quit: 在关闭窗口前触发
// will-quit: 在窗口关闭并且应用退出时触发
// quit: 所有窗口被关闭时触发
// browser-window-blur: 窗口失去焦点时触发
// browser-window-focus: 窗口获得焦点时触发
// DOM加载完成后
win.webContents.on('did-finish-load', () => {})
// DOM加载前
win.webContents.on('dom-ready', () => {})
// new-window: 打开了新窗口时触发
```

## 集成NodeJS

```JavaScript
////////// 直接在主进程集成NodeJS //////////
const win = new BrowserWindow({
    webPreferences: {
        nodeIntegration: true,
        contextIsolation: false
    }
})
////////// 渲染进程集成NodeJS //////////
webPreferences: {
    preload: path.resolve(__dirname, 'preload.js'),
    sandbox: false // 新版electron有了sandbox隔离机制
}
```

## contextBridge

```JavaScript
////////// 在preload.js中 //////////
const { contextBridge } = require('electron')
contextBridge.exposeInMainWorld('myAPI', {
    // 在此封装“myAPI”
})
////////// 在app.js中 //////////
const xxx = window.myAPI.xxx
```

## 保存窗口状态

`npm i electron-win-state -D`

```JavaScript
////////// 在main.js中
const WinState = require('electron-win-state').default
const winState = new WinState({
    defaultWidth: 800,
    defaultHeight: 600
})
const createWindow = () => {
    const win = new BrowserWindow({
        ...winState.winOptions
        // 使用此模块则不能为win添加宽、高、x、y
    })
    winState.mange(win)
}
```

## 菜单

```JavaScript
////////// 主菜单 //////////
const { Menu, MenuItem } = require('electron')
const mainMenu = Menu.buildFromTemplate([
    { // 定义一个无动作的选项
        label: 'main',
        submenu: [
            {
                label: 'submenu1'
            }
        ]
    },
    { // 定义一个预定义的选项
        label: 'edit',
        submenu: [
            {role: 'toggleDevTools'} // undo、redo、copy、paste等
        ]
    },
    { // 自定义一个选项
        label: '向后端问好',
        click: () => { console.log('你好') },
        accelerator: 'Alt+H' // 定义此项选项的快捷键
    }
])
// 在createWindow函数中
Menu.setApplicationMenu(mainMenu)
////////// 上下文菜单 //////////
// 类似创建主菜单，创建一个contextMenu对象
win.webContents.on('context-menu', (e, params) => {
    contextMenu.popup()
})
```
## 对话框

```JavaScript
////////// 在主进程中 //////////
const { dialog } = require('electron')
// 选择文件对话框
dialog.showOpenDialog({
    buttonLabel: 'submit', // 确认按钮的名字
    defaultPath: app.getPath('desktop'),
    properties:
        ['multiSelections', 'createDirectory', 'openFile', 'openDirectory']
}).then(result => {
    console.log(result.filepaths)
})
// 保存文件对话框
dialog.showSaveDialog({}).then(result => { console.log(result.filepaths) })
// 消息对话框
const options = ['yes', 'no', 'not sure']
dialog.showMessageBox({
    title: 'message box',
    message: 'please select an option',
    detail: 'message details',
    buttons: options
}).then(({response}) => {
    console.log(`user selected ${options[response]}`);
})
```
## 快捷键

```JavaScript
////////// 在主进程中 //////////
// 注册一个快捷键
const { globalShortcut } = require('electron')
globalShortcut.register('CommandOrControl+G', () => {
    console.log('client pressed key "G" with cmd or ctrl')
})
```
## 托盘

```JavaScript
////////// tray.js //////////
const { Tray } = require('electron')
module.exports = (win) => {
    const tray = new Tray('./trayImage.png')
    tray.setToolTip('我的应用')
    // 托盘事件
    tray.on('click', (e) => {
        win.isVisible() ? win.hide() : win.show()
    })
    // 托盘菜单
    tray.setContextMenu(Menu.buildFromTemplate([
        {
            label: '...'
        }
    ]))
}
////////// createWindow中 //////////
const createTray = require('./tray.js')
createTray(win) // 传入当前窗口的引用
```
## 多窗口

```JavaScript
const win1 = new BrowserWindow({
    width: 800,
    height: 600
})
const win2 =new BrowserWindow({
    width: 500,
    height: 400
    parent: win1, // 设置win1为其父窗口
    modal: true // 设置为模态窗口
})
```
## 开发者工具

```JavaScript
// 打开开发者工具
win.webContents.openDevTools()
// 关闭所有开发者工具警告
process.env['ELECTRON_DISABLE_SECURITY_WARNINGS'] = 'true'
```
## IPC

```JavaScript
////////// 在main.js中 //////////
const { ipcMain } = require('electron')
// 处理名为“send-event”的事件
ipcMain.handle('send-event', (event, msg) => {
    return msg
})
////////// 在页面实例.js中 //////////
document.querySelector('#btn').addEventListener('click', () => {
    window.myAPI.handleSend()
})
////////// 在preload.js //////////
const { ipcRenderer }  = require('electron')
const handleSend = async () => {
    let fallback = await ipcRenderer.invoke('send-event', 'test')
    console.log(fallback);
}
contextBridge.exposeInMainWorld('myAPI', {
    handleSend
})
```
## 其他

```JavaScript
////////// 获取运行时平台
process.platform
////////// 获取桌面、文档、音乐等路径
app.getPath('desktop')
////////// 获取剪切板
const { clipboard } = require('electron')
// 读取剪切板
const text = clipboard.readText()
// 写入剪切板
clipboard.writeText('a piece to copy')
////////// 本地图片
const { nativeImage } = require('electron')
// 创建本地图片的实例
const image = nativeImage.createFromPath('./image.png')
```

