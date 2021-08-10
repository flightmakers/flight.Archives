## Title/CSS选择器
> 序 : 这是flight.Archives 梦开始的地方, 作者我熬夜肝出来了这篇文章... 保证这是最简洁高效的 CSS Selectors 教程
> 
> Note : 暂时没有能够选择 父元素、父元素的同级元素，或 父元素的同级元素的子元素 的选择器或者组合器

### Tag/基本选择器 (Basic selectors)
| 选择器 | 语法 | 作用 |
| :---------: | :--: | :-----------: |
| 通用选择器(Universal selector) | * | 选择所有元素 |
| 元素选择器(Type selector) | elementname | 选择所有 <elementname> 元素 |
| 类选择器(Class selector) | .classname | 选择所有 class 属性中含有 "classname" 的元素 |  
| ID 选择器(ID selector) | #idname | 选择所有 id="idname" 的元素 |
| 属性选择器(Attribute selector) | `[attr]` `[attr=value]` `[attr~=value]` `[attr\|=value]` `[attr^=value]` `[attr$=value]` `[attr*=value]` | `[attr]`选择所有带有attr属性的元素<br>`[attr=value]`选择 attr 属性值为 value 的元素<br>`[attr~=value]`选择 attr 属性值包含以空格分隔的 value 的元素<br>`[attr\|=value]`选择 attr 属性值以 value 或 value- 为前缀("-"为连字符，Unicode 编码为 U+002D)开头典型的应用场景是用来匹配语言简写代码(如 zh-CN，zh-TW 可以用 zh 作为 value)<br>`[attr^=value]`选择 attr 属性值以 value 开头的元素<br>`[attr$=value]`选择 attr 属性值以 value 结尾的元素<br>`[attr*=value]`选择 attr 属性值包含 value 的元素<br>`[attr operator value i]`在属性选择器的右方括号前添加一个用空格隔开的字母 i(或 I)，可以在匹配属性值时忽略大小写(支持 ASCII 字符范围之内的字母)<br>`[attr operator value s]`(Experimental实验性功能)在属性选择器的右方括号前添加一个用空格隔开的字母 s(或 S)，可以在匹配属性值时区分大小写(支持 ASCII 字符范围之内的字母) |

### Tag/分组选择器 (Grouping selectors)
| 选择器 | 语法 | 作用 |
| :---------: | :--: | :-----------: |
| 选择器列表(Selector list) | SelectorA, SelectorB | 同时选择SelectorA和SelectorB选中的内容 |

### Tag/组合器 (Combinators)
| 选择器 | 语法 | 作用 |
| :---------: | :--: | :-----------: |
| 后代组合器(Descendant combinator) | SelectorA SelectorB(两者间空格) | 选择SelectorA选中元素的**所有**后代节点 |
| 直接子代组合器(Child combinator) | SelectorA > SelectorB | 选择SelectorA选中元素的**直接**子代的节点 |
| 一般兄弟组合器(General sibling combinator) | SelectorA ~ SelectorB | 选择SelectorA选中元素**之后**的兄弟元素 |
| 紧邻兄弟组合器(Adjacent sibling combinator) | SelectorA + SelectorB | 选择SelectorA选中元素**紧随其后**的兄弟元素 |
| 列组合器(Column combinator)(Experimental实验性功能) | SelectorA \|\| SelectorB | 选择SelectorA选中列元素中的元素 |

### Tag/伪选择器(Pseudo)
| 选择器 | 语法 | 作用 |   
| :---------: | :--: | :-----------: |
| 伪类(Pseudo classes) | :status | 选择所有状态与给定 status 相同的元素 |
| 伪元素(Pseudo elements) | ::Pseudo_elements | 选择元素的指定伪元素 |

### ->> Demos
> 详见MDN,Codepen

### ->> See also
> CSS选择器优先级 https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity

### ->> Reference link
> MDN中文文档 https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSSSelectors  
> 
> MDN 英文文档 https://developer.mozilla.org/en-US/docs/Web/CSS/CSSSelectors
> 
> 菜鸟教程 https://www.runoob.com/cssref/css-selectors.html
> 
> CodingStartUp https://www.bilibili.com/video/BV1et411K7RU

### ->> 文档版本&更新记录
> 现在版本为V1.0, 下一版预计加入"CSS支持版本"和"常用度"两列,增加Demos,并将Experimental,Tag,"->>"改为图标.
> 详见 Github
