## Title/CSS选择器完全指南 #flight.Archives002 
> 序 : 这是flight.Archives 梦开始的地方, 作者我熬夜肝出来了这篇文章... 保证这是最简洁高效的 CSS Selectors 教程
> 
> Note : 暂时没有能够选择 父元素、父元素的同级元素，或 父元素的同级元素的子元素 的选择器或者组合器

### Tag/基本选择器 (Basic selectors)
选择器 | 语法 | 作用
:---: | :---: | :---:
通用选择器(Universal selector) | * | 选择所有元素
元素选择器(Type selector) | elementname | 选择所有 <elementname> 元素
类选择器(Class selector) | .classname | 选择所有 class 属性中含有 "classname" 的元素
ID 选择器(ID selector) | #idname | 选择所有 id="idname" 的元素
属性选择器(Attribute selector) | `[attr]` `[attr=value]` `[attr~=value]` `[attr\|=value]` `[attr^=value]` `[attr$=value]` `[attr*=value]` | 选择拥有对应属性的元素

### Tag/分组选择器 (Grouping selectors)
选择器 | 语法 | 作用
:---: | :---: | :---:
选择器列表(Selector list) | SelectorA, SelectorB | 同时选择SelectorA和SelectorB选中的内容

### Tag/组合器 (Combinators)
选择器 | 语法 | 作用 |
:---: | :---: | :---:
后代组合器(Descendant combinator) | SelectorA SelectorB(两者间空格) | 选择SelectorA选中元素的**所有**后代节点
直接子代组合器(Child combinator) | SelectorA > SelectorB | 选择SelectorA选中元素的**直接**子代的节点
一般兄弟组合器(General sibling combinator) | SelectorA ~ SelectorB | 选择SelectorA选中元素**之后**的兄弟元素
紧邻兄弟组合器(Adjacent sibling combinator) | SelectorA + SelectorB | 选择SelectorA选中元素**紧随其后**的兄弟元素
列组合器(Column combinator)(Experimental) | SelectorA \|\| SelectorB | 选择SelectorA选中列元素中的元素

### Tag/伪选择器(Pseudo)
选择器 | 语法 | 作用
:---------: | :--: | :-----------:
伪类(Pseudo classes) | :status | 选择状态与给定相同的元素
伪元素(Pseudo elements) | ::Pseudo_elements | 选择元素的指定伪元素

### ->> Details
#### 基本选择器 - 属性选择器(Attribute Selectors)
语法 | 作用 |   
:---------: | :-----------:
`[attr]` | 选择所有带有attr属性的元素
`[attr=value]` | 选择 attr 属性值为 value 的元素
`[attr~=value]` | 选择 attr 属性值包含以空格分隔的 value 的元素
`[attr\|=value]` | 选择 attr 属性值以 value 或 value- 为前缀("-"为连字符，Unicode 编码为 U+002D)开头典型的应用场景是用来匹配语言简写代码(如 zh-CN，zh-TW 可以用 zh 作为 value)
`[attr^=value]` | 选择 attr 属性值以 value 开头的元素
`[attr$=value]` | 选择 attr 属性值以 value 结尾的元素  
`[attr*=value]` | 选择 attr 属性值包含 value 的元素
`[attr operator value i]` | 在属性选择器的右方括号前添加一个用空格隔开的字母 i(或 I)，可以在匹配属性值时忽略大小写(支持 ASCII 字符范围之内的字母)
`[attr operator value s]` | (Experimental)在属性选择器的右方括号前添加一个用空格隔开的字母 s(或 S)，可以在匹配属性值时区分大小写(支持 ASCII 字符范围之内的字母)

#### 伪选择器 - 伪类选择器
> Reference Link https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes

语法 | 作用
:---------: | :-----------:
`:active` | 选择正在活动链接
`:after` (`::after`) | 在元素之后插入内容, `content` 属性定义了插入的内容
`:before` (`::before`) | 在元素之前插入内容, `content` 属性定义了插入的内容
`:checked` | 选择所有选中的表单元素
`:disabled` | 选择所有禁用的表单元素
`:empty` | 选择所有没有子元素的元素
`:enabled` | 选择所有启用的表单元素
`:first-of-type` | 选择其父级元素的第一个特定类型子元素
`:first-letter` (`::first-letter`) | 选择元素的第一个字母
`:first-line` (`::first-line`) | 选择元素的第一行
`:first-child` | 选择其父元素的第一个子元素
`:focus` | 选择元素输入后具有焦点
`:hover` | 把鼠标放在链接上的状态
`:in-range` | 选择元素指定范围内的值
`:invalid` | 选择所有无效的元素
`:lang( <language-code> )` | 为选择指定语言的元素
`:last-child` | 选择其父元素的最后一个子元素
`:last-of-type` | 选择元素其父级元素是特定类型的最后一个子元素
`:link` | 选择所有未访问链接
`:not(selector)` | 选择所有选定元素以外的元素
`:nth-child(an+b)` | 选择元素所有兄弟元素的第 `an+b` 个元素, `a` , `b` 为常数
`:nth-last-child(an+b)` | 选择元素所有兄弟元素倒数的第 `an+b` 个子元素
`:nth-last-of-type(an+b)` | 选择其父级元素的倒数第 `an+b` 个特定类型子元素
`:nth-of-type(an+b)` | 选择其父级元素的第一个特定类型子元素
`:only-of-type` | 选择其父元素仅有的一个特定类型元素
`:only-child` | 选择其父元素仅有的一个子元素
`:optional` | 选择没有"required"的元素属性
`:out-of-range` | 选择指定范围以外的值的元素属性
`:read-only` | 选择只读属性的元素属性
`:read-write` | 选择没有只读属性的元素属性
`:required` | 选择有"required"属性指定的元素属性
`:root` | 选择文档的根元素
`:target` | 选择唯一的一个 `id` 与当前URL片段匹配的元素
`:valid` | 选择所有有效值的属性
`:visited` | 选择所有访问过的链接

##### 伪选择器 - 伪类选择器 - an+b的详细语法
> Reference Link
> https://developer.mozilla.org/zh-CN/docs/Web/CSS/:nth-child

- 0n+3 或简单的 3 匹配第三个元素.
- 1n+0 或简单的 n 匹配每个元素.（兼容性提醒：在 Android 浏览器 4.3 以下的版本 n 和 1n 的匹配方式不一致.1n 和 1n+0 是一致的，可根据喜好任选其一来使用.）
- 2n+0 或简单的 2n 匹配位置为 2、4、6、8...的元素（n=0时，2n+0=0，第0个元素不存在，因为是从1开始排序).你可以使用关键字 even 来替换此表达式.
- 2n+1 匹配位置为 1、3、5、7...的元素. 你可以使用关键字 odd 来替换此表达式.
- 3n+4 匹配位置为 4、7、10、13...的元素.  
a 和 b 都必须为整数，并且元素的第一个子元素的下标为 1. 换言之就是，该伪类匹配所有下标在集合 { an + b; n = 0, 1, 2, ...} 中的子元素.另外需要特别注意的是，an 必须写在 b 的前面，不能写成 b+an 的形式.

#### 伪选择器 - 伪元素选择器
> Reference Link https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-elements

语法 | 作用
:---: | :---:
::after (:after) | 在元素之后插入内容, `content` 属性定义了插入的内容
::before (:before) | 在元素之前插入内容, `content` 属性定义了插入的内容
::first-letter (:first-letter) | 选择元素的第一个字符
::first-line (:first-line) | 选择元素的第一行
::grammar-error(Experimental) | 选择元素被浏览器标识为语法错误的文本段
::marker(Experimental) | 选择列表元素的项目符号
::placeholder(Experimental) | 选择一个元素的占位符
::selection | 选择用户选中的内容
::slotted() | 选定被放在 `HTML模板` 中的元素,详见 https://developer.mozilla.org/zh-CN/docs/Web/CSS/::slotted
::spelling-error(Experimental) | 选择被浏览器标记为不正确拼写的文本段

#### 参见 - 选择器优先级
> Reference Link https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity

> 高优先级选择器定义的样式会覆盖低优先级选择器  
> `!important` 选择器 > `ID 选择器` > `类选择器` ,`属性选择器` 和 `伪类` > `元素选择器` 和 `伪元素`  

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
> MDN中文文档 https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSSSelectors  
> 
> MDN 英文文档 https://developer.mozilla.org/en-US/docs/Web/CSS/CSSSelectors
> 
> 菜鸟教程 https://www.runoob.com/cssref/css-selectors.html
> 
> CodingStartUp https://www.bilibili.com/video/BV1et411K7RU

### ->> Version History
> 现在版本为V1.1.
> 详见 Github(@flightmakers)
> 
> 2021.8.10 : 发布V1.0
> 
> 2021.8.13 : 更新了Details,修改了一些表述

