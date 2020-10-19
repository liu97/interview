### 水平垂直居中
1. 已知元素宽高，使用position百分比+margin负数
```css
.example {
  width: 100px;
  height: 100px;
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -50px;
  margin-top: -50px;
}
```

2. 不知元素宽高，使用position百分比+translate负百分比
```css
.example {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%); /*translate百分比是根据自身宽度作为依据*/
}
```

3. margin: auto; + position
.example {
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  margin: auto;
}
```

4. flex布局
```css
.warp {
  display: flex;
  align-items: center;
  justify-content: center;
}
.example{
  /*
  * ...
  */
}
```

### 盒子模型
盒子模型分为标准盒子模型和IE盒子模型，模型内容包括：margin、border、padding、content部分。标准盒子模型width和height只代表content部分，erIE盒子模型width和height包括border+padding+content。大部分情况下IE盒子模型比标准盒子模型更好用。两种模型切换方法：
```css
box-sizing: border-box; /*IE盒子模型样式*/
box-sizing: content-box; /*标准盒子模型样式*/
```

### BFC
BFC指的是块级格式化上下文，一个元素形成了BFC之后，那么它内部元素产生的布局不会影响到外部元素，外部元素的布局也不会影响到BFC中的内部元素。一个BFC就像是一个隔离区域，和其他区域互不影响。

BFC布局规则：
* 内部的Box会在垂直方向，一个接一个地放置。
* Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
* 每个元素的左外边缘（margin-left)， 与包含块的左边（contain box left）相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。除非这个元素自己形成了一个新的BFC。
* BFC的区域不会与float box重叠。
* BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
* 计算BFC的高度时，浮动元素也参与计算

触发BFC条件：
* 根元素（<html>）
* 浮动元素（元素的float不是none）
* 绝对定位元素（元素的position为fixed或absolute）
* 行内块元素（display为inline-block）
* overflow值不为visible
* display为flex的直接子元素

BFC的一些作用：
1. 自适应两栏布局
```html
<style>
.div1{
  width: 200px;
  height: 20px;
  background-color: pink;
  float: left;
}
.div2{
  height: 20px;
  background-color: green;
  overflow: hidden;
}
</style>
<body>
  <div class="div1"></div>
  <div class="div2"></div>
</body>
```

2. 清除内部浮动
```html
<style>
  .div-wrap{
    overflow: hidden;
  }
  .div1{
    width: 100px;
    height: 100px;
    background-color: pink;
    float: left;
  }
  .div2{
    width: 100px;
    height: 100px;
    background-color: green;
    float: left;
  }
</style>
<body>
  <div class="div-wrap">
		<div class="div1"></div>
		<div class="div2"></div>
	</div>
	<div>这个内容不会浮上去，且.div-wrapper的高度不会塌陷为最大子元素高度</div>
</body>
```

3. 防止margin重叠
```html
<style>
  .div1{
    width: 100px;
    height: 100px;
    background-color: pink;
    margin-bottom: 10px;
  }
  .div2{
    width: 100px;
    height: 100px;
    background-color: green;
    margin-top: 10px;
  }
  .div-wrapper{
    overflow: hidden;
  }
</style>
<body>
  <div class="div1"></div>
	<div class="div-wrapper">
		<div class="div2">用BFC包裹起来，就不会出现div1和div2的margin叠加问题</div>
	</div>
</body>
```

4. 定位
position有五个属性：static（默认）、relative、absolute、fixed、sticky。
* relative
相对定位，被设置元素不脱离文档流，定位基点相对未添加定位时的位置定位
* absolute
绝对定位，被设置元素脱离文档流不占位置，定位基点为第一个设置了不为static的定位祖先元素
* fixed
固定定位(or绝对定位)，被设置元素脱离文档流不占位置，会根据屏幕视口来定位，需要注意的是当transform，filter属性非none时，定位由是视口改为改祖先元素
* sticky
粘性定位，被认为是相对定位和固定定位的混合，当页面滚动，度元素开始脱离视口时，只要达到sticky设置的生效门槛(top、left等)，relative就会切换为fixed，等父元素完全脱离视口时，fixed自动切换回relative定位