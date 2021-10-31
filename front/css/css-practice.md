## flex

`align-items`属性定义项目在交叉轴上如何对齐

`justify-content`属性定义了项目在主轴上的对齐方式。

`align-content`属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

```css
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```



## 文本溢出

### WebKit浏览器或移动端的页面

在WebKit浏览器或移动端（绝大部分是WebKit内核的浏览器）的页面实现比较简单，可以直接使用WebKit的CSS扩展属性(WebKit是私有属性)`-webkit-line-clamp` ；注意：这是一个 不规范的属性（[unsupported WebKit property](http://developer.apple.com/safari/library/documentation/AppleApplications/Reference/SafariCSSRef/Articles/StandardCSSProperties.html#//apple_ref/doc/uid/TP30001266-UnsupportedProperties)），它没有出现在 CSS 规范草案中。

`-webkit-line-clamp`用来限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他的WebKit属性。查看 `text-overflow` 属性文档：https://www.html.cn/book/css/webkit/text/line-clamp.htm

常见结合属性：

1. `display: -webkit-box;` 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
2. `-webkit-box-orient` 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。
3. `text-overflow: ellipsis;`，可以用来多行文本的情况下，用省略号“…”隐藏超出范围的文本 。查看 `text-overflow` 属性文档：https://www.html.cn/book/css/properties/user-interface/text-overflow.htm

css 代码:

```css
overflow : hidden;
text-overflow: ellipsis;
display: -webkit-box;
-webkit-line-clamp: 1;
-webkit-box-orient: vertical;
```







## 设置圆角的输入框

```html

<style>

.tbradius
{
border-color: #e2e2e0;//设置边框的颜色
border-width: 1px;//设置边框的宽度
border-style: solid;//设置边框的样式，此处为实线
border-radius: 0.4em;//圆角的程度，可以自行测试需要的样式
}

</style>

//文本框

<input type="text" class="tbradius" />
```





# css文件 如何使背景图片大小适应div的大小

**background-size**有3个属性：

auto：当使用该属性的时候，背景图片将保持100% 的大小显示，不进行任何缩放。超过div的多余部分将被隐藏。当图片过小时，图片会自动平铺。这种属性通常用来做重复性的背景或者做半透明图片背景。

cover：当使用该属性时，图片将被缩放至恰好能覆盖div，并且图片被隐藏的部分最少，这种属性在大图背景中应用比较广泛。这点比较难理解，需要结合实践理解。

contain：当使用该属性时，图片被缩放至最大且能被完全展示出来，但是由于图片的的尺寸比例与div的尺寸比例会有不同，所以当图片不能盖住div时，图片会自动平铺。





# 如何让图片保持原比例，占满整个盒子

在项目的过程中，经常遇到客户上传的图片，有些图片的大小和盒子的大小不匹配。我们有时候会采用裁剪图片，有时候会使用最铺满这个盒子类似（background-size:contain）。

给图片设置成max-width:100%;max-height:100%;

具体代码如下：

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
    body{
        margin: 0;
    }
    div{
        width: 200px;
        height: 200px;
        display: table-cell;
        text-align: center;
        vertical-align: middle;
        border: 1px solid #000;
        background-color: #f00;
    }
    img{
        max-width: 100%;
        max-height: 100%;
        vertical-align: middle;
    }
    </style>
</head>
<body>
    <div>
        <img src="1.jpg" alt="">
    </div>
    <div>
        <img src="2.jpg" alt="">
    </div>
    <div>
        <img src="3.jpg" alt="">
    </div>
</body>
</html>
```





## 使用绝对定位添加跟内容对其的下边框

```ｃｓｓ
/*加下边框*/
.row-cell::after{
  position: absolute;
  box-sizing: border-box;
  content: ' ';
  pointer-events: none;
  right: 0.853333rem;
  bottom: 0;
  left: 0.853333rem;
  border-bottom: 0.053333rem solid #ebedf0;
  -webkit-transform: scaleY(.5);
  transform: scaleY(.5);
}
```

