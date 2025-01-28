#### 项目结构

- project/settings.py
	本项目的配置项
- project/urls.py
	用来配置项目路由
- project/wsgi.py
	项目与 WSGI 协议兼容的 web 服务器入口，一般无需修改
- templates

- db.sqlite3

- manage.py
	一般无需编辑此文件，通常在终端输入 `python manage.py xxx` 来和项目交互，可以输入 `python manage.py help` 查看可以做什么

#### project 与 app

一个 django project 由若干 app 组成，一个 app 也可被用到不同项目，通过 `python manage.py startapp <app>` 创建一个 app


