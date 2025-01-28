#### 属性继承性

- 子级默认继承父级的文字控制属性
- 子级只继承自身不存在的文字控制属性

#### 属性层叠性

```css
div {...}
div {...}
/* 相同的属性覆盖，即后面的属性覆盖前面的属性，不相同的属性的叠加 */
```

#### 选择器

```css
/* 通配符选择器，一般用于清除网页的默认样式，例如默认的内外边距 */
* {}

/* 后代选择器，选中某种元素的某种所有后代（子孙）*/
父选择器 子选择器 {}

/* 子代选择器，在一级子代中选中某种元素 */
父选择器 > 子选择器 {}

/* 并集选择器，css 代码 同时对所有选择器生效 */
选择器1,
选择器2,
... {}

/* 交集选择器，选中同时满足所有选择器的元素 */
选择器1选择器2 {}

/* 伪类选择器，选中选择器选中且满足 hover 状态的元素 */
/* 不同元素支持不同的伪类状态 */
/* 超链接支持：link(访问前), visited(访问后), hover(鼠标悬停), active(点击时) */
/* 超链接四个状态都设置时，需要按照上述顺序书写 */
选择器:hover {}

/* 结构伪类选择器，根据标签的结构关系查找元素 */
E:first-child {}
E:last-child {}
E:nth-child(6) {}  /* 从 1 开始 */
/*
2n: 偶数
2n+1, 2n-1: 奇数
5n: 5倍数的标签
n+5: 5和以后的
-n+5: 5和以前的
*/

/* 伪元素选择器 */
/* 创建虚拟标签（伪元素），用来摆放装饰性的内容 */
/* 在 E 元素里面的最前面添加一个伪元素 */
E::before {}
/* 在 E 元素里面的最后面添加一个伪元素 */
E::after {}
/* 也可以只使用一个冒号 */
/*
- 必须设置 content 属性，用来设置伪元素的内容，如果没有内容，则引号内留空
- 伪元素默认是 inline 显示模式
- 权重和 E 相同
*/
```

#### 选择器特定性（选择器优先级）

- 一般而言，选择器选择范围越大，其优先级越低
	- * < 标签选择器 < 类选择器 < id选择器 < 内联样式 < !important
- 权重计算规则
	- （行内样式，id选择器个数，类选择器个数，标签选择器个数）
	- 从左向右依次比较选个数，同一级个数多的优先级高
	- !important 权重最高
	- 继承权重最低

#### 文字控制属性

- font-size: 文字大小
- font-weight: 文字粗细, 400=normal, 700=bold
- font-style: 文字倾斜, normal/italic
- line-height: 行高
	- line-height: 30px, 30px 行高
	- line-height: 2, 2*font-size 行高
- font-family: 字体簇
	- 一般用 ==`sans-serif`== 垫底，表示找不到字体时使用某种==无衬线字体==
- font: 字体复合属性
	- `[倾斜] [加粗] <字号>/[行高] <字体>`
- text-indent: 首行缩进，中文通常缩进 2em 个单位
- text-align: 水平对齐方式, left, center, right
- text-decoration: 文字修饰线, none, underline, line-through, overline
- color: 文字颜色
	- COLOR 常量
	- rgb(r, g, b)
	- rgba(r, g, b, a)
	- `#RRGGBB` 等同于 `#RGB`

#### 背景相关属性

|          属性           |   描述    |                 取值备注                  |                     备注                     |
| :-------------------: | :-----: | :-----------------------------------: | :----------------------------------------: |
|   background-color    |   背景色   |                                       |                                            |
|   background-image    |   背景图   |               url(...)                |                                            |
|   background-repeat   | 背景图平铺方式 | no-repeat, repeat, repeat-x, repeat-y |               默认是 repeat 效果                |
|  background-position  |  背景图位置  |          <x方向> <y方向>，值为英文，px          | 0 0 代表左上角，是默认值；英文书写时可以颠倒顺序；只写一个方向另一个方向默认居中 |
|    background-size    |  背景图缩放  |       百分比，英文(cover, contain)，px       | cover 等于 100%；==cover：图片宽度和盒子一样，高度按比例缩放==  |
| background-attachment |  背景图固定  |               ==fixed==               |              ==背景不随着元素的内容滚动==              |
|      background       | 背景复合属性  |        bgc bgi bgr bgp/bgs bga        |            空格隔开各个属性值，==不区分顺序==             |

#### 标签的显示模式

- 三种显示模式
	- 块元素
		- 独占一行
		- 默认宽度继承父亲
		- 可以设置宽高
		- 示例
			- div
	- 行内元素
		- 不独占一行
		- 尺寸由内容撑开
		- 不可以设置宽高
		- 示例
			- span
	- 行内块元素
		- 不独占一行
		- 尺寸由内容撑开，但是可以设置宽高
		- 示例
			- img
- 转化显示模式
	- display: inline | block | inline-block

#### 盒子模型

- 盒子的组成部分
	- content: width and height
	- padding: 内边距
		- ==会将盒子撑大==
	- border: 边框线
		- ==会将盒子撑大==
		- 复合属性 -> <粗细> <样式> <颜色> (书写不区分顺序)
	- margin: 外边距
		- ==属于盒子外面==
- 盒子的默认尺寸
	- 盒子尺寸 = 内容 + border + padding
- box-sizing 属性
	- ==border-box: padding 和 border 不撑大盒子，而是向内延伸==
	- content-box: 默认属性

#### 标准流（文档流）

> 标准流指的是标签在页面中默认的排布规则，例如：块元素独占一行，行内元素可以一行显示多个。

标准流无法满足特定的需求时，可以采用==浮动==或==flex布局==

#### 浮动（float)

```css
/*
作用：块元素水平排列。
- 浮动后的盒子会顶对齐
- 浮动后的盒子变为行内块显示模式
- 父亲宽度不够，浮动的子元素会换行
- 浮动后的盒子会脱离标准流，不再占用标准流的位置
- 清除浮动影响的方法
- 1. 父元素内容最后添加 <div class="clearfix"></div>, .clearfix { clear: both; }
- 2. 单伪元素法，父级添加类 clearfix, 设置 css:
.clearfix::after {
	content: "";
	display: block;
	clear: both;
}
- 3. 双伪元素法
.clearfix::before,
.clearfix::after {
	content: "";
	display: table;
}
.clearfix::after {
	clear: both;
}
- 4. 父元素添加 overflow: hidden
*/
```

#### Flex 布局

> Flex 布局也叫弹性布局，是浏览器提倡的布局模型；Flex 模型不会产生浮动布局中脱离标准流现象，布局网页更简单、更灵活。

```css
.father {
	/*
	将此元素设置为弹性容器，它的孩子变成弹性盒子
	主轴：默认x方向，侧轴：默认y方向
	弹性盒子是按照主轴方向排列的
	弹性盒子会自动挤压、拉伸，默认情况下所有弹性盒子都在一行（列）显示
	*/
	display: flex;

	/*
	修改主轴的方向，=row(默认)|column|row-reverse|column-reverse
	*/
	flex-direction: column;

	/* 取消父元素宽度不够时默认的挤压拉伸，而是换行显示，默认 nowrap */
	flex-wrap: wrap;

	/*
	主轴对齐方式
	*/
	justify-content: space-between;

	/*
	侧轴对齐方式（对弹性容器设置）
	*/
	align-items: center;
	...

	/* 多行之间的对齐方式 */
	align-content: space-around;
}
.son:nth-child(5n) {
	/*
	侧轴对齐方式（对某些弹性盒子单独设置）
	*/
	align-self: stretch;
	...
}
/* 弹性伸缩比 */
.son:nth-child(2) {
	flex: 1; /* 表示在 主轴 上占用剩余空间的 1 份，即剩余空间的 1/3 */
}
.son:nth-child(3) {
	flex: 2; /* 表示在 主轴 上占用剩余空间的 1 份，即剩余空间的 2/3 */
}
```

#### 定位

```css
/* 相对定位：
不脱离标准流，占有的空间不改变
标签的显示模式不改变
*/
div {
	position: relative;
	top: 50px;
}

/* 相对父亲的绝对定位（默认是相对 body）: 
脱离标准流，不占位
son 的显示模式变为行内块
*/
.father {
	position: relative;
}
.son {
	position: absolute;
	top: 0;
	right: 0;
}

/* 固定定位（相对视窗），注意区分相对视窗与相对 body：
脱离标准流，不占位
div 变为行内块元素
*/
.div {
	position: fixed;
	right: 50px;
}
```

#### Z-index

对于脱离标准流的元素，浏览器根据标签的书写顺序，后写的堆在先写的元素上面
可以手动设置 z-index，设置这些元素的层级关系

#### 字体图标

- 展示的是图标，本质是字体
- 需要引入样式
- [iconfont - 阿里图标库](https://www.iconfont.cn)
- 可以上传 svg矢量图 到 iconfont，生成字体图标

#### Transition

```css
/* transition: <需要过渡的属性> <过渡的时间> */
div {
	height: 300px;
	width: 300px;
	transition: all 1s;
}
div:hover {
	height: 400px;
	width: 400px;
}
```

#### Transform 平面转换（2D转换）

```css
/* 平面转换：
作用：为元素添加动态效果，一般与过渡配合使用
概念：改变盒子在平面内的形态(位移、旋转、缩放、倾斜)
*/
div:hover {
	/* 平移
	单位：px，百分比（相对盒子自身的尺寸）
	+-均有意义
	可以单独设置 translateX 或 translateY
	*/
	transform: translate(X距离, Y距离);

	/* 旋转
	单位是 deg
	+-均有意义
	*/
	transform: rotate(旋转角度);

	/* 多重转换
	旋转会改变坐标轴向，所以 先旋转后平移 和 先平移后旋转是不同的效果
	多重转换不可以拆开写，是复合属性
	*/
	transform: translate(...) rotate(...);

	/* 缩放 */
	transform: scale(缩放倍数);
	transform: scale(X轴缩放倍数, Y轴缩放倍数);

	/* 倾斜
	单位：deg
	*/
	transform: skew(ndeg);

	/* 渐变
	渐变方向取值：to right | 45deg
	终点位置单位：%
	*/
	/* 线性渐变 */
	background-image: linear-gradient(
		[渐变方向],
		COLOR1 [终点位置],
		COLOR2 [终点位置], ...
	);
	/* 线性渐变 - 透明背景 */
	background-image: linear-gradient(
		transparent,
		rbga(...)
	);
	/* 径向渐变
	圆心位置取值：px | % | 方位
	*/
	background-image: radial-gradient(
		半径[, 半径(椭圆)] at 圆心位置
		COLOR1 [终点位置],
		COLOR2 [终点位置], ...
	);
	/* 径向渐变 - 透明背景 */
	background-image: radial-gradient(
		半径[, 半径(椭圆)] at 圆心位置
		rgba(...),
		transparent
	);
}

img {
	/* 改变旋转原点 */
	transform-origin: right bottom;
}
```

#### 空间转换 （3D 转换）

```css
div:hover {
	/* 必须写全三个轴 */
	transform: translate3d(X距离, Y距离, Z距离);
	/* 单独写一个轴 */
	transform: translateX(X距离);
	transform: translateY(Y距离);
	transform: translateZ(Z距离);

	/*
		旋转
		旋转方向与参数正负性符合 左手法则
		给父亲添加视距可以获得更加立体的效果
	*/
	/* 沿着某个轴旋转 */
	transform: rotateX(ndeg);
	transform: rotateY(ndeg);
	transform: rotateZ(ndeg);
	/* 扩展 x, y, z 取 0-1 */
	transform: rotate3d(x, y, z, ndeg);

	/* 缩放 */
	transform: scaleX(倍数);
	transform: scaleY(倍数);
	transform: scaleZ(倍数);
	transform: scale3d(x倍数, y倍数, z倍数);
}
div {
	/* 视距 perspective
		作用：指定了观察者与 Z=0 平面的距离，为元素添加透视效果
		透视效果：近大远小、近实远虚
		视距是添加给直接父标签的，取值范围 800-1200px
	*/
	perspective: 1000;
}
.div_son {
	transform: translateZ(300px);
}
```

- [立体呈现](demo/立体呈现_html.md)

#### 动画

```css
/* 实现步骤 */

/* 1. 定义动画 */
/* from-to 版 */
@keyframes 动画名称 {
	from {}
	to {}
}
/* 精细版 */
@keyframes 动画名称 {
	0% {}
	10% {}
	100% {}
}

/* 2. 使用动画 */
div {
	/* 属性取值不分先后顺序
	速度曲线: linear(匀速), steps(n)(分步完成)
	延迟时间：等多久开始动画
	重复次数：n(重复播放n次), infinite(循环播放)
	动画方向：alternate(多一个反向播放周期)
	完毕状态：fowards(停在结束状态), =backwards(停在初始状态)
	*/
	animation: <动画名称> <动画时长> [速度曲线] [延迟时间] [重复次数] [动画方向] [完毕状态];
	animation-name: ...;  /* 动画名称 */
	animation-duration: ...;  /* 动画时长 */
	animation-timing-function: ...;  /* 速度曲线 */
	animation-delay: ...;  /* 延迟时间 */
	animation-iteration-count: ...;  /* 重复次数 */
	animation-direction: ...;  /* 动画方向 */
	animation-fill-mode: ...;  /* 完毕状态 */
	animation-play-state: paused;  /* paused 为暂停动画，常配合 hover 使用 */
}
```

#### CSS 移动端

##### 适配方案

- 宽度适配（宽度自适应）（一般只用于 PC 端适配）
	- 宽度百分比 + flex 布局
- 等比适配（宽高等比缩放）（可用于移动端适配）
	- rem
	- vw | vh

```html
<!-- 使用视口标签 -->
<!-- 作用：令视口宽度=设备宽度(逻辑分辨率的宽度)，缩放1倍（不缩放） -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

##### rem

> 1rem 等于 HTML 标签下的 font-size

- 常将视口宽度等分成 10 份，1rem 设置为 1 份
- 使用媒体查询来为不同设备应用不同 css 代码
- ==可以直接导入 flexible.js 来完成上述工作==

##### vw | vh

- vw
	- 1vw = 0.01 viewport width
- vh
	- 1vh = 0.01 viewport height

==vw, vh 一般不混用，切换宽高比不同的设备会导致盒子变形==

##### 二倍图

- 现阶段设计稿一般参考 iPhone6/7/8（设备宽度 375px）产出设计稿
- 为了更好的显示效果，设计稿一般给出二倍图（宽图 750px）
- ==如果使用像素大厨等软件，需要进行设置（像素密度调整为二倍）才能给出正确数据==

#### 媒体查询

```css
/* 语法 1 */
@media (媒体特性) {
	选择器 {
		...
	}
}
/* 语法 1 */
@media [逻辑符] [媒体类型] and (媒体特性) {
	选择器 {
		...
	}
}

/* 媒体查询在意书写顺序
max-xxx: 应该从大到小写
min-xxx: 应该从小到大写
*/

/* 媒体类型
screen: 屏幕，默认
print: 打印预览
speech: 阅读器
all: 所有
*/

/* 媒体特性
width, height
max-width, max-height
min-width, min-height
orientation: potrait(竖屏), landscape(横屏)
*/

/* 媒体查询 - 使用外部 css
<link rel="stylesheet" media="[逻辑符] [媒体类型] and (媒体特性)" href="xxx.css">
*/
```

#### 常用属性
##### opacity

用于设置整个元素的不透明度（包含背景和内容），rgba 只能设置背景的不透明度

##### cursor

用于设置悬停在元素上的指针样式
cursor: default|pointer|text|move

#### 现象 - 问题

##### 外边距合并现象

两个垂直排列的兄弟元素，上下 margin 会合并，最终 margin 取两 margin 的最大值

##### 外边距塌陷问题

```css
/* 现象：子级添加上外边距时，导致父亲向下移动 */
.son {
	width: 500px;
	height: 500px;
	background-color: blue;
	margin-top: 50px;
}
/* 解决办法：
1. 取消子级 margin，在父级设置padding
2. 父亲设置 overflow: hidden
3. 父亲设置 border-top（副作用：撑大盒子）
*/
```

##### 行内元素的边距问题

==行内元素添加 margin 和 padding 无法改变元素垂直位置==
==给行内元素添加 line-height 可以改变垂直位置==

#### 效果

##### 文字垂直居中

- 让行高等于盒子高度可以达到文件垂直居中的效果
- 只适用于单行文字

##### 版心居中

- margin: 0 auto;
- ==以上代码需要盒子有宽度才能生效，因为使用了 auto，浏览器要根据宽度计算左右外边距==

##### 清除默认样式

```css
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;  /* 可选的开发习惯，内容 + 内边距 + 边框 == 宽度 */
}
li {
	list-style: none;  /* 清除 li 前的小点 */
}
a {
	text-decoration: none;
}
```

##### 盒子阴影

```css
div {
	box-shadow: <X偏移量> <Y偏移量> [模糊半径] [扩散半径] [阴影颜色] [内外阴影]
}
```

##### 登录页、通知页面居中

```css
/* 假设 img 是一个要居中的页面 */
img {
	position: absolute;
	left: 50%;
	top: 50%;
	transform: translate(-50%, -50%);
}
```

##### 对齐：垂直对齐 | 基线对齐

```html
<html>
<head>
	<style>
		/* vertical-align 应该给 div 中最高的元素添加 */
		div img {
			vertical-align: baseline（默认，基线对齐）| top | middle | bottom;
		}
	<style>
</head>
<body>
	<div>
		<img src="..."/>
		一行文字...
	</div>
</body>
</html>
```

##### favicon 网页图标

```html
<!-- emmet = link:favicon -->
<link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
```