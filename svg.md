## Title/ SVG完全指南 #flight.Archives004
> 序:  
> 今天我写一个SVG滤镜教程,实在写不下去了QwQ... 于是赶紧先来一篇SVG指南找找感觉
>
> 简介:  
> SVG是一种基于XML语法的文本文档, 全称是可缩放矢量图(Scalable Vector Graphics)  
> 
> 其他的图片格式都是基于像素处理的, 而SVG则是对图像形状的描述, 具有这两个特点:  
> 1. 体积小(文本文件) 
> 2. 放大不失真

### Tag/SVG介绍
SVG 文件可以直接嵌入网页, 然后用 JavaScript 和 CSS 进行操作.
```html
<!DOCTYPE html>
<html>
<head></head>
<body>
<svg id="mysvg" viewBox="0 0 800 600">
  <circle id="mycircle" cx="400" cy="300" r="50" />
</svg>
</body>
</html>
```
↑ 这样就创建了一个SVG!

SVG 代码也可以写在一个独立文件中, 然后用`<img>`, `<object>`, `<embed>`, `<iframe>`等标签插入网页.

```html
<img src="circle.svg">
<object id="object" data="circle.svg" type="image/svg+xml"></object>
<embed id="embed" src="icon.svg" type="image/svg+xml">
<iframe id="iframe" src="icon.svg"></iframe>
```

CSS 也可以使用 SVG 文件.

```css
.logo {
background: url(logo.svg);
}
```

SVG 文件还可以转为 BASE64 编码，然后作为 Data URI 写入网页。

```html
<img src="data:image/svg+xml;base64,[data]">
```

### Tag/ 语法
0. 通用属性

   属性 | 可取值 | 作用
   --- | --- | ---
   `fill` | `url(<selector>)` | 指定一个形状的填充
 
1. `<svg>` 标签
   
   属性 | 可取值 | 作用
   --- | --- | ---
   `width`, `height` | `<length>` | 指定SVG的宽和高
   `viewBox` | 4个`<number>` | 四个数字分别对应SVG左上角的横纵坐标, 视口的宽高. 如果视口的宽高小于SVG的`width`和`height`,则最终结果会拉伸以填满SVG的大小
   
2. `<svg>` - `<circle>` 标签

   属性 | 可取值 | 作用
   --- | --- | ---
   `cx` | `<number>` | 指定圆的横坐标
   `cy` | `<number>` | 指定圆的纵坐标 
   `r` | `<length>` | 指定圆的半径

3. `<svg>` - `<line>` 标签

   属性 | 可取值 | 作用
   --- | --- | ---
   `x1` | `<number>` | 指定起点的横坐标
   `y1` | `<number>` | 指定起点的纵坐标
   `x2` | `<number>` | 指定终点的横坐标
   `y2` | `<number>` | 指定终点的纵坐标
   
4. `<svg>` - `<polyline>` 标签

   属性 | 可取值 | 作用
   --- | --- | ---
   `points` | `<number>,<number> <number>,<number>...` | 指定折线每一个点的坐标, 横纵坐标之间用逗号分隔, 点与点之间用空格分隔
   
5. `<svg>` - `<rect>` 标签

   属性 | 可取值 | 作用
   --- | --- | ---
   `x` | `<number>` | 指定矩形的横坐标
   `y` | `<number>` | 指定矩形的纵坐标
   `width`, `height` | `<length>` | 指定矩形的宽高
   
6. `<svg>` - `<ellipse>` 标签

   属性 | 可取值 | 作用
   --- | --- | ---
   `cx` | `<number>` | 指定椭圆的横坐标
   `cy` | `<number>` | 指定椭圆的纵坐标
   `rx` | `<length>` | 指定椭圆的横向半径
   `ry` | `<length>` | 指定椭圆的纵向半径

7. `<svg>` - `<polygon>` 标签

   属性 | 可取值 | 作用
   --- | --- | ---
   `points` | `<number>,<number> <number>,<number>...` | 指定多边形每一个点的坐标, 横纵坐标之间用逗号分隔, 点与点之间用空格分隔
   
8. `<svg>` - `<path>` 标签

   属性 | 可取值 | 作用
   --- | --- | ---
   `d` | `string` | 指定路径每一个点的坐标
    
   `string` 的值是一个长字符串, 每个字母表示一种绘制动作, 后面跟着坐标:
   
   ```  
   M 1,2
   L 3,20
   L 12,40
   Z
   ```
   
   `M` 表示移动(moveto), `L` 表示画直线 (lineto), `Z` 表示闭合路径


9. `<svg>` - `<text>` 标签

   属性 | 可取值 | 作用
   --- | --- | ---
   `x` | `<number>` | 指定文本的横坐标
   `y` | `<number>` | 指定文本的纵坐标
   
    文字定义在 `<text>` 标签内.

   
10. `<svg>` - `<use>` 标签

    属性 | 可取值 | 作用
    --- | --- | ---
    `href` | `<selector>` | 指定要复制的节点
    `x` | `<number>` | 指定复制内容的横坐标
    `y` | `<number>` | 指定复制内容的纵坐标
   
11. `<svg>` - `<g>` 标签

    `<g>` 标签将多个形状组成一个组(group), 方便复用
    
    属性 | 可取值 | 作用
    --- | --- | ---
    `cx` | `<number>` | 指定圆的横坐标
    `cy` | `<number>` | 指定圆的纵坐标
    `r` | `<number>` | 指定圆的半径

12. `<svg>` - `<defs>` 标签
    
    `<defs>` 标签用于自定义形状, 它内部的代码不会显示, 仅供引用

   
13. `<svg>` - `<pattern>` 标签

    `<pattern>` 标签用于自定义一个图案, 该图案可以被引用来平铺一个区域

    
14. `<svg>` - `<image>` 标签

    属性 | 可取值 | 作用
    --- | --- | ---
    `xlink:href` | `<url>` | 指定图片的来源

15. `<svg>` - `<animate>` 标签

    属性 | 可取值 | 作用
    --- | --- | ---
    `attributeName` | `<string>` | 指定发生动画的属性名
    `from` | 属性值 | 指定单次动画的初始值
    `to` | 属性值 | 指定单次动画的结束值
    `dur` | `<time>` | 指定动画的时长
    `repeatCount` | `<number>` | 指定动画的执行次数, `indefinite`为无限循环

    `<animate>` 标签定义在元素内部.


16. `<svg>` - `<animateTransform>` 标签

    属性 | 可取值 | 作用
    --- | --- | ---
    `attributeName` | `<string>` | 指定发生动画的属性名
    `from` | 属性值 | 指定单次动画的初始值
    `to` | 属性值 | 指定单次动画的结束值
    `dur` | `<time>` | 指定动画的时长
    `repeatCount` | `<number>` | 指定动画的执行次数, `indefinite`为无限循环

    `<animate>` 标签定义在元素内部, 定义元素的 `transform` 属性 

### Tag/ CSS属性
> 见MDN

### ->> Details
#### -

<!--
### ->> flight.frontendBeautiful
> 待添加

### ->> flight.Player
> 待添加

### ->> Demos
> 待添加

### ->> See also
> 待添加
-->

### ->> Reference link
> MDN中文文档 https://developer.mozilla.org/zh-CN/docs/Web/SVG
>
> MDN 英文文档 https://developer.mozilla.org/en-US/docs/Web/SVG
>
> CSS-Tricks https://css-tricks.com/using-svg/
>
> 阮一峰 https://www.ruanyifeng.com/blog/2018/08/svg.html

### ->> Version History
> 现在版本为V1.0
> 详见 Github(@flightmakers)
>
> 2021.8.15 发布V1.0
