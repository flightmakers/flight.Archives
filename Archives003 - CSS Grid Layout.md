## Title/ CSS Grid 布局(Grid Layout)完全指南 #flight.Archives003
> 序 :  
> 写完这篇文章后,我准备一直做下去了,包括flight的各个分区,也看到前方的路.  
> 我要做最简约高效的前端教程 //表达最张狂的性格  
>
> 简介(from Ruanyf) :  
> 2017三月,主流浏览器更新了对Grid(网格布局)的支持.  
> 这是最强大的 CSS 布局方案.  
> 它将网页划分成一个个网格,做出各种各样的布局. 以前，只能通过复杂的 CSS 框架达到的效果，现在浏览器内置了.

### Tag/Grid布局介绍
```css
.container{
    display:grid; /*or inline-grid*/
}
```
这段代码定义了.container元素为Grid容器,.container的直接子元素为Grid项目
其中网格的分界线称作Grid Line,两条相邻网格线之间的间隔称作Grid Track.  
单个格子称作Grid Cell,多条网格线包围的区块称作Grid Area

### Tag/Grid容器(Grid Container)属性
属性 | 可取值 | 作用
--- | --- | ---
`display` | `grid`, `inline-grid` | 指定一个块状/行内Grid容器
`grid-template-columns`, `grid-template-rows` | 多个值, `[line-name] <track-size>...`(网格线的名称,间隔的宽度) | 指定每个行/列的名称和大小, 详见Details
`grid-template-areas` | 多个值, `<grid-area-name>`(区块的名称) 或 `.`(空的区块) | 指定每一个区块的名称, 详见Details
`grid-template` | `<grid-template-rows> <grid-template-columns>` | 对于 `<grid-template-rows>`, `<grid-template-columns>` 和 `<grid-template-areas>`的CSS简写属性
`column-gap`(前`grid-column-gap`),`row-gap`(前`grid-row-gap`) | `<line-size>`(网格线的宽度) | 指定行/列的间距 
`gap`(前`grid-gap`) | `<grid-row-gap> <grid-column-gap>` | 对于 `row-gap`和 `column-gap`的CSS简写属性
`align-items`, `justify-items` | `start`,`end`,`center`,`stretch`(默认值) | `align-items`指定项目的行对齐方式, `justify-items`指定项目的列对齐方式
`place-items` | `<align-items> <justify-items>` | CSS简写属性, 如果只有一个值则同时指定两个属性
`justify-content`,`align-content` | `start`, `end`, `center`, `stretch`, `space-around`, `space-between`, `space-evenly` | `justify-content`指定项目在容器中的水平位置, `align-content`指定项目在容器中的垂直位置
`place-content` | `<align-content> <justify-content>` | CSS简写属性, 如果只有一个值则同时指定两个属性
`grid-auto-columns`, `grid-auto-rows` | 多个值, `<track-size>` | 指定在有多余区块时浏览器自动创建的多余网格的列宽和行高
`grid-auto-flow` | `row`, `column`, `row dense`, `column dense` | 指定项目的放置顺序,默认是 `row` 即"先行后列"
`grid` | `<grid-template>`, `<'grid-template-rows'> [ auto-flow && dense? ] <'grid-auto-columns'>`, `[ auto-flow && dense? ] <'grid-auto-rows'>? <'grid-template-columns'>` | `grid-template-rows`, `grid-template-columns`, `grid-template-areas`, `grid-auto-rows`, `grid-auto-columns` 和 `grid-auto-flow`的CSS简写属性, 详见Details

### Tag/Grid项目(Grid item)属性
属性 | 可取值 | 作用
--- | --- | ---
`grid-column-start`, `grid-column-end`, `grid-row-start`, `grid-row-end` | `<line>`, `span <number>`, `span <name>` | 指定项目所占的Grid Area, 详见Details
`grid-column`, `grid-row` | `<start-line> / <end-line>` | `grid-column`是 `grid-column-start`和 `grid-column-end`的CSS简写属性, `grid-row`是 `grid-row-start`和 `grid-row-end`的CSS简写属性
`grid-area` | `<name>`, `<row-start> / <column-start> / <row-end> / <column-end>` | 指定项目所在区域
`justify-self`,`align-self` | `start`,`end`,`center`,`stretch` | `justify-self`指定项目在区块中的水平位置, `align-self`指定项目在区块中的垂直位置, 
`place-self` | `auto`(默认值), `<align-self> <justify-self>` | `<align-self>`, `<justify-self>` 的CSS简写属性, 如果只有一个值则同时指定两个属性

### ->> Details
#### 容器属性 - `grid-template-columns`, `grid-template-rows` 
可取值:  
```
grid-template-columns: 100px 1fr; /*区块的大小*/
grid-template-columns: [linename] 100px; /*方括号内可以定义网格线的名称, 方便以后引用*/
grid-template-columns: [linenameA linenameB] 100px; /*一条线可以有多个名字*/
grid-template-columns: [linenameA linenameB]; /*可以只定义名称,不定义大小*/
```

特殊内容介绍:  
1. repeat() 函数  
   > CSS函数 repeat(times, value) 可以简化重复值输入的繁琐
   ```css
   .container {
       display: grid;
       grid-template-columns: repeat(4, 25%); /*等同于 grid-template-columns: 25% 25% 25% 25%;*/
       grid-template-rows: repeat(2, 50px 100px 80px); /*等同于 grid-template-columns: 50px 100px 80px 50px 100px 80px;*/
   }
   ``` 
   特殊说明:  
   `times` 参数接受 `auto-fill` 和 `auto-fit` 关键字, 适用于容器大小不固定的情况  
   ```
   grid-template-columns: repeat(auto-fill, 100px) /*在一行内不断放置100px的项目直到填满该行*/
   ```
   - `auto-fill` 表示自动判断重复次数以在每一行/列填充尽可能多的项目  
   - `auto-fit` 表示自动判断重复次数以在每一行/列填充尽可能多的空间  
   在含有 `minmax()` 函数等可变因素的情况下, 二者可能会产生不同表现    
   二者对比: https://css-tricks.com/auto-sizing-columns-css-grid-auto-fill-vs-auto-fit/
   Demo: https://codepen.io/flightmakers/pen/GRmLKyZ?editors=0100
     

2. 长度单位 `fr`  
   "fr" 是 "fraction"("片段") 的缩写, 可以用于指定宽度比例   
   ```css
   .container {
       display:grid;
       grid-template-columns: 200px 1fr 2fr;
   }
   ```
   `fr` 单位可以和绝对单位一起使用, 上面代码指定了第一行200px, 第二行宽度是第三行的一半   


3. 用于控制大小的关键字
   - `min-content` 分配最小宽度
   - `max-content` 分配最大宽度
   - `fit-content` 分配的宽度在 `min-content` 和 `max-content` 之间
   - `auto` 自动分配宽度

4. 用于控制大小的函数  
   `minmax(min,max)` 接受两个参数, 表示一个长度范围, 表示宽度不小于 `min` 不超过 `max`.  
   如果只定义其一, 可以使用 `min()` 和 `max()` 函数

#### 容器属性 - `grid-template-areas`
可取值:  
```
/*<string>+*/
grid-template-areas:
"a a a"
"b c c"; /*划分出6个区块, 然后将其命名为 `a`, `b` 和 `c` 的3个区域*/

grid-template-areas:
"a a ."
"b c c"; /* . 可以将某个区块指定为空*/
```

#### 项目属性 - `grid`
可取值:  
```
/*<'grid-template'> (grid-template-columns 和 grid-template-rows) */
grid: "a" 100px "b" 1fr; 
grid: [linename1] "a" 100px [linename2]; 
grid: "a" 200px "b" min-content; 
grid: "a" minmax(100px, max-content) "b" 20%; 

/* <'grid-template-rows'> / [ auto-flow && dense? ] <'grid-auto-columns'> */
grid: 200px / auto-flow; 
grid: 30% / auto-flow dense;
grid: repeat(3, [line1 line2 line3] 200px) / auto-flow 300px;
grid: [line1] minmax(20em, max-content) / auto-flow dense 40%;

/* [ auto-flow && dense? ] <'grid-auto-rows'>? / <'grid-template-columns'>*/
grid: auto-flow / 200px;
grid: auto-flow dense / 30%;
grid: auto-flow 300px / repeat(3, [line1 line2 line3] 200px);
grid: auto-flow dense 40% / [line1] minmax(20em, max-content);
```
这个属性对代码的易读性存在负面影响QwQ...

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
> MDN中文文档 https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout
>
> MDN 英文文档 https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout
>
> CodingStartUp https://www.bilibili.com/video/BV1XE41177oN?from=search&seid=7045917601727025410
>
> CSS-Tricks https://css-tricks.com/snippets/css/complete-guide-grid/
> 关于 `auto-fill` 和 `auto-fit` 属性的对比 https://css-tricks.com/auto-sizing-columns-css-grid-auto-fill-vs-auto-fit/
> 
> Scotch.io#1 https://scotch.io/tutorials/deep-dive-into-css-grid-2  
> Scotch.io#2 https://scotch.io/tutorials/getting-started-with-css-grid-layout

### ->> Version History
> 现在版本为V1.0
> 详见 Github(@flightmakers)
> 
> 2021.8.13 在jj发布V1.0
> 
> 2021.8.14 奋(mo)战(yu)了两天!!! 这篇文章终于发布了QwQ!!!
