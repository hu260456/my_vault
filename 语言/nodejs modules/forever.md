`npm i -g forever`

```Shell
# 为 nodejs 项目开启一个服务，关闭终端后服务器也能对外响应
forever start main.js
# 查看 forever 启动的服务
forever list
# 停止一个服务
forever stop <pid>
# 停止所有服务
forever stopall
```
