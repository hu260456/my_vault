- 立体呈现
	- 用途：设置元素的子元素是位于 ==3D空间== 还是 ==平面== 中
	- 属性取值
		- flat（子级位于平面）
		- presrved-3d（子级位于 3d空间）
	- 步骤
		1. 给父元素添加 transform-style 属性
		2. 自绝父相，子级可以钉在 top:0 left:0 处
		3. 调整子元素动画末的位置，以及过渡属性

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>立体呈现</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            list-style: none;
        }

        .nav {
            margin: 5rem auto;
            width: 200px
        }

        .nav ul {
            display: flex;
        }

        .nav li {
            position: relative;
            width: 100px;
            height: 40px;
            line-height: 40px;
            transition: all 0.5s;
            transform-style: preserve-3d;
        }

        .nav li a {
            position: absolute;
            left: 0;
            right: 0;
            display: block;
            width: 100%;
            height: 100%;
            text-align: center;
            text-decoration: none;
            color: #fff;
        }

        .nav li a:first-child {
            background-color: green;
            transform: translateZ(20px);
        }

        .nav li a:last-child {
            background-color: orange;
            transform: rotateX(90deg) translateZ(20px);
        }

        .nav li:hover {
            transform: rotateX(-90deg);
        }
    </style>
</head>

<body>
    <div class="nav">
        <ul>
            <li>
                <a href="#">首页</a>
                <a href="#">Index</a>
            </li>
            <li>
                <a href="#">登录</a>
                <a href="#">Login</a>
            </li>
            <li>
                <a href="#">注册</a>
                <a href="#">Register</a>
            </li>
        </ul>
    </div>
</body>

</html>
```