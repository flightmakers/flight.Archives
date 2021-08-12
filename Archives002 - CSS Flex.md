## Title/CSS Flex布局完全指南 #flight.Archives002
> 序(from Ruanyf) :  
> 
> 网页布局（layout）是 CSS 的一个重点应用.  
> 布局的传统解决方案，基于盒状模型，依赖 display 属性 + position属性 + float属性. 它对于那些特殊布局非常不方便，比如，垂直居中就不容易实现  
> 2009年，W3C 提出了一种新的方案 - Flex 布局，可以简便、完整、响应式地实现各种页面布局.

### Tag/Flex介绍
```css
.container{
    display:flex; /*or inline-flex*/
}
```
设置了.container元素为Flex容器(Flex container),.container的直接子元素被称为Flex项目(Flex item)  
Flex元素按照主轴(main-axis)的方向排列,交叉轴(cross-axis)与主轴垂直  
主轴的开始和结束被称为 main start 和 main end  
交叉轴的开始和结束被称为 cross start 和 cross end  

### Tag/容器(flex container)属性
| 属性 | 可取值 | 说明 |
| :---------: | :--: | :-----------: |
| `display` | `flex`,`inline-flex` | 指定一个容器是否启用Flex布局 |
| `flex-direction` | `row`,`row-reverse`,`column`,`column-reverse` | 指定主轴(main-axis)的方向 |
| `flex-wrap` | `nowrap`,`wrap`,`wrap-reverse` | 指定内容超出一行情况的换行方式 |
| `flex-flow` | `<flex-direction> <flex-wrap>` | CSS简写属性 |
| `justify-content` | `flex-start`,`flex-end`,`center`,`space-between`,`space-around` | 指定项目在主轴(main-axis)上的对齐方式 |
| `align-items` | `flex-start`,`flex-end`,`center`,`baseline`,`stretch` | 指定项目在交叉轴(cross-axis)上的对齐方式 |
| `align-content` | `flex-start`,`flex-end`,`center`,`space-between`,`space-around`,`stretch` | 同时指定项目在两根轴线上的对齐方式(如果项目只有一根轴线.该属性不起作用) |

### Tag/项目(flex item)属性
| 属性 | 可取值 | 说明 |
| :---------: | :--: | :-----------: |
| `order` | `<number>` | 指定项目的排列顺序,默认值为0,`order`相同时按照项目在DOM中的顺序排序 |
| `flex-grow` | `<number>` | 指定项目大小的比例,默认值为0,`width`属性会优先指定flex-item的最小大小 |
| `flex-shrink` | `<number>` | 指定项目大小在单行空间不足时的收缩比例,默认值为1 |
| `flex-basis` | `<length>` | 指定项目在主轴方向上的初始大小 |
| `flex` | `<'flex-grow'> <'flex-shrink'> <'flex-basis'>` | CSS简写属性 |
| `align-self` | `auto`,`flex-start`,`flex-end`,`center`,`baseline`,`stretch` | 指定单个元素的对齐方式,优先级高于`align-items`属性 |

### ->> Details
#### `Flex` 属性的值 (from MDN)
```css
/* 关键字值 */
flex: auto;
flex: initial;
flex: none;

/* 一个值, 无单位数字: flex-grow */
flex: 2;

/* 一个值, width/height: flex-basis */
flex: 10em;
flex: 30px;
flex: min-content;

/* 两个值: flex-grow | flex-basis */
flex: 1 30px;

/* 两个值: flex-grow | flex-shrink */
flex: 2 2;

/* 三个值: flex-grow | flex-shrink | flex-basis */
flex: 2 2 10%;

/*全局属性值 */
flex: inherit;
flex: initial;
flex: unset;
```

### ->> Demos
> 详见MDN,Codepen

### ->> See also
> Flex Bugs https://github.com/philipwalton/flexbugs
> 
> Flexbox Properties Demonstration https://codepen.io/justd/full/yydezN/

### ->> Reference link
> MDN中文文档 https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Flexbox
>
> MDN 英文文档 https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox
>
> CodingStartUp https://www.bilibili.com/video/BV1qJ411N7TA
> 
> CSS-Tricks https://css-tricks.com/snippets/css/a-guide-to-flexbox/
> 
> Scotch.io https://scotch.io/tutorials/a-visual-guide-to-css3-flexbox-properties

### ->> 文档版本&更新记录
> 现在版本为V1.0
> 详见 Github(@flightmakers)
