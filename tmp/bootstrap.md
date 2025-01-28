#### 栅格系统

```html
<div class="container">
	<div class="row">
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
		<div class="col-12 col-sm-6 col-md-4 col-xl-3 col-xxl-2"></div>
	</div>
</div>
```

> bootstrap 栅格系统将屏幕宽度分成了 12 份

- 默认: 超小尺寸屏幕
- sm: 小尺寸屏幕
- md: 中等尺寸屏幕
- xl: 大尺寸屏幕
- xxl: 超大尺寸屏幕

代码解释：
	container 表示一个版心居中的盒子；row 表示其中的元素排列成一行，这个盒子是 flex 布局；col-sm-6 表示对于小尺寸屏幕，这类盒子占用 6 份宽度，也就是一行有 12/6=2 个此类盒子