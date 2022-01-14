# CSS笔记


## 设计原则

### 

## 语法

1. CSS 规则由两个主要的部分构成：选择器，以及一条或多条声明。
```
selector {property: value；property: value}
```
2. 选择器通常是需要改变样式的 HTML 元素；每条声明由一个属性和一个值组成。

### CSS定位

1. 定位的基本思想很简单，它允许你定义元素框相对于其正常位置应该出现的位置，或者相对于父元素、另一个元素甚至浏览器窗口本身的位置。
2. 一切皆为框：
```
div、h1 或 p 元素常常被称为块级元素。这意味着这些元素显示为一块内容，即“块框”。与之相反，span 和 strong 等元素称为“行内元素”，这是因为它们的内容显示在行中，即“行内框”。
您可以使用 display 属性改变生成的框的类型。这意味着，通过将 display 属性设置为 block，可以让行内元素（比如 <a> 元素）表现得像块级元素一样。还可以通过把 display 设置为 none，让生成的元素根本没有框。这样的话，该框及其所有内容就不再显示，不占用文档中的空间。
但是在一种情况下，即使没有进行显式定义，也会创建块级元素。这种情况发生在把一些文本添加到一个块级元素（比如 div）的开头。即使没有把这些文本定义为段落，它也会被当作段落对待：
<div>
some text
<p>Some more text.</p>
</div>
在这种情况下，这个框称为无名块框，因为它不与专门定义的元素相关联。
块级元素的文本行也会发生类似的情况。假设有一个包含三行文本的段落。每行文本形成一个无名框。无法直接对无名块或行框应用样式，因为没有可以应用样式的地方（注意，行框和行内框是两个概念）。但是，这有助于理解在屏幕上看到的所有东西都形成某种框。
```


## 选择器

| 选择器 |	例子 |	例子描述 |	CSS |
| ----- | -- | -- | -- |
| .class |	.intro |	选择 class="intro" 的所有元素。|	1|
| .class element | .static h1 | 选择 class="static" 中 h1 的所有元素 |  |
| element.class | h1.static | 选择class为static的所有h1元素 | |
| #id|	#firstname|	选择 id="firstname" 的所有元素。|	1|
|*|	*|	选择所有元素。|	2|
|element|	p|	选择所有 <p> 元素。|	1|
|element,element |	div,p	| 选择所有 <div> 元素和所有 <p> 元素 |	1
|element element |	div p	| 选择 <div> 元素内部的所有 <p> 元素 |	1
|element>element |	div>p	| 选择父元素为 <div> 元素的所有 <p> 元素 |	2
|element+element |	div+p	| 选择紧接在 <div> 元素之后的所有 <p> 元素 |	2
|[attribute] |	[target]	选择带有 target 属性所有元素。	2
|[attribute=value]	[target=_blank]	选择 target="_blank" 的所有元素。	2
|[attribute~=value]	[title~=flower]	选择 title 属性包含单词 "flower" 的所有元素。	2
|[attribute|=value]	[lang|=en]	选择 lang 属性值以 "en" 开头的所有元素。	2
|:link	a:link	选择所有未被访问的链接。	1
|:visited	a:visited	选择所有已被访问的链接。	1
|:active	a:active	选择活动链接。	1
| :hover |	a:hover |	选择鼠标指针位于其上的链接。	|1|
|:focus	input:focus	选择获得焦点的 input 元素。	2
|:first-letter	p:first-letter	选择每个 <p> 元素的首字母。	1
|:first-line	p:first-line	选择每个 <p> 元素的首行。	1
|:first-child	p:first-child	选择属于父元素的第一个子元素的每个 <p> 元素。	2
|:before	p:before	在每个 <p> 元素的内容之前插入内容。	2
|:after	p:after	在每个 <p> 元素的内容之后插入内容。	2
|:lang(language)	p:lang(it)	选择带有以 "it" 开头的 lang 属性值的每个 <p> 元素。	2
|element1~element2	p~ul	选择前面有 <p> 元素的每个 <ul> 元素。	3
|[attribute^=value] |	a[src^="https"]	选择其 src 属性值以 "https" 开头的每个 <a> 元素。	3
|[attribute$=value]	|a[src$=".pdf"]	选择其 src 属性以 ".pdf" 结尾的所有 <a> 元素。	3
|[attribute*=value]	|a[src*="abc"]	选择其 src 属性中包含 "abc" 子串的每个 <a> 元素。	3
|:first-of-type	    |p:first-of-type	选择属于其父元素的首个 <p> 元素的每个 <p> 元素。	3
|:last-of-type	    |p:last-of-type	选择属于其父元素的最后 <p> 元素的每个 <p> 元素。	3
|:only-of-type	    |p:only-of-type	选择属于其父元素唯一的 <p> 元素的每个 <p> 元素。	3
|:only-child	    |p:only-child	选择属于其父元素的唯一子元素的每个 <p> 元素。	3
|:nth-child(n)	    |p:nth-child(2)	选择属于其父元素的第二个子元素的每个 <p> 元素。	3
|:nth-last-child(n)	|p:nth-last-child(2)	同上，从最后一个子元素开始计数。	3
|:nth-of-type(n)	|p:nth-of-type(2)	选择属于其父元素第二个 <p> 元素的每个 <p> 元素。	3
|:nth-last-of-type(n)|	p:nth-last-of-type(2)	同上，但是从最后一个子元素开始计数。	3
|:last-child	    |p:last-child	选择属于其父元素最后一个子元素每个 <p> 元素。	3
|:root	            |:root	选择文档的根元素。	3
|:empty	            |p:empty	选择没有子元素的每个 <p> 元素（包括文本节点）。	3
|:target	        |#news:target	选择当前活动的 #news 元素。	3
|:enabled	        |input:enabled	选择每个启用的 <input> 元素。	3
|:disabled	        |input:disabled	选择每个禁用的 <input> 元素	3
|:checked	        |input:checked	选择每个被选中的 <input> 元素。	3
|:not(selector)	    |:not(p)	选择非 <p> 元素的每个元素。	3
|::selection	    |::selection	选择被用户选取的元素部分。	3


## 属性

| 属性 | 功能| 可能的值 | 示例 |
| -- | -- | -- | -- |
| background | 背景色 |
| `border`|	在一个声明中设置所有的边框属性按顺序（border-width，border-style，border-color） |
| color | 颜色：red，#ff0000，#f00，rgb(255,0,0)，rgb(100%,0%,0%) |
| display | 规定元素应该生成的框的类型 | [参考](http://www.w3school.com.cn/cssref/pr_class_display.asp)|
| float | 块级元素加入这个属性，就不会换行| 
| font-family | 字体系列| "sans serif" |
| font-size | 字体大小px |
| font-weight | 规定字体的粗细 | normal:默认值,定义标准的字符;bold:定义粗体字符;bolder:定义更粗的字符;lighter:定义更细的字符;100\200\300\400\500\600\700\800\900:定义由粗到细的字符,400 等同于 normal，而 700 等同于 bold;inherit:规定应该从父元素继承字体的粗细。|
| left | 原始位置左侧增加px，可以为负|
| letter-spacing | 设置字符间距 | normal：默认，规定字符间没有额外的空间；length：定义字符间的固定空间（允许使用负值）；inherit：规定应该从父元素继承 letter-spacing 属性的值|
| line-height | 设置行间的距离（行高） | normal:默认,设置合理的行间距;number:设置数字，此数字会与当前的字体尺寸相乘来设置行间距;length:设置固定的行间距;%:基于当前字体尺寸的百分比行间距 |
inherit	规定应该从父元素继承 line-height 属性的值。|
| `margin` | 在一个声明中设置所有外边距属性。| |
| `margin-bottom` |设置元素的下外边距| |
| `margin-left`	|设置元素的左外边距| |
| `margin-right`	|设置元素的右外边距| |
| `margin-top`	|设置元素的上外边距| |
| `object-fit`  |指定可替换元素的内容应该如何适应到其使用的高度和宽度确定的框|contain：被替换的内容将被缩放，以在填充元素的内容框时保持其宽高比。 整个对象在填充盒子的同时保留其长宽比，因此如果宽高比与框的宽高比不匹配，该对象将被添加“黑边”。cover：被替换的内容在保持其宽高比的同时填充元素的整个内容框。如果对象的宽高比与内容框不相匹配，该对象将被剪裁以适应内容框。fill：被替换的内容正好填充元素的内容框。整个对象将完全填充此框。如果对象的宽高比与内容框不相匹配，那么该对象将被拉伸以适应内容框。none：被替换的内容将保持其原有的尺寸。scale-down：内容的尺寸与 none 或 contain 中的一个相同，取决于它们两个之间谁得到的对象尺寸会更小一些。 |例如video填充div|
| opacity	|规定元素的不透明级别| 
| `padding` |在一个声明中设置所有内边距属性 | |
| `position` | 定义建立元素布局所用的定位机制 | 默认：static；relative：相对于其正常位置进行定位；absolute：生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位；fixed：生成绝对定位的元素，相对于浏览器窗口进行定位；inherit：规定应该从父元素继承 position 属性的值|
| table-layout | 用来显示表格单元格、行、列的算法规则 | automatic	默认。列宽度由单元格内容设定。fixed	列宽由表格宽度和列宽度设定。inherit	规定应该从父元素继承 table-layout 属性的值。 |
| `text-align` | 文字对齐：center |
| text-transform | 控制文本的大小写 | none：默认，定义带有小写字母和大写字母的标准的文本；capitalize：文本中的每个单词以大写字母开头；uppercase：定义仅有大写字母；lowercase：定义无大写字母，仅有小写字母；inherit：规定应该从父元素继承 text-transform 属性的值 |
| top |
| transform | 向元素应用 2D 或 3D 转换 | [参考](http://www.w3school.com.cn/cssref/pr_transform.asp) |
| z-index | 设置元素的堆叠顺序 | |


## 案例分析

### DIV层叠与固定

1. 在父对象上设置：position:relative; 在子对象上设置：position:absolute; top: 10px; left: 10px; 这样如果父对象里面有其他元素的话。上面设置的子对象就可以"悬浮"在父对象上面了。

### B站视频布局
```

```
