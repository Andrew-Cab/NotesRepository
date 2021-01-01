# 一.HTML

==W3C==(World Wide Web Consortium  万维网联盟)==标准==

- 结构化标准语言  (HTML、XML)
- 表现标准语言  (CSS)
- 行为标准  (COM、ECMAScript)

##1.基础标签 

- 标题标签

```html
<h1>一级标题</h1>
<h2>二级标题</h2>
<h6>二级标题</h6>
```

- 段落标签

```html
<p></p>
<!-- 快捷键p+tab -->
```

- 换行标签

```html
<br/>
```

- 水平线标签

```html
<hr/>
```

- 粗体&斜体

```html
<!-- 粗体 -->
<strong></strong>或<b></b>
<!-- 斜体 -->
<em></em>或<i></i>
```

- 特殊符号

```html
<!-- 连续空格 -->
&nbsp;&nbsp;
<!-- 大于号 -->
&gt;
<!-- 小于号 -->
&lp;
<!-- 版权符号 -->
&copy;
<!-- &符号 -->
&amp;
```

- 图片插入

```html
<!-- 相对路径: ./ 当前目录,不写默认为此。 ../ 上一级目录 -->
<img src="图片路径" alt = "代替文字" title = "悬停文字" width = "x" height = "y"/>
```

- 链接标签

```html
<!-- 文字超链接 -->
<!-- 若跳转链接为"javascript:void(0)"则会保留连接样式而取消跳转效果,为"javascript:函数名"则可以通过链接调函数，为"javascript:location.href='新的地址';"可实现跳转-->
<!--target用来指定新页面的显示形式，_blank表示在新标签页中打开，_self表示在当前页中刷新，_top跳转回顶级页面，并在顶级页面中中显示，_parent跳转回父级页面并在父级页面中显示-->
<a href = "跳转链接" target = "_blank">
    链接文字
</a>

<!-- 图像超链接 -->
<a href = "跳转链接" target = "iframeName">
	图像插入语句
</a>

<!-- 连接标记 -->
<a name = "top">顶部</a>
<!-- 锚链接 调转到同一或不同页面标记的位置-->
<a href = "#top">回到顶部</a>

<!-- base标签，设置在head标签中，当页面内的a标签使用了相对路径，那么这个相对路径会优先参考base标签中的路径，否则参浏览器当前地址 -->
<base href="http://localhost:8080/project/a.html"/>
<!-- 功能性链接 -->
<a href = "mailto:邮箱地址">链接文字</a>
```

## 2.列表 

- 有序列表

```html
<ol>
    <li>列表名称</li>
</ol>
```

- 无序列表

```html
<ul>
    <li>列表名称</li>
</ul>
```

- 自定义列表

```html
<!--
dt: 列表名称
dd: 列表内容
-->

<dl>
    <dt>列表名</dt>
    <dd>列表内容</dd>
    <dd>列表内容</dd>
</dl>
```

## 3.表格

<!--
tr 行
td 单元格
th 与td用法相同，但其中的子会以粗体显示
-->

<table>
    <tr>
        <!-- colspan = "列数" 独占几列 -->
    	<th colspan = "3">单元格内容</td>
    </tr>
    <tr>
        <!-- rowspan = "行数" 独占几行 -->
    	<td rowspan = "2">单元格内容</td>
        <td>单元格内容</td>
        <td>单元格内容</td>
    </tr>
    <tr>
    	<td>单元格内容</td>
        <td>单元格内容</td>
    </tr>
</table>
## 4.视频和音频

```html
<!--
controls 显示进度条 不加此选项网页不会显示任何东西
autoplay 自动播放
-->

<video src = "资源路径" controls autoplay></video>
<audio src = "资源路径" controls autoplay></audio>
```

## 5.语义化标签

| 元素名  |             描述             |
| :-----: | :--------------------------: |
| header  |      标题头部区域的内容      |
| footer  |      标题脚步区域的内容      |
| section |   Web页面中的一块独立区域    |
| article |        独立的文章内容        |
|  aside  | 相关内容或应用(常用于侧边栏) |
|   nav   |        导航类辅助内容        |

##iframe内联框架

```html
<!-- 在一个页面中嵌入另一个页面 -->
<iframe src = "引用页面地址(也可以不填)" name = "框架标识名" frameborder = "0"></iframe>
```

## frameset框架

```html
<html>
    <head>
        <title>框架标签学习</title>
        <meta charset="UTF-8"/>
    </head>
    <frameset rows="10%,*,10%">
        <frame src=""/>
        <frameset cols="10%,*">
            <frame src=""/>
            <frame src=""/>
        </frameset>
        <frame src=""/>
    </frameset>
</html>
<!--
	只需要一个空页面，不需要body标签，直接通过frameset标签可以将网页切分成不同大小的若干单独显示网页资源的区域，再通过frame标签在该部分中添加要显示的网页资源，比iframe框架更为灵活。因为网页被分为了独立的各个部分，所以在各个区域定义的超链接标签在点击时只会在超链接所在的部分进行刷新，若要点击超链接后将整个页面都刷新为新的页面则需要在超链接中添加target="_top"
-->
```



## 6.表单

###6.1表单基础元素

```html
<!-- 表单form
action : 表单提交的位置，可以是网站也可以是请求处理地址
method : 提交方式
post : 提交信息在URL中不可见,封装在请求体中，较安全，可传输大文件
get : 可以在URL中看到提交信息，不安全，不可传输大文件但高效
-->
<form action = "网页地址" method = "get">
    <!--
    文本输入框 : input type = "text"
    value : 初始值
	maxlength : 文本框最大长度
	name:为提交数据时的键，必须有，值为用户输入的内容
	readonly="readonly": 只读，用户不能修改，但数据会提交
	disabled="disabled": 失效，设置该属性的标签用户将不能点击，数据不会提交
    -->
    <p>
        名字: <input type = "text" name = "标识名" value = "xxx"/>
    </p>
    
    <!--密码框 : input type = "password" -->
    <p>
        密码: <input type = "password" name = "标识名"/>
    </p>
                      
    <!--
	单选框: input type = "radio"
	value: 单选框提交的值，必须有，否则不能提交
	name : 分组，若想起到单选框的效果则分组必须相同否则于多选框相同
	checked : 不写或为true为选中状态，false为不选中状态
	-->
    <p>性别:
        <input type = "radio" value = "boy" name = "sex" checked="checked"/>男
        <input type = "radio" value = "gril" name = "sex"/>女
    </p>
            
    <!--=
        多选框: input type = "checkbox"
        value: 多选框的值
		checked : 默认选中
     -->
    <p>爱好:
        <input type = "checkbox" value = "sleep" name = "hobby"/>睡觉
        <input type = "checkbox" value = "game" name = "hobby" checked="checked"/>游戏
    </p>
     
    <!-- 按钮 -->
	<p>按钮:
        <input type = "butten" name = "but1" value = "按钮名称"/>
        <!-- 图片按钮 与提交按钮效果一样 -->
        <input type = "image" src = "图片路径"/>
        <!-- 提交 -->
        <input type = "submit" value="登录"/>
        <!-- 重置 -->
        <input type = "reset"/>
    </p>
    
	<!--
	下拉框
	selected : 默认被选中的
	-->
    <p>国家:
        <select name = "下拉框名称">
            <option value = "china">中国</option>
            <option value = "us" selected="selected">美国</option>
            <option value = "swit">瑞士</option>
        </select>
    </p>
    
    <!--
		文件域，提交文件的表单必须加encType=multipart/form-data属性，且提交方式为post
	-->
    <p>
        <input type = "file" name = "files"/>
    </p>
    
    <!--
		文本域
			clos: 每一行有多少列
			rows: 共可输入多少行
	-->
    <p>反馈:
        <textarea name = "textarea" clos = "10" rows = "10">文本内容</textarea>
    </p>
    
    <!-- 隐藏域：不会打乱页面布局，通常用来提交必要但不需要在页面展示的参数 -->
    <p>
        <input type = "hidden" value = "id"
    </p>
</form>
<!--快速添加多个标签：ul>li*4>a   一个ul标签下有4各li标签，每个li标签下有1个a标签-->
```

==form表单中的复选框，单选框，在提交表单时会自动提交键和值，若此类标签在table中定义，想要提交选框的值，只需将table嵌套在form标签中提交form==

###6.2其他表单元素

```html
<!-- 带有验证效果的元素 -->

<!-- 邮箱 -->
<P>邮箱:
    <input type = "email" name = "emails"/>
</P>

<!-- URL -->
<p>URL:
    <input type = "URL" name = "URL"/>
</p>

<!-- 数字 -->
<p>商品数量:
    <input type = "number" name = "num" max = "100" min = "10" step = "2"/>
</p>

<!-- 滑块 常用于音量调节 -->
<p>音量:
    <input type = "range" name = "voice" min = "0" max = "100" step = "2"
</p>
    
<!-- 搜索框 -->
<p>搜索:
    <input type = "search" name = "search"/>
</p>

<!--
增强鼠标可用性 <label for = "id">文字</label>
在通过在某选框中增加id属性赋值到label标签的for属性中来与label标签中的“文字”建立联系实现点击文字即可跳到某选框的功能
-->
<p>
    <label for = "mark">点击文字可跳转for标记的选框</label>
    <input type = "text" id = "mark"/>
</p>

<!--
其它输入框类型:
取色器  color
日期  date
日期时间  date-local
隐藏域  hidden  此类型标签不会显示在客户端，常用来设置一个name和value给服务器端做判断处理
只读  readonly
禁用  disabled
-->
```

==表单提交时以"name=value"的键值对形式提交，若不添加name属性则不能正常提交==

### 6.3表单初级验证

- ***placeholder = "提示内容*** "   用于文本框中提示用户输入，提示内容在文本框中以灰色显示
- ***required***   非空限制
- ***pattern = "正则表达式"***  正则表达式判断



#二.CSS

##1.css的三种导入方式

```html
<!--行内样式/内联样式 通过style属性指定样式，有多个样式以";"断开-->
<h1 style="color: red">
    我是标题
</h1>

<!--内部样式，位于html页面<head>标签中 -->
<style>
    /*标签选择器，所用于页面中所有此标签*/
    h1{
        color: #a13d30;
    }
</style>

<!--外部样式,位于css页面中,需要在html中使用link标签引入 -->
h1{
	color: yellow;
}
<!-- 连接式导入css页面 -->
<link rel = "stylesheet" href = "css文件路径"/>
<!-- 导入式导入css页面 css2.1 弊端:到网页庞大时，会先加载骨架然后渲染-->
<style>
	@import url("css页面路径");
</style>

<!--
优先级:就近原则
依据哪段css代码距离被修饰代码最近
-->
```

## 2.选择器

###2.1.基本选择器

==选中多个选择器时，选择器间使用","隔开==

1. 标签选择器

2. 类选择器 class

```html
<head>
    <style>
    	/*
        格式: .class的名称{}
        class可以复用
        */
    	.lable{}
	</style>
</head>

<body>
    <h1 class="lable">标题1</h1>
    <h1 class="lable">标题1</h1>
</body>
```

3. id 选择器

```html
<head>
    <style>
        /*
        格式: #id名称{}
        id必须唯一
        */
        #wujianqi{}
    </style>
</head>
<body>
    <h1 id = "wujianqi">标题1</h1>
</body>
```

==优先级:id选择器>class选择器>标签选择器==

### 2.2.层次选择器

```html
<head>
    <style>
    /*后代选择器: 在某一层级后的所有指定标签*/
        body p{/*body后所有p标签*/
            background: red;
        }
    /*子选择器: 在某一层级后一层的指定标签*/
        body>p{/*body后一层级的p标签*/
            background: #3cbda6;
        }
    /*相邻弟选择器: 只有一个，相邻向下*/
        .active+p{
            background: black; /*p3和p8变色*/
        }
    /*通用选择器: 指定标签以下同级标签，不包括自己*/
       	#wujianqi~p{
            background: green;
        }
    </style>
</head>
<body>
    <p>p1</p>
    <p class="active">p2</p>
    <p id = "wujianqi">p3</p>
    <input type = "text">
    <ul>
        <li>
        	<p>p4</p>
        </li>
        <li>
        	<p>p5</p>
        </li>
        <li>
        	<p>p6</p>
        </li>
    </ul>
    <p>p7</p>
</body>
```

###2.3.结构伪类选择器

```html
<head>
    <style>
    	/*选中父标签第一个或最后一个子标签，且这个标签必须是指定的标签，否则不显示*/
        ul li:first/last-child{
            background: #02ff00;
        }
        /*选中a标签的父标签的第一个子标签，且这个标签必须是a，因为所有类型的标签都会加入排序*/
        a:nth-child(1){
             background: #02ff00;
        }
        /*选中此类型标签的父标签，然后选定父标签的第二个此类型子标签，只有此类型标签会被加入排序*/
        p:nth-of-type(2){
            background: #02ff00;
        }
        /*鼠标悬浮元素*/
        a:hover{
            color: orange;
            font-size: 50px; /*悬浮元素放大*/
        }
        /*鼠标点击未松开的元素*/
        a:active{
            color: green;
        }
    </style>
</head>
<body>
    <p>p1</p>
    <a>a1</a>
    <p>p2</p>
    <ul>
        <li>li1</li>
        <li>li2</li>
        <li>li3</li>
    </ul>
</body>
```



### 2.4.属性选择器

```html
<head>
    <style>
    	/*
        标签[属性名/属性名=属性值]
         = 绝对等于
         *= 模糊等于，即包含
         ^= 以此值开头
         $= 以此值结尾
        */
        a[class*="wu"]{ /*选中a标签中class属性包含wu的a标签*/
            background: #02ff00;
        }
    </style>
</head>
```

## 3.网页美化元素

### 3.1.文本样式

```css
/*颜色:
color: #FFFFFF(白色) 十六进制RGB，每两位表示一种颜色
	   rgb(0,255,255)
	   rgba(0,255,255,0.5) a表示透明度
*/
p{
    color: rgba(0,255,255,0.5);
    /*首行缩进两个字*/
    text-indent: 2em;
    /*水平居中对齐center*/
    text-align: center;
    /*设置文字间距*/
    letter-spacing: 10px;
    /*文本背景色*/
    background: #2700ff;
    /*标签块高*/
    height: 300px;
    /*行高，若指定行行高与其块高度相同，文字会自动上下居中*/
    line-height: 300px;
}
/*超链接去下划线*/
a{
    text-decoration: none;
}
/*文本阴影*/
p{
    text-shadow: #3cc7f5 10px 10px 10px; /*参数: 水平偏移 竖直偏移 模糊半径*/
}
```

### 3.2.无序列表样式

list-style-type:none;  无样式

list-style: none; 无样式

​				 circle； 空心圆

​				 decimal; 数字

​				 square；正方形

### 3.3.背景

图片

```html
<head>
   <style>
       div{
           width: 1000px;
           height: 700px;
           border: 1px solid red;
           background-image: url("图片地址");
           background-size: cover;/*设置图片水平大小为与标签大小相同*/
       }
       .div1{
           background-repeat: repeat-x;/*水平重复平铺*/
       }
       .div2{
           background-repeat: repeat-y;/*竖直重复平铺*/
       }
       .div3{
           background-repeat: 270px 10px no-repeat;/*指定位置背景图*/
           /*background-position: 270px 10px*/
       }
    </style> 
</head>
<body>
    <div class = "div1"></div>
    <div class = "div2"></div>
    <div class = "div3"></div>
</body>
```

### 3.4.渐变

background: linear-gradient(direction,color-spot1,color-spot2....)

[渐变色网址](https://www.grabient.com/)

## 4.盒子模型

```html
<head>
    <style>
        h1,ul,li,a,body{ /*初始化页面*/
            margin: 0; /*许多标签默认有一个内外边距*/
            /*默认情况下内边距会影响此盒子的大小*/
            padding: 0;
            /*通过此语句确定盒子大小，解决设置内边距时使盒子大小改变的情况*/
            box-sizing: border-box;
            text-decoration: none;
        }
        #box{
            width: 300px;
            border: 1px solid red; /*粗细 样式 颜色*/
            margin: 0 auto;/*上下为0，左右自动(居中)*/
        }
        h2{
            font-size: 16px;
            background: black;
            line-height: 30px;
            margin: 0;
        }
        form{
            background: #3cbda6;
        }
        div:nth-of-type(1)>input{ /*包含div标签的父标签的第一个div型标签的后一层的input标签*/
            border: 3px solid black;
        }
        div:nth-of-bype(2)>input{
            border: 3px dashed #4d0b8c;
        }
        div:nth-of-type(3)>input{
            border: 3px solid #198c2b;
        }
    </style>
</head>
<body>
    <div id = "box">
        <h2>会员登陆</h2>
        <form action="#">
            <div>
                <span>用户名:</span>
                <input type = "text">
            </div>
            <div>
                <span>密码:</span>
                <input type = "password">
            </div>
            <div>
                <span>邮箱:</span>
                <input type = "text">
            </div>
        </form>
    </div>
</body>
```

## 5.display和浮动

### 5.1.display

**display: block;**  将行内元素变为块元素

**display: inline;**  将块元素变为行内元素

**display: inline-block;**  保持块元素的特性时还可以在一行中

### 5.2.浮动

***clear: both;***   不允许该标签左右有浮动标签；效果为在浮动的层面上拥有块元素的效果

==浮动会造成父级元素框塌陷及改变网页大小出先重新排版的情况==

解决方法:

​	1.增加父级标签的边框高度

​	2.在父级标签中增加一个空的div标签，清除标签左右浮动，该空div标签会以块元素的形式置于父		级标签的底部，因为此div没有设置大小，所以刚好可以解决父级边框塌陷的问题

​	3.在父级标签中添加***overflow: hidden;***属性

​	4.父类添加一个伪元素==（推荐）==

```css
#father :: after{
    content:'';
    display:block;
    clear:both;
}
```

- overflow属性：

| 属性值  | 说明                                                 |
| :-----: | ---------------------------------------------------- |
| visible | 默认值，内容不会被修剪，会呈现现在边框之外           |
| hidden  | 内容会被修剪，并且其余内容是不可见的                 |
| scroll  | 内容会被修剪，但浏览器会显示滚动条以便查看其余内容   |
|  auto   | 如果内容被修剪，则浏览器会显示滚动条以便查看其余内容 |

## 6.定位

###6.1.相对定位

​	***position: relative;*** (只写此语句就是将位置固定于原处)

​	top: -20px;  新位置顶部相对于原位置顶部==上移超出原位置顶部20px==

​	left: 20px;  新位置左侧==相距原位置左侧==20px(向右移动)

​	bottom: 20px;  新位置下侧==相距原位置下侧==20px(向上移动)

​	right: -20px;  新位置右侧相对于原位置右侧==右移超出原位置右侧==20px

==相对与原来的位置偏移,超出原位置即为负，相对定位依然在标准文档流中，原来的位置会被保留==

### 6.2.绝对定位

​	***position: absolute;***

​	left: 20px; 

​	bottom: 20px;  ==距离浏览器左侧20px，底部20px。固定在网页的确定位置上==

1.父级标签没有定位时，相对于浏览器定位

2.父级标签存在定位，相对于父级标签进行定位

3.在父级标签内部定位

### 6.3.固定定位

​	poxition: fixed;

​	right: 0px;

​	bottom: 0px;  ==相对于显示器屏幕定位，位于屏幕右下角==

###6.4.z-index

当有多个标签重叠时显现底层标签内容:

- z-index: 999;  为底层标签设置此属性，将底层标签层级变高

- cpacity: 0.5;  将设置此属性的标签变透明

#三.JavaScript

- 功能: 可以增强用户和html页面的交互过程，可以控制html元素，让页面有动态效果。

- JavaScript发展史: 

  ​	1992年，Nombase公司，开发出第一门客户端脚本语言，专门用于表单的校验。命名为:C--，后更名为: ScriptEase

  ​	1995年，Netscape(网景)公司开发出一门客户端脚本语言: LiveScript。后来请sun公司专家修改，命名为JavaScript

  ​	1996年，微软抄袭了JavaScript开发出JScript语言

  ​	1997年，ECMA(欧洲计算机制造商协会)，制定出客户脚本语言标准: ECMAScript，统一了所有客户脚本语言的编码方式

  ==JavaScript = ECMAScript + JavaScript自己特有的东西(BOM + DOM)==

##1.ECMAScript (客户端脚本语言的标准)

### 1.1基本语法

1. 与html的集合方式

```html
<body>
    <!-- 内部 -->
    <script type="text/javascript">脚本语言</script>
    
    <!-- 外部 -->
    <script src="js文件路径" type="text/javascript" charset="utf-8"></script>
    <!-- script标签可以写在任意位置，script标签执行的时间根据标签位置决定,script标签可定义多个-->
</body>
```

2. 数据类型

   ​	1.原始数据类新(基本数据类型)：

   ​		  1.number: 	数字。 整数/小数/NaN(not a number  一个不是数字的数字类型)

   ​		  2.string:	字符/字符串

   ​		  3.boolean:	true/false

   ​		  4.null:	一个对象为空的占位符

   ​		  5.undefined:	未定义。如果一个变量没有初始化值，则会默认赋值为undefined

   ​	2.引用数据类型：object 对象

3. 变量:  ==js是弱类型语言，在开辟变量储存空间时，不定义空间将来存什么数据类型，可以存放任意类型数据==

   ​	var 变量名 [= 初始化值];  ==可定义同名变量，但后一个会覆盖前一个==

   ​	typeof(变量名);  ==可用于查看此变量储存的数据类型==

4. 运算符

   ​	1.正负号算符：在JS中，如果运算数不是运算符所要求的类型，那么js引擎会自动将算数进行类型转换。若字符串中的值不能转换为一个数字，则会转为NaN，NaN和任意数运算都为NaN。字符串类型的与数值类型相加为字符串拼接。true转为1，false转为0。Number()函数转化类型有同样效果，Boolean()函数会根据变量有无值返回true和false
​	2.比较运算符：字符串与字符串之间的比较是通过每个字母的ASCII码值来比较，数字与字符串比较则会将字符串转为数字进行比较，“===”，全等于，在比较之前先判断数据类型，若类型不同则直接返回false，switch结构体中使用的判断就是全等于

​	3.逻辑运算符：若逻辑运算符两侧的数据不为boolean，则自动转换为boolean ==&和|返回的是数值0和1，在if判断中会进行隐式的转换，作为逻辑判断使用时没有影响==
   ​		其他类型转boolean:

   ​			1.number：0或NaN为假，其他为真

   ​			2.string：除了空字符串("")，其他都是true

   ​			3.null和undefined：都为false

   ​			4.对象数据类型：都为true

### 1.2基本对象

1. Function

```html
<script>
	/*
		1.创建:
			1.function 方法名(形式参数列表){
				  方法体	
			  }
			2.var 方法名 = function(参数列表){
				  方法体
			  }
			3.var 方法名 = new Function(参数列表,"函数执行体");
		2.调用:
			方法名(参数列表);
		3.属性:
			方法名.length  代表参数的个数
		4.特点:
			1.方法是一个对象;如果定义名称相同的方法会覆盖
			2.方法调用只与方法名称有关，和参数列表无关，调用方法时可以传入与声明时数量不一致的参数
			3.在方法声明中有一个隐藏的内置对象(数组),arguments,封装所有的实际参数;可以通过arguments[i]获取传入方法中的参数
			4.若函数没有返回值则返回undefined
			5.函数调用只有加了()才是函数调用，只写方法名是指该函数的引用变量，打印此变量会将函数的内容以字符串的形式打印；所以将函数作为参数传递时只能写方法名，若加()则是传递了该函数的返回值
			  示例：
			  	function test(obj){
			  		obj();
			  	}
			  	test(function(){
			  		alert("开发中常用传递函数的方法(匿名写法)");
			  	});
	 */
    
    //创建
    function fun(a,b){  //参数和返回值的数据类型都是var，可以不用写
        alert(a+b);
        return a+b;
    }
    //调用
    document.write(fun(1,2));
</script>
```

2. Array

```html
<script>
	/*
		1.创建:
			1.var arr = new Array(元素列表);
			2.var arr = new Array(默认长度);
			3.var arr = [元素列表];
			4.var arr = new Array([5,6]); 创建一个第一个元素是数组的数组
		2.特点:
			数组元素类型可变，随便放任意类型数据
			数组长度可变，访问时，若下标越界，不会报异常而是打印undefined
			可以通过length改变数组长度，若超过原数组，则超出部分用空填充，小于原数组则截取原数组超出部分
		3.方法:
			1.join("分隔符"): 将数组中的元素按照指定的分隔符拼接为字符串
			2.push(参数列表): 向数组尾部添加元素并返回新的长度
			3.concat(Array... arr): 将数组合并返回一个新数组
			4.pop(): 移除最后一个元素并将其返回
			5.shift(): 移除第一个元素并返回
			6.unshift(): 从数组起始位置插入数据并返回新长度
			7.splice(start,deletecount[,item1,item2]): 从指定位置开始删除指定数量的元素从删除位置开始添加元素，并返回被删除的元素
		4.遍历:
			for(var i in arr){}
	*/
</script>
```

3. Date

```html
<script>
	/*
		1.创建:
			var date = new Date();
		2.方法:
			toLocalString();  返回当地格式的时间字符串
			getTime();  返回毫秒值。返回1970年1月1日零点到当前时间的毫秒值之差
	*/
</script>
```

4. Math

```html
<script>
	/*
		1.创建:
			不用创建，直接使用: Math.方法名()
		2.方法:
			random();  返回(0,1]之间的随机数
			ceil(x);  向上取整
			floor(x);  向下取整
			round(x);  四舍五入
		3.属性:
			PI  圆周率
	*/
</script>
```

5. RegExp   正则表达式对象

   1.定义字符串的组成规则

   ​	1.单个字符：[]

   ​		eg:[a]	[ab]: a或b	[a-zA-Z0-9_]: 范围内任意一个字符

   ​		特殊符号代表的单个字符:

   ​			\d: 单个数字字符 = [0-9]

   ​			\w: 单个单词字符 = [a-zA-Z0-9_]

   ​	2.量词符号
   ​		?： 表示出现0次或1次
   ​		*：表示出现0次或多次
   ​		+： 表示出现1次或多次
​		{m,n}：表示 m <= 字符数量 <=n，如果为{,n}则表示最多n次，为{m,}则表示最少m次

```html
<script>
	/*
		1.创建
			1.var reg = new RegExp("正则表达式");
				eg:	var reg = new RegExp("\\w{6,12}")
			2.var reg = /^正则表达式$/;  可添加^表示表达式的开始，$表示表达式的结尾
		2.方法
			1.test("字符串");  验证指定字符串是否符合正则定义的规范，返回boolean
	*/
</script>
```

6. Global

```html
<script>
	/*
		特点:全局对象，这个Global中封装的方法不需要对象就可以直接调用:方法名()
		方法:
			encodeURI(var): URL编码
			decodeURI(var): URL解码			
			encodeURIComponent(var): URL编码,会将更多的字符进行编码
			encodeURIComponetn(var): URL解码
			parseInt(var):从第一个字符开始逐一判断字符是否符合数字的形式，直到遇到不符合的，将之前符合全部转换；若没有符合的直接返回NaN
			isNaN(var): 判断一个值是否为NaN
			eval(var): 将字符串解析，转换为JavaScript代码执行，例如将"12+5"的字符串转化为12+5直接计算出结果
	*/
</script>
```

7.自定义类

```html
<script>
	/*
		function Person(name,age){
			this.name=name; 声明属性
			this.age=age;
			this.test1=function(a){ 声明函数
				alert(a);
			}
		}
		function User(uname,pwd){
			this.uname=uname;
			this.pwd=pwd;
		}
		
		Person.prototype.test2=function(){
			alert("嘿嘿");
		}
		Person.prototype.user=new User();
		User.prototype.testU=function(){
			alert("user");
		}
		prototype(原型)关键字：通过该关键字声明的属性或方法为所有实例对象的公共部分，类似于java静态属性，声明方法通常使用该关键字将其声明到公共空间来节省内存
		
		使用类: var person1 = new Person("张三",18);
			   var person2 = new Person("李四",19);
			   alert(person1.test1===person2.test1);  false
			   alert(person1.test2===person2.test2);  true
			   person1.user.testU();
			   person1.address="山西省";
			   alert(person1.address);  类的声明时不需要声明变量即可使用，创建类的对象后可临时为对象添加属性，获取对象中不存在的属性时会得到undefined。js中对象的属性内容可以自己扩充，类中声明的只是公共属性
	*/
</script>
```

8.自定义对象

```html
<script>
	/*
		1.var 变量名 = new Object();  对象实例
		  变量名.属性名 = 值；	为对象设置属性
		  变量名.方法名 = function(){}	为对象设置函数
		  该方法创建出来的对象不具备原型空间
		2.var 变量名 = {
			属性名: 值,
			属性名: 值,
			函数名: function(){}
		}
		使用对象是因为很多时候不清楚类有哪些属性，所以只能临时创建一个对象来自定义属性存储数据
	*/
    var obj = new Object();
    obj.name = "武建琦";
    obj.age = 20;
    obj.fun = function(){
        alert("姓名:" + this.name + ",年龄:" + this.age);
    }
    //调用
    alert(obj.age);
    obj.fun();
    //调用的时候同样可以临时为对象添加属性或函数
</script>
```



##2.JavaScript

###2.1BOM

1. 概念：Browser Object Model 浏览器对象模型

​	将浏览器的各个组成部分封装成对象

2. 组成

   Window:	窗口对象

   History:	历史记录对象

   Location:	地址栏对象

   Navigator:	浏览器对象

   Screen:	显示器屏幕对象

####2.1.1 BOM_Window

```html
<script>
	/*
		1.方法
			scrollTo(xpos,ypos) 滚动到指定坐标位置，0，0为滚动到顶部
			1.与弹出有关的方法:
				alert(); 显示带有一段消息和一个确认按钮的警告框
				confirm(); 显示带有一段消息以及确认按钮和取消按钮的对话框，返回boolean
				prompt("提示信息"); 显示可提示用户输入的对话框，返回用户输入值，没有输入点击确定返回"",点击取消返回null
			2.与打开关闭有关的方法
				open("窗口地址"[,"打开方式(newwindow)","height=400,width=400,top=0,left=0,toolbar=no,menubar=no,scrollbars=no,resizable=no,location=no,status=no"]); 打开新的浏览器窗口，返回新的浏览器对象
				close(); 关闭调用此方法的浏览器对象
				opener.父页面方法; 可通过该属性在有open方法打开的子页面调用open方法所在页面(父页面)中的方法
			3.与定时器有关的方法
				setTimeout(); 在指定毫秒数后调用函数，会返回一个该计时器的对象
					参数:
						1.js代码或方法对象
						2.毫秒值
				clearTimeout(计时器对象); 取消由setTimeout方法设置的timeout，可传入参数来关闭指定的计时器
				setInterval(); 按照指定的周期(以毫秒计)来调用函数，同样返回该间隔器的对象
				clearInterval(间隔器对象):取消周期定时器
		2.属性
			1.获取其他BOM对象:
				history
				location
				navigator
				screen
			2.获取DOM对象
				document
			3.top：返回最顶层页面
		3.特点
			Window对象不需要创建可以直接使用  window.方法名();
			window可以省略。 方法名();
	*/
</script>
```

####2.1.2 BOM_Location

```html
<script>
	/*
		1.创建
			window.location
			location
		2.方法
			reload(); 重新加载当前文档。刷新
		3.属性
			href 设置或返回完整的URL，设置URL执行效果为跳转
			search  获得当前请求url从?开始的内容，即请求参数
			注：设置href属性location默认为在此页面修改地址栏并跳转。window的top属性表示回到上一个url，常用于frameset框架中的页面，因为使用frameset制作的页面各自是独立的，所以在某一个frameset页面中设置href属性跳转时只会修改这一个页面的地址栏并跳转，想要整个页面实现跳转就需要先设置top属性返回上一个不是frameset的页面再修改地址栏：window.top.location.href="访问路径"
	*/
</script>
```

####2.1.3 BOM_History

```HTML
<script>
	/*
		1.创建
			window.history
			history
		2.方法
			back()	加载history列表中的前一个URL
			forward()	加载history列表中的下一个URL
			go()	加载history列表中的某个具体页面
				参数: 若为正数表示前进多少个历史记录，为负数则后退多少个历史记录，为0相当于刷新当前页面
		3.属性
			length	返回当前窗口的历史列表中的URL的数量
	*/
</script>
```

####2.1.4BOM_Screen

```html
<script>
	/*
		1.创建
			window.screen
			screen
		2.属性
			width  获取屏幕的宽度分辨率
			height 获取屏幕的高度分辨率
	*/
</script>
```



###2.2DOM

1.概念: Document Object Model 	文档对象模型

​	当浏览器加载标记语言文档时，将标记语言文档的各个组成部分(标签)，封装为对象。可以使用这些对象，对标记语言文档进行CRUD的动态操作

2.组成:

​	核心	DOM - 针对任何结构化文档的标准模型

​	XML	DOM - 针对XML文档的标准模型

​	HTML	DOM - 针对HTML文档的标准模型

####2.2.1核心DOM模型

​	1.Document:	文档对象

```html
<script>
	/*
		1.创建:
			window.document
			document
		2.方法:
			获取Element对象
				1.getElementById()			根据id属性获取元素对象。
				2.getElementsByTagName()	根据标签名称获取元素对象们，返回值是一个数组
				3.getElementsByClassName()	根据class属性获取元素对象们。返回值是数组
				4.getElementsByName()		根据name属性值获取元素对象们。返回值是数组
				以上都是从浏览器创建的document对象中封装的内容获取的，而不是html中
			创建其他DOM对象
				createAtrribute()
				createComment()
				createElement("标签")
				createTextNode("文本内容")	将文本内容转变为一个节点，调用appenChild()添加内容到指定标签中
	*/
</script>
```

​	2.Element:	元素对象 / 标签对象

```html
<script>
	/*
		1.创建
			通过document创建
		2.方法
			1.removeAttribute("属性")	删除标签属性
			2.setAttribute("属性","属性值")	设置标签属性，可以设置自定义和固有的的属性
			3.getAttribute("属性")   可以获取到自定义和固有的属性值，在获取input标签的value属性的值时，只能获取到最初封装到input对象中的值，而不能获取到用户实时输入的值，若最初没有设置标签的值，则默认为""
		3.属性
			js将html中的所有标签都封装到了document对象中，每个标签对象所封装的属性都是html标签所标记的属性；例如id，name，value，type，可通过这些属性名直接获取到它所对应的值，也可以使用=为属性直接赋值，但尽量不要修改id和name，会影响到很多操作。此外，这种方式获取自定义属性时会得到undefined，对于input标签可以获得用户实时输入的值
			例:
				var inp = document.getElementById("uname");
				alert(inp.type+"_"+inp.name+"_"+inp.id+"_"+inp.value);
				因为这些对象都是js对象，所以即使对象中没有封装的属性也可以获取，只是结果为undefined
	*/
</script>
```

​	3.Node:	节点对象，间接获取document中封装的属性

```html
<script>
	/*
		特点:	所有dom对象都可以被认为是一个节点
		方法:
			CRUDdom树(增删改查)
				appendChild(子节点的对象引用)	向节点的子节点列表的结尾添加新的子节点
				removeChlid("子节点对象引用")	删除并返回当前节点的指定子节点
				replaceChlid()	用新节点替换一个子节点
		属性:
			parentNode	获取当前节点的父节点
			childNodes  获取当前节点的所有子节点
			previousSibling  获取当前节点同级的前一个节点
			nextSibling  获取当前节点同级的后一个节点
		注：在html中，标签之间的回车也算一个节点，即文本节点
	*/
</script>
```

4. TEXT：文本对象
5. Comment：注释对象
6. Attribute：属性对象

####2.2.2HTML_DOM_innerHTML

```html
<script>
	/*
		HTML DOM属性是针对关于如何获取、修改、添加或删除HTML元素的标准
		innerHTML	获取目标标签对象的文本内容，包括其中的标签；回车符号以及制表符等在网页中无法常显示的内容，可通过"=","+="进修内容的修改和追加,可以添加标签，文字
		innerText   获取目标标签对象的文本内容，不包括html标签。添加html标签时不会被解析
	*/
</script>
```

####2.2.3HTML_DOM_控制样式

```html
<head>
    <style>
        .d1{
            border:1px solid red;
            width: 100px;
        }
    </style>
</head>
<script>
	/*
		1.标签对象.style.css样式 = 样式值。原理为js将style中的css样式也封装到style对象中，对象中的css属性名是原css中的属性名将-去掉改为驼峰命名
		2.提前定义好类选择器的样式，然后通过对象的className属性将标签对象的class设为类选择器的名称,将值设为""就是删除
	*/
    var div1 = document.getElementById("div1");
    div1.onclick = function(){  //点击ID为div1的标签后使其改变样式
        div1.style.border = "1px solid red";
    }
    
    var div2 = document.getElementById("div2");
    div2.onclick = function(){
        div2.className = "d1";
    }
</script>
```

#### 2.2.4DOM操作表单

```html
<script>
	/*
		1.获取表单对象
			var form = document.getElementById("id");
			var form = document.name属性值
		2.获取form下的所有表单元素对象数组，不包含非表单标签
			表单引用变量.elements
		3.form表单常用方法
			表单引用变量.submit()  提交表单，可用于对普通按钮绑定事件，在事件中调用此方法就可达到普通按钮提交表单
			表单引用变量.reset()  对普通按钮绑定事件，在事件函数中调用此方法可是普通按钮达到重置表单的效果
        4.下拉框操作
        	alert(document.getElementById("下拉框id").value);  在onchange事件绑定的函数中使用此语句，当下拉框被选择时，可以获得被选中的下拉框的值
        	document.getElementById("下拉框id").options;  获取所有option标签对象，返回一个数组，可通过判断这些option标签的selected属性是否为true找到被选中的下拉框
	*/
</script>
```



####2.2.5事件监听机制

1.概念: 某些组件被执行了某些操作后，出发某些代码的执行

2.事件：某些操作。如：单击，双击，鼠标移动，键盘按下

3.事件源:	组件。如：按钮，文本输入框

4.监听器：代码

5.注册监听：将事件，事件源，监听器结合在一起。当事件源上发生了某个事件，则触发执行某个监听器的代码（JS函数）。

6.事件注册:

​	1.静态注册:	通过html标签的事件属性直接赋予事件响应后的代码
​	2.动态注册:	通过js代码得到标签的dom对象，然后通过dom对象.事件名 = function(){}赋予事件响应后的代码

7.常见事件:

​	1.点击事件:

​		1.onclick:	单击事件

​		2.ondblclick:	双击事件

​	2.焦点事件:

```html
<script>
	/*
		1.onblur:	元素失去焦点
			*一般用于表单校验: 当输入框失去焦点时，在文本框后显示"√"或其他提示符
		2.onfocus:	元素获得焦点
	*/
    document.getElementById("username").onblur = function(){
        alert("失去焦点了");
    }
    //当Id为usernam的标签失去焦点时执行弹出窗口
</script>
```

​	3.加载事件:

```html
<script>
	/*
		onload:	一张页面或一幅图像完成加载
			*一般直接为window对象添加此事件
	*/
    window.onload = function(){
        //等待页面加载完毕后执行此函数
    }
</script>
```

​	4.鼠标事件:

```html
<script>
	/*
		1.onmousedown	鼠标按钮被按下
			定义函数时，定义一个参数接受event对象，event对象的button属性可以获取是哪一个鼠标按键
		2.onmouseup		鼠标按键被松开
		3.onmousemove	鼠标被移动
		4.onmouseover	鼠标移到某个元素之上
		5.onmouseout	鼠标从某元素移开
	*/
    document.getElementById("username").onmousedown = function(event){
        alert(event.button);
    }
</script>
```

​	5.键盘事件:

​		1.onkeydown	某个键盘按键被按下

​		2.onkeyup		某个键盘按键被松开

​		3.onkeypress	某个键盘按键被按下并松开

​	6.选中和改变

​		1.onchange	域的内容被改变，常用于下拉框

​		2.onselect		文本被选中

​	7.表单事件

​		1.onsubmit	确认按钮被点击

事件的函数可以定义返回值，该事件的函数若返回值为true或没有则会提交表单，反之。对超链接绑定的事件函数返回值为false可以取消其**默认行为**，即执行过函数后，取消页面跳转

```html
<body>
    <!--关于拒绝提交表单的两种写法-->
    <form action = "#" id = "form" onclick = "checkForm();">
    <!--此方法拒绝失败，正确写法:	onclick = "return checkForm()"-->
    <input id = "username" name = "username">
        <select id = "city">
            <option>北京</option>
            <option>上海</option>
        </select>
    </form>
    
    <script>
    	document.getElementById("form").onsubmit = function(){
            return false;
        }
        //拒绝成功
        
        function checkForm(){
            return false;
        }
    </script>
</body>
```

​		2.onreset		重置按钮被点击


#四.XML

- 概念：Extensible Markup Language 	可扩展标记语言

  ​	可扩展：标签都是自定义的

- 功能：存储数据

  ​				1.配置文件
  ​				2.在网络中传输

- xml与html的区别：

  ​	1.xml标签都是自定义的，html标签是预定义
  ​	2.xml语法严格，html语法松散
  ​	3.xml是存储数据的，html是展示数据的

- 基本语法：
           1.xml文档的后缀名为  .xml
           2.xml第一行必须定义为文档申明
           3.xml文档中有且仅有一个根标签
           4.属性值必须使用引号（单双都可以）
           5.标签必须正确关闭
           6.标签名称区分大小写

- 组成部分:

  1. 文档声明：

     1. 格式：<?xml 属性1="属性值" 属性2="属性值" ?>

     2. 属性列表：
        1. version：版本号，必须属性，主流版本号为1.0
        2. encoding：编码方式。告知解析引擎使用何种编码方式解析
        3. standalone：是否独立，值为yes/no。依赖/不依赖其他文件，大多数时候不设置

  2. 标签：标签名称自定义

     - 规则：
       1. 名称可以包含字母，数字及其他字符
       2. 名称不能以数字或标点符号开始
       3. 名称不能以字母xml（或XML,Xml等）开始
       4. 名称不能包含空格

  3. 属性
     id属性值唯一

  4. 文本

     - CDATA区：在该区域中的数据会被原样展示，"<",">","&"等特殊符号不需要使用html中的方法转义

     - 格式：<![CDATA[

       ​				要展示的内容	
       ​			]]>

- 约束：DTD  Document Type Defination，用于约束这个xml文档定义的标签名称

     使用：

     1. 内部DTD
        定义语法：<!DOCTYPE 根元素标签 [元素声明]>
        元素声明语法：<!ELEMENT 元素名 (子元素[,子元素...])>

        <!ELEMENT 元素名 (#PCDATA)>    表示这个标签内部只能是文本，不可以写其他标签

        数量词：

        - +：出现一次或多次
        - ?：出现一次或零次
        - *：出现任意次

        属性声明语法：<!ATTLIST 元素名称 属性名称 属性类型 默认值> 
        属性类型：常用为CDATA，表示字符类型
        默认值：#REQUIRED  属性值为必须的
                       \#IMPLIED   属性值为非必须的
                       \#FIXED value   属性值为固定的

     2. 外部DTD

        1. 创建一个独立的DTD文件
        2. 在DTD文件中写约束规则，不需要写<!DOCTYPE 根元素名称[]>标签
        3. 在xml中引入外部DTD：<!DOCTYPE 根元素名称 SYSTEM "DTD文件名">

     > 示例

     ```xml
     <?xml version="1.0" encoding="utf-8"?>
     <!--内部声明DTD-->
     <!DOCTYPE scores [
     	<!ELEMENT scores (student+)>
     	<!ELEMENT student (name,course,score)>
     	<!ATTLIST student id CDATA #REQUIRED>
     	<!ELEMENT name (#PCDATA)>
     	<!ELEMENT course (#PCDATA)>
     	<!ELEMENT score (#PCDATA)>
     ]>
     <!--
     	导入外部DTD约束
     	<!DOCTYPE score SYSTEM "score.dtd">
     -->
     
     <scores>
     	<student id="1">
         	<name>张三</name>
             <course>java</course>
             <score>57</score>
         </student>
         <student id="2">
         	<name>李四</name>
             <course>xml</course>
             <score>66</score>
         </student>
     </scores>
     ```

     创建名为score.dtd的DTD文件：

     ```dtd
     <?xml version="1.0" encoding="utf-8"?>
     <!--外部声明DTD-->
     <!ELEMENT scores (student+)>
     <!ELEMENT student (name,course,score)>
     <!ATTLIST student id CDATA #REQUIRED>
     <!ELEMENT name (#PCDATA)>
     <!ELEMENT course (#PCDATA)>
     <!ELEMENT score (#PCDATA)>
     ```

     

- 解析：将文档中的数据读取到内存中
  解析方式：
  1. DOM：将标记语言文档一次性加载进内存，适用于服务器端，及多次访问xml
     - 优点：操作方便，可以对文档进行CRUD的所有操作
     - 缺点：占内存
  2. SAX：逐行读取，基于事件驱动，适用于移动端，及数据量较大的xml
     - 优点：不占内存
     - 缺点：只能读取，不能增删改
  3. JDOM解析：第三方提供开源免费的解析方式
  4. DOM4J解析：是JDOM的升级版
  
  > 示例：解析xml文件
  
  ```java
  //导入dom4j-1.6.1jar包
  public static void main(String[] args){
      //创建SAXReader对象
      SAXReader reader = new SAXReader();
      //获取xml文件Document对象
      Document doc = reader.read(new FIle("xml文件路径"));
      //获取根元素
      Element root = doc.getRootElement();  //root.getName()获取该元素的名字
      //获取根元素下一级子元素
      Iterator<?> it = root.elementIterator();
      while(it.hasNext()){
          Element e = (Element)it.next();
          //获取元素属性 e.attributeIterator()可获取属性迭代器
          Attribute id = e.attribute("id"); //参数可以是属性的名字，也可以是属性的位置
          System.out.println(id.getName()+"="+id.getValue());
          //获取子元素
          Element name = e.element("name");
          Element course = e.element("course");
          Element score = e.element("score");
          //获取标签文本
          System.out.println(name.getName()+"="+name.getStringValue());
          System.out.println(course.getName()+"="+course.getText());
      }
  }
  ```
  
  > 示例：生成XML文件
  
  ```java
  public static void main(Stirng[] args){
      //通过DocumentHelper生成一个Document对象
      Document doc = DocumentHelper.createDocument();
      //添加并得到根元素
      Element root = doc.addElement("books");
      //为根元素添加子元素
      Element book = root.addElement("book");
      //为book添加属性,同样会返回一个子元素对象可以使用链式编程
      book.addAttribute("id","1");
      //为book添加子元素
      Element name = book.addElement("name");
      Element author = book.addElement("author");
      Element price = book.addElement("price");
      //为子元素添加文本
      name.addText("java core");
      author.addText("张三");
      price.addText("88");
      //将doc输出到xml文件中
      Writer writer = new FileWriter(new File("src/book.xml"));
      doc.write(writer); //简单的将内容输出为一行
      //关闭资源
      writer.close();
      /*
      	美化格式输出
      	OutputFromat format = OutputFormat.createPrettyPrint();
      	XMLWriter writer = new XmlWriter(new FileWriter(new File("src/book.xml")),format);
      	writer.write(doc);
      	writer.close();
      */
  }
  ```
  
  

#五.Web

##1.Servlet（动态资源）

- 概念：运行在服务起端的小程序，Servlet就是一个接口，定义了java类被浏览器访问到（tomcat识别到）的规则。将来写的java类实现此接口就可以被浏览器识别。tomcat中保存Servlet的容器叫Catalina，手动发布项目时，可在tomcat这个目录下定义xml文件，定义标签<Context docBase="项目路径"/>来发布项目，xml文件名即为虚拟目录名。

- 快速入门：

  1. 创建javaEE项目

  2. 定义一个类，实现Servlet接口

  3. 实现接口中的方法
     - void init(ServletConfig servletConfig)
     - ServletConfig getServletConfig()
     - void service(ServletRequest servletRequest,ServletResponse servletResponse)
     - String getServletInfo()

  ==默认情况下Servlet在第一次被访问时创建，可以配置执行Servlet的时机。
  Servlet的init方法只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单列的，多个用户同时访问时会出现线程安全问题
      解决：尽量不要在Servlet中定义成员变量，而是定义在service方法中，使其不在共享。即使定义了也不要对其进行修改值==

- 配置Servlet

  1. 在项目中配置web.xml
     <servlet>

     ​	<servlet-name>servlet名称（任意即可）</servlet-name>

     ​	<servlet-class>servlet的全类名</servlet-class>
      </servlet>
     <servlet-mapping>
     ​	<servlet-name>servlet名称（与上一标签一致）</servlet-name>
     ​	<url-pattern>/servlet<url-pattern>

     </servlet-mapping>

  2. servlet3.0配置：

     1. 创建javaEE项目，选择Servlet的版本3.0以上，可以不创建web.xml
     2. 定义一个类，实现Servlet接口
     3. 复写方法
     4. 在类上使用@WebServlet注解，进行配置
     
     ```markdo
     注解只需要设置url的值即可，urlPatterns属性是String[]，所以可以定义多个url来访问资源
         url定义规则：
             1./xxx
             2./xxx/xxx  多层路径，目录结构
             3.*.后缀名
         例：
             1.@WebServlet(urlPatterns="/demo","/bbb","/aaa")
             2.@WebServlet(urlPatterns="/user/demo")
             3.@WebServlet(urlPatterns="/demo/*")    访问时，输入/demo/任意字符串，即可访问
             4.@WebServlet(urlPatterns="*.do")
     ```
     
     

- 执行原理：
  1. 当服务器接受到客户端浏览器的请求后，会解析请求的URL路径，获取访问的Servlet的资源路径
  2. 查找web.xml文件，是否有对应的<url-pattern>标签体内容,若没有则404
  3. 如果有，则找到对应的<servelt-class>全类名
  4. tomcat会通过反射将字节码文件加载进内存，并且创建其对象，若类名有误则无法通过反射创建该对象，报类名找不到异常（500异常）
  5. 调用该Servlet类中的service方法，若Servlet没有重写该方法则调用父类的service方法

- 关于为什么访问项目时url只写到**"虚拟目录"/**会访问到index.jsp：
  在tomcat中的web.xml中对于发布到该服务器中的所有项目有一个配置，其servlet标签定义了**"/"**的<url-partten>，其对应的的servlet名为default，访问的类为“org.apache.catalina.servlets.DefaultServlet”，它会去找定义在<welcome-file-list>标签中定义的资源，即index.jsp/html。我们自己在创建web项目时，若采用web.xml配置，则此文件中也会默认创建该标签并定义默认访问资源。当我们项目中由该标签时，访问**"虚拟目录/"**时会访问项目的index.jsp，若将项目中的该标签删去则会访问tomcat中定义的index.jsp

- web.xml中的标签定义没有顺序，但加载时有顺序：ServletContext->context-param->listener->filter->servlet

> 详情查询黑马P684，尚学堂P593，P617

## 2.GenericServlet

Servlet接口的子类，将Servlet接口中的其他方法做了默认空实现，只将service()方法作为抽象方法，将来定义Servlet类时，可以继承GenericServlet，实现service()方法即可，也可以重写其他方法，简化代码。**但不常用**

##3.HttpServlet

GenericServlet抽象类的子类，该类的service方法通过req.getMethod获取网页的请求方式的字符串，然后判断这个字符串，调用指定的方法。在定义Servlet类时继承此抽象类，重写doGet和doPost方法。因为service方法是web程序的入口，而tomcat通过反射创建出的Servlet对象中没有service方法，所以会调用父类的service方法，HttpServlet类的service方法会根据不同的请求类型调用不同的方法，而doGet和doPost方法因为被重写所以会调用重写的doGet和doPost；同时也可以重写service方法，定义一个类重写service方法，可重新定义方法的访问依据；同时重写这三个方法，tomcat会优先调用service方法，如果在重写的service方法中写==super.service(request,response)==则会在执行完service方法后根据请求方法调用Servlet中的doGet或doPost方法，因为父类的service方法中进行了请求方式的判断，若Servlet中没有写这些方法就会报405==推荐==

> 重写service方法，重新定义方法访问依据

```java
/*
	用于将原来多个处理同种业务的Servlet类写为多个方法抽取到一个Servlet类中，以达到简化项目项目结构，例如将所有操作User的Servlet抽取为一个Servlet类，通过请求该Servlet类中不同的方法达到请求原来不同Servlet的效果
	思路：通过定义一个类继承HttpServlet重写service方法，在该方法中将请求路径中最后一个表示要访问什么方法的字符串分离出来，通过反射调用子类中的该方法即可，若url中最后的方法未被定义在子类中，则反射调用时会找不到该方法，抛出异常。也可以改为重写doGet和doPost方法，这样还可以保留父类service方法中根据不同请求方法分发方法的功能
*/
public class BaseServlet extends HttpServlet{
    @Override
    protected void service(HttpServletRequest req,HttpServletResponse resp)throws ServletException{
        String uri = req.getRequestURI(); //获取请求URI
        String methodName = uri.substring(uri.lastIndexOf('/')+1); //分离出方法名
        try{
            //通过反射获取到请求中要访问的方法
        	Method method = 
          this.getClass().getMethod(methodName,HttpServletRequest.class,HttpServletResponse.class);
          //此处this是子类对象，因为tomcat创建的是它所访问的Servlet类的实例，通过该类的实例调用service方法，而所访问的类没有重写所以才调用父类的service方法，也就是此处的service
        	//调用子类中的该方法
         	method.invoke(this,req,resp);
        }catch(NoSuchMethodException e){
            e.printStackTrace();
        }
    }
}
```

```java
//所有请求user类型资源的请求都会访问此Servlet
@WebServlet("/user/*")
public class UserServlet extends BaseServlet{
    public void add(HttpServletRequest req,HttpServletResponse rep) throws ServletException{
        System.out.println("UserServlet中的add方法");
    }
    public void find(HttpServletRequest req,HttpServletResponse rep) throws ServletException{
        System.out.println("UserServlet中的find方法");
    }
}
```

```java
@WebServlet("/dog/*")
public class DogrServlet extends BaseServlet{
    public void add(HttpServletRequest req,HttpServletResponse rep) throws ServletException{
        System.out.println("DogServlet中的add方法");
    }
    public void find(HttpServletRequest req,HttpServletResponse rep) throws ServletException{
        System.out.println("DogServlet中的find方法");
    }
}
```



#六.HTTP

概念：Hyper Text Transfer Protocol	超文本传输协议

1. 传输协议：定义了客户端和服务器端通信时发送数据的格式
2. 特点：
   1. 基于TCP/IP的高级协议
   2. 默认端口号：80
   3. 基于请求/响应模型：一次请求对应一次响应
   4. 无状态：每次请求之间相互独立，不能交互数据

3. 历史版本：

   - 1.0版本：每一次请求响应都会建立新的连接

   - 1.1版本：一次请求响应结束后不会马上断开，而是等待一会儿，若有新的请求则复用连接

##1.请求消息数据格式

1. 请求行
   请求方式	请求url	请求协议/版本
   GET		/login.html	HTTP/1.1
   请求方式：HTTP协议有7中请求方式，常用的有2中
- GET：请求参数在请求行中/url后
  
- POST：请求参数在请求体中
2. 请求头
   请求头名称:   请求头值
   常见请求头：
   1. User-Agent：浏览器告诉服务器访问使用的浏览器版本信息，可以在服务器端获取该头的信息，解决浏览器的兼容性问题
   2. referer：请求URL从哪里来，如果是通过超链接访问会获取到超链接所在的URL，如果从浏览器地址栏访问会获取到null。
      - 作用：
        1. 防盗链
        2. 统计工作
   
3. 请求空行：用于分割POST的请求头和请求体
  
4. 请求体
   封装POST请求消息的请求参数

##2.Request

1. request对象和response对象的原理
   1. request和response对象是由服务器创建的，我们来使用他们
   2. request对象是获取请求消息，response对象是来设置响应消息

3. request对象的继承体系结构：
   ServletRequest(接口)<---(extends)HttpServletRequest(接口)<---(implements)org.apache.catalina.connector.RequestFacade  (tomcat创建的类)

3. request对象功能：

   1. 获取请求消息数据

      1. 获取请求行数据

         例：GET /day14/demo1？name=zhangsan HTTP/1.1
         方法：

         1. 获取请求方式：String getMethod()	GET
         2. ==获取虚拟目录：String getContextPath()   /day14		在Servlet类中写虚拟目录要使用此方法动态获取而不是写死==
         3. 获取Servlet路径（资源名）：String getSeveletPath()  /demo1
         4. 获取get方法请求参数：String getQueryString()   name=zhangsan
         5. ==获取请求URI（虚拟路径+资源路径）：String getRequestURI()   /day14/demo1==
         6. 获取请求URL：StringBuffer getRequestURL()  http://localhost/day14/demo1
            ==URL和URI的区别：
                  URL：统一资源定位符
                  URI：统一资源标识符
            URL是URI的子集==
         7. 获取协议及版本号：String getProtocol()  HTTP/1.1
         8. 获取客户机的IP地址：String getRemoteAddr()

      2. 获取请求头数据
         方法：

         1. String getHeader(String name)    通过请求头名称获取请求头的值

         2. Enumeration<String> getHeaderNames()   获取所有的请求头名称
            遍历Enumeration：
            while(引用变量.hasMoreElements()){

            ​	String name = 引用变量.nextElement();
            ​	String value = request.getHeader(name);

            }
            可以通过判断请求的值是否包含chrom或firefox来确定是什么浏览器

      3. 获取请求体数据
         只有POST请求方式才会有请求体，在请求体中封装了POST请求的请求参数
         步骤：

         1. 获取流对象
            - BufferedReader getReader();	获取字符输入流，只能操作字符数据
            - ServletInputStream getInputStream();    获取字节输入流，可以操作所有类型数据
         2. 从流对象中拿数据
            使用方法和普通流相同

   2. 其他功能

      获取请求参数通用的方式（兼容GET和POST请求）

      - String getParameter(String name)	根据参数名称获取请求参数值
      - String[] getParameterValues(String name)    根据参数名称获取请求参数值的数组，用于复选框

      ==注：若获取的的指定参数名称在请求中不存在，则会返回null，而不是报错==

      - Enumeration<String> getParameterNames()   获取所有请求的参数名称
      - Map<String,String[]> getParameterMap()   获取所有请求参数的map集合
        - Set<T> keySet()	获取键（请求参数）的set集合
        - String[] get(String key)   获取值的数组(复选框的一个请求参数对应多个值)

==注：若表单提交的值有中文，POST提交方法会出现乱码。因为POST内部是通过流来获取的参数==
​		==解决方法：在获取参数前，设置Request对象的编码为setCharacterEncoding("utf-8")。若get请求出现乱码，需要去tomcat->config->server.xml找到\<connector>标签（8080端口配置处）添加URIEncoding="UTF-8"属性。二者之所以不同，是因为GET请求中的参数一旦传递到Servlet中就会使用默认的iso-8859-1解码，而POST请求的请求参数在请求体中，只有在获取参数时才会使用iso-8859-1解码，所以POST请求只需在获取前设置即可==

==解决方法二（通用get和post）：将获取到的参数值进行parameter = new String(parameter.getBytes("iso-8859-1"),"utf-8")，原理：getBytes会将字符串以参数的编码方式进行编码，而new String则会将参数的字节数组以指定的解码方式解码。因为HTTP要求请求必须是iso8859-1编码，所以浏览器在传输中文字符时会先对中文字符进行new String(parameter.getBytes("utf-8"),"iso-8859-1")，这样以乱码的iso8859-1进行传输，到达目的地后就可以用相反的方式进行编解码就可以得到正确的中文字符==

> **请求转发**：一种在服务器内部的资源跳转方式，常用于将一个复杂的Sevelet类任务分为多个单一的任务

步骤：

1. 通过Request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)   参数为另一资源路径
2. 使用RequestDispatcher对象的方法来进行转发：forward(ServletRequest request,ServletResponse response)。将当前servlet的request和response转发到另一servlet并执行doGet或doPost

特点：

1. 资源跳转后浏览器地址栏路径不发生变化
2. 只能转发当前服务器内部资源
3. 转发虽然访问的是多个资源，但却是一次请求，可以使用request对象来共享数据
4. 请求转发相当于方法调用，执行完请求转发后会回到原Servlet中执行后续代码，但通常没有后续代码

>  request对象共享数据

- 域对象：一个有作用范围的对象，可以在范围内共享数据
- request作用域：一次请求的范围
  方法：
  1. void setAttribute(String name,Object obj)	以键和值的形式存储数据到request对象作为共享数据
  2. Object getAttribute(String name)    通过键获取值，若没有对应的键则返回null
  3. void removeAttribute(String name)   通过键移除键值对

> request对象获取ServletContext

- ServletContext getServletContext()

> BeanUtils工具类

​	用于将**getParameterMap() **方法获取的请求参数集合封装成一个对象

​	工具类中操作的是JavaBean的==属性==而不是==成员变量==，即调用工具类中的方法传入JavaBean的属性时，只需传入getter或setter方法去掉get或set再转换为小写；根据get或set方法操作的成员变量进行设置，而与成员变量的名字没有直接关系
​	属性：setter和getter方法截取后首字母变为小写的产物
​		例如：getUserName()  -----> UserName ----->userName

类中的方法：

1. setProperty(Object obj,String name,Object value);
   为==参数对象==的==指定属性==设置==属性值==
2. getProperty();     获取属性值
3. populate(Object obj,Map map);
   将map集合中的键值对封装为指定的JavaBean对象

使用步骤：

1. 导入commons-beanutils-1.8.0.jar包
2. 使用getParameterMap()方法获取表单提交的参数和值
3. 创建一个对象
4. 使用BeanUtils封装:   BeanUtils.populate(对象名,map);

## 3.响应消息数据格式

1. 响应行
   协议/版本	响应状态码	状态码描述
   HTTP/1.1			200				OK
   响应状态码：服务器告诉客户端浏览器本次请求和相应的状态
   - 状态码都是3位数字
   
   - 分类：
     1.   1XX：服务器接受客户端消息，但没有接收完成，等待一段时间后，发送1XX多状态码
     2.   2XX：成功。代表码：200
     3.   3XX：重定向。
                 代表码：
                        302（重定向）
                        304（访问缓存：浏览器会将服务器上的资源进行缓存，若浏览器再次请求相同的资源时会发送304告诉浏览  器访问本地资源即可）
     4.   4XX：访问失败，客户端错误。
              代表码：
                      404（请求路径没有对应资源）
                      405（请求方式没有对应的doXXX方法）
     5.   5XX：访问失败，服务器端错误。代表码：500（服务器内部出现异常）
     
   - 在项目的web.xml中配置错误页面
   
     ```xml
     <error-page>
     	<error-code>404</error-code>
         <location>错误页面路径</location>
     </error-page>
     <!--配置完毕后，当项目抛出异常时，会根据指定的状态码跳转到此处设置的错误页面-->
     ```
2. 响应头
   头名称：值
   常见的响应头：
   1. Content-Type：服务器告诉客户端本次响应体数据格式以及编码格式
   2. Content-disposition：服务器告诉客户端以什么格式打开响应体数据
      - in-line：默认值，在当前页面内打开
      - attachment;filename=xxx：以附件形式（弹出提示框）打开响应体,filename为附件的名称
3. 响应空行
4. 响应体

##4.Response

功能：设置响应消息

1. 设置响应行

   - setStatus(int sc);   设置状态码

2. 设置响应头

   - setHeader(String name,String value)  ==设置响应头，头名相同会覆盖==
   - addHeader(String name,String value)  ==设置响应头，头名相同不会覆盖==

3. 设置响应体

   - 获取输出流：
     1. 字符输出流：PrintWriter getWriter();
     2. 字节输出流：ServletOutputStream getOutputStream();
     
     ==两种流不能同时使用==

4. 设置响应状态码
  
   - sendError(int sc,String message);

> 案例

1. 完成重定向

```java
//重定向，访问Demo1资源后会自动跳转Demo2资源
@WebServlet("/responseDemo1")
public class ResponseDemo1 extends HttpServlet{
    protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        System.out.println("Demo1被访问了");
        
        //设置状态码为302
        response.setStatus(302);
        //设置响应头location
        response.setHeader("location","虚拟目录/responseDemo2"); //除了路径都是固定写法
        /*
        	简单的重定向
        	response.sendRedirect("虚拟目录/responseDemo2");
        */
    }
    protected void doGet(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        this.doPost(request,response);
    }
}

@WebServlet("/responseDemo2")
public class ResponseDemo2 extends HttpServlet{
    protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        System.out.println("Demo2被访问了");
    }
    protected void doGet(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        this.doPost(request,response);
    }
}
```

重定向的特点：

1. 地址栏发生变化
2. 重定向可以访问其他服务器的资源
3. 重定向是两次请求
4. 因为请求转发的地址栏不会改变，所以每当刷新一次页面就会执行一次请求，造成表单数据多次提交，而重定向则可以解决多次请求的问题
5. 不能访问WEB-INF下的资源

路径写法：

- web中"/"表示的意义
  1. 如果代码中的"/"被浏览器解析，得到的地址是：http://ip:port
  2. 如果被服务器解析，得到的地址是：http://ip:port/虚拟路径
  3. 请求转发中的"/"会被浏览器解析，所以得到的地址是:http://ip:port

- 路径分类

  1. 相对路径：通过相对路径不可以确定唯一资源
     如：./index.html    以"."开头，"./"为当前目录下
     规则：找到当前资源和目标资源之间的相对位置关系
  2. 绝对路径：通过绝对路径可以确定唯一资源
     如：http://localhost/day15/responseDemo2   简化写法：/day15/responseDemo2
     规则：判断定义的路径是给谁用的，即判断请求从哪发出
     1. 如果是客户端浏览器使用：需要加虚拟目录
     2. 给服务器使用：不需要加虚拟目录，直接写资源路径
   3. 在网页中的超链接写相对路径，则是相对于当前请求url的路径。若直接访问该页面，则相对路径的资源会正常显示，而若该页面通过请求转发或重定向访问，==则url可能会改变（例如请求转发的Servlet的资源路径设为多级目录，则转发后url不再是项目的虚拟目录）==，导致该链接相对路径所映射的绝对路径与原来的不一致，最终找不到资源
  

重定向是服务器告诉客户端浏览器访问其它的资源路径，请求是由客户端发出，所以需要加虚拟路径

  

2. 输出数据到客户端浏览器

```java
//输出字符数据
@WebServlet("/responseDemo3")
public class ResponseDemo2 extends HttpServlet{
    protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        //设置流的默认编码iso8859-1为GBK
        response.setCharacterEncoding("GBK");
        //设置响应头告诉浏览器本次响应使用的编码格式，避免中文乱码。设置此行代码可不用设置上一行代码
        response.setHeader("content-type","text/html;charset=GBK");
        /*
        简单形式：
        	response.setContentType("text/html;charset=utf-8");
        */
        //获取字符输出流
        PrintWriter pw = response.getWriter();
        //输出数据,这个流是response获取的，response在一次响应完成后会自动销毁，会自动将数据刷新出缓冲区
        pw.write("<h1>hello response</h1>");
        
        /*
        	字节流使用：
        		1.response.setContentType("text/html;charset=utf-8");
        		2.获取字节输出流
        		ServletOutputStream sos = response.getOutputStream();
        		3.输出数据
        		sos.write("hello".getBytes());
        */
    }
    protected void doGet(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        this.doPost(request,response);
    }
}
```

3. 验证码

本质是图片，为了防止表单恶意注册

```java
@WebServlet("/checkCodeServlet")
public class ResponseDemo2 extends HttpServlet{
    protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        int width = 100;
        int height = 50;
        //创建BufferedImage对象生成图片
        BufferedImage image = new BufferedImage(width,htight,BufferedImage.TYPE_INI_RGB);
        //美化图片
        Graphics g = image.getGraphics();//画笔对象
        g.setColor(Color.PINK); //设置画笔颜色
        g.fillRect(0,0,width,height); //从图片0,0点设置填充
        g.setColor(Color.BLUE);
        g.drawRect(0,0,width-1,height-1);//画边框，因为边框有一个像素的大小，所以需要-l避免画到外面
        String str = "A~Za~z0~9";
        Random ran = new Random();
        for(int i = 1;i<=4;i++){
            int index = ran.nextInt(str.length());
            char ch = str.charAt(index);//随机字符串
            g.drawString(ch+"",width/5*i,height/2);//写验证码内容
        }
        //画干扰线
        g.setColor(Color.GREEN);
       for(int i = 0;i<10;i++){
            //随机生成坐标点
            int x1 = ran.nextInt(width);
            int x2 = ran.nextInt(width);
            int y1 = ran.nextInt(height);
            int y2 = ran.nextInt(height);
            g.drawLine(x1,y1,x2,y2); //参数分别为两个点的xy坐标
       }
        //将图片输出到页面展示
        ImageIO.write(image,"jpg",response.getOutputStream());
    }
    protected void doGet(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        this.doPost(request,response);
    }
}
```

## 5.SerlvletConfig对象

作用：ServletConfig对象是Servlet的专属配置对象，每个Servlet都单独拥有一个ServletConfig对象，用来获取每个Servelt在项目的web.xml中单独配置的信息

获取ServletConfig对象：ServletConfig sc = this.getServletConfig();

==该方法实际为父类中的方法，返回父类的一个ServletConfig变量，访问一个Servlet时会先调用init方法进行初始化，在父类中init方法会接受一个ServletConfig对象并进行保存，当在Servlet中重写了init方法时，需要在init方法中加super.init(config)，将init方法的参数传递回父类中，父类会将这个ServletConfig对象进行保存，getServletConfig方法才能从父类中获取到该对象==

使用：

1. 配置局部配置参数：==局部配置参数必须要配置在各自Servlet的<servlet>标签中==
   <servlet>

   ​	<init-param>

   ​		<param-name>name</param-name>

   ​		<param-value>value</param-value>
   ​	</init-param>
   </servlet>

2. 获取配置参数：
   String getInitParameter(String name)   获取参数中指定键的值

##6.ServletContext对象

概念：代表整个web应用，可以和程序的容器（服务器）来通信，session解决了同一用户在不同Servlet中数据共享问题，而ServletContext对象解决了不同对象之间的数据共享问题，例如查看网站的访问量

原理：ServletContext对象由服务器进行创建，一个项目只有一个对象。不管在项目的任意位置进行获取得到的都是同一个对象，那么不同用户发起的请求获取到的也就是同一个对象了，该对象由用户共同所有

生命周期：从服务器开启到服务器关闭，若该对象存储了重要数据，为了避免重启服务器数据丢失，可在destroy方法中将数据存储到文件中，init方法中将数据从文件中读出

获取：

1. 通过session对象获取：
   request.getSession().getServletContext();
2. 通过HttpServlet获取：
   this.getServletContext();
3. this.getServletConfig().getServletContext()

以上三种方法获取的都是同一个ServletContext对象

功能：

1. 获取文件的MIME类型
   MIME类型：在互联网通信过程中定义的一种文件数据类型
   格式：大类型/小类型。如：text/html；image/jpeg
   获取：String getMimeType（String  file）
   
2. 获取项目中web.xml文件中的全局配置参数
   在项目web.xml中配置全局参数：
   <context-para>

   ​	<param-name>name</param-name>

   ​	<param-value>zhangsan</param-value>

   </context-param>
   ==一个\<context-param>标签中只能有一组键和值，若要多组键和值只能定义多个\<context-param>标签==

   获取参数：

   1. String getInitParameter(String name)   	根据键名返回项目的web.xml中配置的全局参数的值，返回String类型，若不存在值返回null
   2. Enumeration getInitParameterNames();    返回全部键名

   **用处：**
      将项目中的静态数据定义到web.xml文件中，然后通过ServletContext获取，这种方法可以在当数据需要修改时不用改动源码，也就不需要重启服务器就可以实时看到效果

3. 它是域对象：共享数据

   1. setAttribute(String name,Object value)
   2. getAttribute(String name)
   3. removeAttribute(String name)

   ServletContext对象范围：所有请求的范围

4. 获取文件的真实路径（服务器读取的项目路径，即项目本地路径）
  
```
	1. getRealPath("") 获取服务其中的项目路径，如D:\workspace\IDEA_workspace\MyProject\out\artifacts\
	2. getRealPath("b.txt")  位于web目录下的文件
	3. getRealPath("WEB-INF/b.txt") 位于web目录下WEB-INF目录下的文件
	4. getRealPath("WEB-INF/classes/a.txt")位于src目录下的源码会被自动加载为class文件放到D:\workspace\IDEA_workspace\MyProject\out\artifacts\tomcat2_war_exploded\WEB-INF\classes，out目录就是IDEA编译后class文件存放的位置。artifacts目录里的项目就是真实发布在tomcat上的项目，tomcat2_war_exploded目录对应的就是IDEA中的web目录，tomcat就是将这些class文件加载进内存。
```

   ==getClassLoader只能获取src目录下的文件==

5. 获取文件真实路径的流对象
   InputStream getResourceAsStream(String path)   参数为文件**真实路径**

   > 文件下载案例

   ```html
   <bady>
   	<a href = "/虚拟目录/downloadServlet?filename=1.jpg">图片</a>
       <a href = "/虚拟目录/downloadServlet?filename=1.avi">视频</a>
   </bady>
   ```

   ```java
   @WebServlet("/downloadServlet")
   public class DownloadServlet extends HttpServlet{
       protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException{
           //获取请求参数，文件名
           String filename = request.getParameter("filename");
           ServletContext servletContext = this.getServletContext();
           String realPath = servletContext.getRealPath("/img/"+filename);
           //使用字节流关联
           FileInputStream fis = new FileInputStream(realPath);
           //将文件通过输出流响应到浏览器前先设置响应头类型, content-type
           String mimeType = servletContext.getMimeType(filename);//获取文件mime类型
           response.setHeader(" content-type",mimeType);
           //设置响应头的打开方式,content-disposition
           response.setHeader("content-disposition","attachment;filename="+filename);
           //将输入流的数据写出到输出流中
           ServletOutputStream sos = response.getOutputStream();
           byte[] buff = new byte[1024*8];
           int len = 0;
           while((len = fis.read(buff)) != -1){
               sos.write(buff,0,len);
           }
       }
       protected void doGet(HttpServletRequest request,HttpServletResponse response) throws ServletException{
           this.doPost(request,response);
       }
   }
   ```

   

#七.会话技术

会话：一次会话包含多次请求和响应。一次会话指浏览器第一次给服务器资源发送请求，会话建立，直到一方断开

功能：在一次会话的范围内的多次请求间共享数据

共享数据的方式：

1. 客户端会话技术（数据存储在客户端）：Cookie
2. 服务器端会话技术（数据存储在服务器端）：Session

##1.Cookie

操作步骤：

1. 创建Cookie对象，绑定数据
   new Cookie(String name,String value);  Cookie对象是键值对的形式
2. 发送Cookie对象到客户端
   response.addCooike(Cookie cookie);
3. 服务器端获取Cookie，拿到数据
   Cookie[] request.getCookies();   若客户端没有Cookie对象则会返回null，遍历数组前先判断一下
   String getName();   获取Cookie数组中存储的对象的name
   String getValue();   获取Cookie数组中存储的对象的value

执行原理：
     当客户端访问包含Cookie的Servlet资源时，服务器会调用addCookie方法将一个包含Cookie信息的响应头==（set-cookie:name=value）==发送到客户端，客户端会将头中的数据保存到浏览器中，同名Cookie会进行覆盖，在下一次发送请求时会将这些信息包含在请求消息头中==（cookie:name=value)==发送到服务器端；可以通过request.getCookies来获得客户端返回的Cookie信息

> Cookie的细节

1. 一次可以创建并发送多个Cookie

2. Cookie可以在浏览器中保存多长时间

   1. 默认情况下当浏览器关闭后，Cookie数据被销毁，因为此时Cookie存在电脑内存中，重启tomcat不会销毁Cookie，因为Cookie存在客户端

   2. 在发送Cookie前设置持久化存储：setMaxAge(int seconds)

      参数：

      1. 正数：将Cookie数据写到硬盘的文件中。持久化存储，在指定秒数后自动删除
      2. 负数：默认值，浏览器关闭后销毁
      3. **0**：将此Cookie对象中持久化存储的Cookie信息删除

3. Cooike能不能存储中文问题
   - 在tomcat  8  之前，Cookie中不能直接存储中文数据，需要将中文数据转码，一般采用URL编码
   - 在tomcat8之后，可以存储中文数据，但不能存储特殊字符，如空格。同样需要使用URL编码存储，URL解码解析
4. ==在一个tomcat服务器中部署了多个web项目==，默认情况下这些web项目不能共享Cookie。可以在发送前通过**setPath(String path)**来设置Cookie的获取范围，若不去调用这个方法，则默认为当前虚拟目录下可获取，将参数设置为"/"即可使所有web项目都获取到。即设置请求什么资源时请求信息会携带Cookie。==不同的tomcat服务器间Cookie共享==需要通过setDomain(String path)方法，若将一级域名设置为相同的就可以共享Cookie。例如：setDomain(".baidu.com")，那么tieba.baidu.com和news.baidu.com中的Cookie可以共享

> Cookie的特点及作用

特点：

1. 由于Cookie数据存储在客户端浏览器，所以数据很容易丢失和篡改
2. 浏览器对于单个Cookie大小有限制，对同一个域名下的总Cookie数量也有有限制

作用：

1. Cookie一般用于存储少量的不太敏感的数据
2. 在不登陆的情况下，完成服务器对客户端身份的识别。比如在不登陆的情况下对浏览器进行一些个性化设置后，服务器端会发送一个Cookie到客服端存储，当一下次访问时客户端带着Cookie想服务器端发出请求，服务器端会根据Cookie确定用户身份并以用户的个性化设置进行展示

> 案例

```java
/*
	需求：
		访问一个Servlet，如果第一次访问，则提示：您好，欢迎您首次访问。如果不是第一次访问，则提示：欢迎话来，您上次访问的时间为：显示时间字符串。免登录功能
*/

@WebServlet("/cookieTest")
public class CookieTest extends HttpServlet{
    protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        //设置响应的消息体的数据格式以及编码
        response.setContentType("text/html;charset=utf-8");
        //获取所有Cookie
        Cookie[] cookies = request.getCookies();
        boolean flag = false;  //代表没有cookie为lastTime
        //遍历Cookie数组
        if(cookies != null && cookies.length>0){
            for(Cookie cookie : cookies){
                //获取Cookie名称
                String name = cookie.getName();
                //判断名称是否是：lastTime
                if("lastTime".equals(name)){
                    //有该Cookie，说明不是第一次访问
                    flag = true;
                    //输出响应消息
                    String value = cookie.getValue();
                    //使用URL解码
                    value = URLDecoder.decode(value,"utf-8");
                    response.getWriter().write("<h1>欢饮回来，您上次访问的时间为："+value+"</h1>");
                     //重新设置Cookie的value
                    Date date = new Date();
                    SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
                    String str_date = sdf.format(date);
                    //使用URL编码
            		str_date = URLEncoder.encode(str_date,"utf-8");
                    cookie.setValue(str_date);
                    //设置cookie的存活时间为一个月
                    cookie.setMaxAge(60*60*24*30);
                    response.addCookie(cookie);
                     break;
                }
            }
        }
        if(cookies == null || cookies.length == 0 || flag == false){
            //没有cookie，说明第一次访问
            //设置Cookie的value
            Date date = new Date();
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
            String str_date = sdf.format(date);
            //使用URL编码
            str_date = URLEncoder.encode(str_date,"utf-8");
        	Cookie cookie = new Cookie("lastTime",str_date);
            cookie.setValue(str_date);
            //设置cookie的存活时间为一个月
            cookie.setMaxAge(60*60*24*30);
            response.addCookie(cookie);
            response.getWriter().write("<h1>您好，欢迎您首次访问</h1>");
        }
    }
    
    protected void doGet(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        this.doPost(request,response);
    }
}
```

##2.Session

概念：服务器端会话技术，在一次会话的多次请求（重定向）间共享共享数据，将数据保存在服务器端的对象HttpSession中，通常用于登录web项目时，将用户个人信息存储到session中，供该用户的其他请求使用

获取Session对象：HttpSession session = request.getSession();  ==没有session创建session，有session则获取session，如果session对象失效了，也会重新创建一个session对象==

HttpSession对象：

- Object getAttribute(String name);
- void setAttribute(String name,Object value)
- void removeAttribute(String name)  ==对于session存储验证码内容这种具有时效性的内容，使用完后一定要销毁session，否则验证码会出现复用的情况==
- invalidate();    使session对象强制失效

原理：当请求访问了包含创建session的资源时，会在服务器端内存创建一个session对象并赋予其一个ID，同时响应给客户端一个Cookie==(set-cookie:JSESSIONID=ID)==存储在客户端浏览器内存中，再次请求另一个包含session的资源时会携带Cookie==(cookie:JESSIONID=ID)==，服务器端通过ID找到第一次访问时创建的session返回给当前资源session对象的引用变量，保证了多次请求时获取的session对象为同一个以达到数据共享

> Session细节

1. 当客户端关闭，服务器不关闭，两次获取的Session不是同一个，==如果需要相同，则可以创建Cookie，键为JSESSIONID，设置最大存活时间让cookie持久化==

```java
@WebServlet("/SessionDemo")
public class ResponseDemo2 extends HttpServlet{
    protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        HttpSession session = request.getSession();
        Cookie c = new Cookie("JSESSIONID",session.getID());
        c.setMaxAge(60*60);
        response.addCookie(c);
    }
    protected void doGet(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        this.doPost(request,response);
    }
}
```

2. 客户端不关闭，服务器关闭后两次获取的Session不是同一个对象。但是数据可以不丢失：

   1. session的钝化：在服务器正常关闭之前，将session对象序列化到tomcat的work目录下
   2. session的活化：在服务器启动后，将session文件转化为内存中的session对象

   通过本地tomcat关闭和开启服务器可以自动完成上述过程，但IDEA只能完成钝化而不能完成活化。**之所以两个对象不同，就是因为反序列化创建的对象与原对象是两个对象**

3. 当服务器关闭；当session对象调用invalidate()方法；或当指定时间内（默认30分钟）不进行交互就会被销毁
   session对象.setMaxInactiveIntterval(int seconds)设置session对象的存活时间，只要规定时间内访问后就会重新计时

> Session特点

1. session用于存储一次会话的多次请求的数据，存在服务器端
2. session可以存储任意类型，任意大小的数据

#八.JSP

概念：Java Server Pages，java服务器端页面，既可以定义html标签，又可以写java代码。本质就是一个Servlet

运行原理：

1. 访问jsp页面，向服务器发送请求
2. tomcat的web.xml中配置了专门拦截以**.jsp**结尾的资源，将其交给**JspServlet**处理
3. 该Servlet找到该jsp资源，将其翻译成对应的Serlvet文件
4. 利用反射调用class文件中生成的**jspService**方法，将jsp文件的内容写出到浏览器



1. JSP脚本
   - <% 代码 %>：定义的java代码在service方法中，service方法可以定义什么这里就可以定义什么。
   - <%! 代码 %>：定义成员（变量或方法）
   - <%= 代码 %>：用于输出内容到页面，相当于定义在service方法中的输出语句

2. 内置对象：jsp文件在转译成对应的servlet中tomcat自动定义的九个service方法中的局部变量

   - PageContext pageContext：当前页面共享数据，通过getAttribute()和setAttribute()获取和设置键值对，只能在当前页面获取到当前页面设置的值。还可以通过getXXX()获取其他内置对象。每个jsp只有一个该对象，作用域为当前页面，可以解决当前页面内的数据共享问题

   - HttpServletRequest request：一次请求访问的多个资源共享数据（转发）
   - HttpSession session：一次会话的多个请求间共享数据，一个用户的不同请求数据共享
   - ServletContext application：所有用户间共享数据，作用范围是整个项目
     **以上为http中的域对象，用于共享数据**
   - HttpServletResponse response：响应对象
   - Object page：当前页面的对象，就是this，是当前页面被翻译成Servlet后对这个Servlet创建出的对象
   - JspWriter out：将数据输出到页面上，**tomcat生成的jsp的Servlet文件，都是使用该方式输出jsp页面内容**
     ==out.print()和response.getWriter().write()的区别：==out定义在什么位置就什么时候输出，而response永远会在out前输出，在tomcat服务器真正给客户端做出响应之前，会先找response缓冲区的数据，再找out缓冲区的数据。
   - ServletConfig config：Servlet的配置对象
   - Throwable exception：异常对象
3. JSP指令：用于配置JSP页面，导入资源文件。
   定义格式：<%@ 指令名称 属性名1=属性值1 属性名2=属性值2...%>
- page：配置JSP页面转译为servlet代码的参数
   1. contentType: 等同于response.setContentType()，设置响应体的mime类型以及字符集
   2. errorPage：若当前页面出现异常，会自动跳转到指定页面
   3. isErrorPage：将此页面标记为true，则可以在此页面中使用exception内置对象
   4. import：声明转义为java文件后代码所需要的包不同的包使用逗号隔开
   5. session：设置转译的servlet中是否开启session支持
   6. file：在当前jsp中引入其他jsp资源，值为资源路径

- include：<%@include file="jsp页面路径"%> 静态引入另一个jsp文件。可写在jsp任意位置，会将当前jsp与另一个jsp转译为一个servlet，所以当前文件与静态引入的文件不能有同名变量
- <jsp:forward page="jsp页面路径">\<jsp:param name="键" value="值"/>\</jsp:forward>：jsp中的转发，会将数据转发路径后，相当于get请求行，所以在获取时使用的是java代码中的getParameter方法
- taglib：引入标签库

##1.MVC开发模式

概念：将程序分为三个部分，Model View Controller，当浏览器请求资源时会由控制器调用Model进行业务操作并把数据返回给控制器，控制器将数据交给View展示来给浏览器做出响应。==Model有JavaBean充当，View由JSP充当，Controller由Servlet充当。==

##2.EL表达式

概念：Expression Language 表达式语言

作用：替换和简化JSP页面中的java代码的编写

语法：${表达式}

==注意：==如果无法使用EL表达式，则需要在**page**指令中添加==isELIgnored="false"==来开启对表达式的支持

使用：

1. 运算

   ​	运算符：

   1. 算数运算符：+ - * /(div) %(mod) 三元运算符
   2. 比较运算符：> < >= <= == !=
   3. 逻辑运算符：&&(and) ||(or) !(not)
   4. 空运算符：(not) empty
      功能：用于判断字符串、集合、数组对象是否为null且长度是否为0。例：${empty 键名}

2. 获取值

   1. EL表达式只能从域对象中获取值
   2. 语法：
      1. ${域名称/其它.键名}：从指定域中获取指定键的值，若没有获取到字符串则显示空字符串，而不会打印null，避免打乱页面布局
         域名称:
         
         - pageScope     ---->   pageContext
         - requestScope     ---->   request
         - sessionScope    ---->   session
         - applicationScope    ---->    application(ServletContext)
         
         其它：
         
         - param：获取请求参数的值  ${param.username}，获取请求url中key为username的值
         - paramValues：获取复选框或下拉列表的值  ${paramValues.hobby}
         - cookie：获取cookie的值
         - initParam：获取web.xml中配置的初始化参数值，可以是全局也可以是局部参数
      2. ${键名}：依次从最小的域中查找是否有该键对应的值，直到找到为止
      3. ${键名.==对象属性名==}：若域中键所对应的值是对象，则可通过此方法获取对象中的==属性==值
      4. ${键名[索引]}：若值是数组或集合对象，则通过此方法获取指定索引处的值，若索引越界则显示空字符串而不是报错
      5. ${map集合.key}或${map集合["key"]}：获取键对应的值为map集合的key所对应的值。第一种key为字符串时不需要加引号
   3. 隐式对象：EL表达式中有11个隐式对象
      - pageContext
        1. 获取其他八个内置对象
        2. ${pageContext.request.contextPath}：在JSP中动态获取虚拟目录

##3.JSTL标签库

概念：JavaServer Pages Tag Library	JSP标准标签库。由Apache组织提供的开源免费的jsp标签

作用：用于简化和替换jsp页面上的java代码

使用步骤：

1. 导入JSTL相关jar包
2. 引入标签库：<%@ taglib prefix="前缀名（通常设为c）" uri="http://java.sun.com/jsp/jstl/core" %>
3. 使用标签

常用JSTL标签：

1. <c:if test="返回值为boolean类型的EL表达式">标签体 < /c:if>
   test为必须属性，接受boolean表达式。如果表达式为true，则显示标签体内容，如果为false，则不显示。

2. < c:choose>

   ​	<c:when test="${表达式}">标签体< /c:when>

   ​	< c:otherwise>标签体< /c:otherwise>

   < /c:choose>
   choose标签相当于switch声明

   when标签相当于case

   otherwise标签相当于default

3. < c:forEach begin="1" end="10" var="i" step="1" varStatus="s">

   ​	${s.index}	${s.count}<br>
   < /c:froEach>

   ----

   遍历集合（foreach）：
   < c:forEach items="${值为集合的键名}" var="str" varStatus="s">

   ​	${s.index}	${s.count} ${str}<br>
   < /c:froEach>

   属性：

   1. begin：开始值（包含）
   2. end：结束值（包含）
   3. var：临时变量，相当于for循环中的i
   4. step：步长值
   5. varStatus：循环状态码
      - index：容器中元素的索引，从0开始
      - count：循环次数，从1开始

##4.三层架构：软件设计架构

1. 界面层（表示层）：用户看到的界面。用户可以通过界面上的组件和服务器进行交互。用于接收用户参数，封装数据，调用业务逻辑层完成处理，转发JSP页面完成显示。此类代码常放在web包下
2. 业务逻辑层（service层）：处理业务逻辑。组合DAO层中的简单方法，形成复杂的功能。此类代码常放在service包下
3. 数据访问层（DAO层）：Data Access Object，操作数据存储文件。定义了对于数据库最基本的CRUD操作。此类代码常放在dao包下



#九.Filter

- 作用：一般用于完成多个Servlet资源之间的通用的操作。如：对于需要登陆才能访问的资源进行登录验证，统一编码处理，敏感字符过滤

- 步骤：

  1. 定义一个类，实现Filter接口（javax.servlet)

  2. 复写方法

     - public void init (FilterConfig filterConfig)   ==在服务器启动后，会创建Filter对象，调用init方法==

       > FilterCongif

       作用：

       - 获取Filter名称：getFilterName()
       - 获取Filter中配置的参数：getInitParameter()
       - 获取ServletContext对象：getServletContext()
     - public void doFilter (ServletRequest servletRequest , ServletResponse servletResponse , FilterChain filterChain)
       ==该方法内部需要通过调用filterChain.doFilter(servletRequest , servletResponse)才能在执行完过滤器后执行真正要访问的资源，每一次访问拦截资源时都会执行==
       
     - public void destory ()  ==在服务器正常关闭使执行该方法销毁Filter对象==

  3. 配置拦截路径

     - xml文件配置
       <filter>

       ​	<filter-name>demo</filter-name>

       ​	<filter-class>全类名</filter-class>
       
       ​	<param-name>参数名</param-name>

       ​	<param-value>参数值</param-value>

       </filter>
       <filter-mapping>

       ​	<filter-name>demo</filter-name>
       
       ​	<url-pattern>/*</url-pattern> 	拦截路径，该标签可写多个
       </filter-mapping>
       
     - 在类上方写==@WebFilter("资源路径")==，资源路径表示访问什么资源时会执行该过滤器，若路径写为=="/*"==表示访问该项目下的任意资源都需要经过该过滤器

- 执行过程：在访问设置了过滤器的资源时，先执行过滤器中的方法，若过滤器中的调用了==filterChain.doFilter(servletRequest , servletResponse)==开始执行下一个过滤器，没有则开始访问目标资源，目标资源加载完之后会回到过滤器中，继续执行之后的代码。==filterChain.doFilter()==之前的代码通常对request对象的请求消息增强，而之后的代码则是对response对象的响应消息增强

- 拦截路径配置：

  - 具体资源路径：/index.jsp    只有访问index.jsp资源时，过滤器才会执行
  - 拦截目录：/user/*   访问/user下的所有资源时，过滤器会执行
  - 后缀名拦截：*.jsp   访问所有后缀名为jsp资源时，过滤器会执行

- 拦截方式配置

  - 注解配置：设置dispatcherTypes属性：

    1. REQUEST：默认值，由浏览器直接访问资源时会执行过滤器
    2. FORWARD：转发访问资源，只有由转发访问资源时会执行过滤器
    3. INCLUDE：包含访问资源
    4. ERROR：错误跳转资源
    5. ASYNC：异步访问资源

    ==例如：@WebFilter(value="/index.jsp" , dispatcherTypes ={DispatcherType.FORWARD,DispatcherType.REQUEST})==

  - web.xml配置：添加<dispatcher>拦截方式</dispatcher>

- 过滤器链（多个过滤器拦截同一资源）执行的顺序问题
  1. 注解配置：按照类名的字符串比较规则比较，值小的先执行
  2. web.xml配置：谁的<filter-mapping>定义在上边谁先执行

> 案例

一.登录验证

```java
@WebFilter("/*")
public class LoginFilter implements Filter {
    public void doFilter(ServletRequest req,ServletResponse resp,FilterChain chain) throws ServletException{
        //请求一般都是基于http协议，所以将参数类型转换为HttpServeltRequest以便使用接口中的方法
        HttpServletRequest request = (HttpServletRequest) req;
        //获取资源请求路径
        String uri = request.getResquestURI();
        /*判断是否包含登录相关资源路径,要注意也需要排除掉css/js/图片/验证码等页面资源，因为加载页面时页面中的静态资源本质也是通过资源resource属性所设置的地址（如果是相对路径就是相对于浏览器当前url）来向服务器发起请求加载资源，而过滤器设置为拦截所有请求，若向页面服务器发起资源请求此处不放行，会导致资源加载不出来*/
        if(uri.contains("/login.jsp") || uri.contains("/loginServlet") || uri.contains("/css/") || uri.contains("/js/")){
			//包含，直接放行，访问登录页面
            chain.doFilter(req,resp);
        }else{
            //不包含，验证用户是否为登录状态。登录成功后设置一个名为user的session来记录登录信息，通过这个session来判断是否登录
            Object user = request.getSession().getAttribute("user");
            if(user != null){
                //user对象不为空，说明已登录，允许访问
                chain.doFilter(req,resp);
            }else{
                //没有登陆。跳转登录页面
                request.setAttribute("login_msg","您尚未登录，请登录");
                request.getRequestDispatcher("login.jsp").forward(request,resp);
            }
        }
    }
    
    public void init(FilterConfig config) throws ServletException{}
    public void destory(){}
}
```

# 十.Listener

##1.监听器分类

> Servlet context events

- **javax.servlet.ServletContextListener**：监听ServletContext生命周期（服务器启动创建，服务器关闭销毁）
- **javax.servlet.ServletContextAttributeListener**：监听ServletContext域中属性变化

> HTTP session events

- **javax.servlet.http.HttpSessionListener**：监听HttpSession的生命周期（第一次使用时创建，session超时销毁或手动设置`session.invalidate()`销毁
- **javax.servlet.http.HttpSessionAttributeListener**：监听HttpSession域中属性变化
- **javax.servlet.http.HttpSessionActivationListener**：监听某个对象随HttpSession活化钝化
- **javax.servlet.http.HttpSessionBindingListener**：监听某个对象保存到session中和从session中移除

> Servlet request events

- **javax.servlet.ServletRequestListener**：监听ServletRequest对象的生命周期（请求进入服务器创建，请求完成销毁）
- **javax.servlet.ServletRequestAttributeListener**：监听ServletRequest对象属性变化

## 2.监听器中的方法

> 监听声明周期

- **ServletContextListener**
  - `void contextDestroyed(ServletContextEvent sce)`：服务器停止调用
  - `void contextInitialized(ServletContextEvent sce)`：服务器启动调用
- **HttpSessionListener**
  - `void sessionCreated(HttpSessionEvent se)`：第一次使用session创建
  - `void sessionDestroyed(HttpSessionEvent se)`：session失效时调用
- **ServletRequestListener**
  - `void requestDestroyed(ServletRequestEvent sre)`：请求结束时调用
  - `void requestInitialized(ServletRequestEvent sre)`：发起一次请求时调用

> 监听域中属性变化

- `void attributeAdded(ServletContextAttributeEvent scab)`：监听属性添加
- `void attributeRemoved(ServletContextAttributeEvent scab)`：监听属性移除
- `void attributeReplaced(ServletContextAttributeEvent scab)`：监听属性修改

> session独有监听器

- **HttpSessionActivitionListener**
  - `void sessionDidActivate(HttpSessionEvent se)`：某个对象随着session已经活化后调用
  - `void sessionWillPassivate(HttpSessionEvent se)`：某个对象将要和session钝化前调用
- **HttpSessionBindingListener**
  - `void valueBound(HttpSessionBindingEvent event)`：某个对象在session中保存时调用
  - `void valueUnbound(HttpSessionBindingEvent event)`：某个对象在session中移除时调用

## 3.监听器使用

###3.1.ServletContextListener

> 编写监听器

```java
public class MyServletContextListener implements ServletContextListener{
    @Override
    public void contextDestroyed(ServletContextEvent arg){
        ServletContet context = arg.getServletContext();
        System.out.println("contextDestroyed:"+context);
    }
    @Override
    public void contextInitialized(ServletContextEvent arg){
       ServletContet context = arg.getServletContext();
        System.out.println("contextInitialized:"+context);
    }
}
```

> web.xml中注册监听器

```xml
<web-app>
	<listener>
    	<listener-class>com.zbvc.listener.MyServletContextListener</listener-class>
    </listener>
</web-app>
```

### 3.2.HttpSessionListener

```java
public class MySessionLifeCycleListener implements HttpSessionListener{
    @Override
    public void sessionCreated(HttpSessionEvent se){
        HttpSession session = se.getSession();
        System.out.println("session对象创建了:"+session.getId());
    }
    @Override
    public void sessionDestroyed(HttpSessionEvent se){
        System.out.println("session销毁了:"se.getSession().getId());
    }
}
```

```xml
<web-app>
	<listener>
    	<listener-class>com.zbvc.listener.MySessionLifeCycleListener</listener-class>
    </listener>
</web-app>
```

**第一次访问jsp页面时会调用`sessionCreated()`方法，因为jsp页面内置了Session对象，在访问jsp页面时会自动创建，所以会监听到Session对象的创建**

### 3.3.Session特有监听器

这两个监听器不需要在web.xml中进行注册

> 创建POJO对象

```java
public class User implements Serializable,HttpSessionActivitionListener{
    @Override
    public void sessionWillPassivate(HttpSessionEvent se){
        System.out.println("User["+this+"]将要和session一起钝化了");
    }
    @Override
    public void sessionDidactivate(HttpSessionEvent se){
        System.out.println("User["+this+"]将要和sesson一起活化了");
    }
}
```

创建User类型的对象并将其存入Session后，当session钝化和活化时就会调用User类型对象中的`sessionWillPassivate()`和`sessionDidactivate()`

#十一.JQuery

​	JQuery是一个JS框架，简化JS开发。本质上就是一些js文件，代码同样需要写在<script>标签中

- 使用步骤：

  1. 下载JQuery

  2. 导入JQuery的js文件

     <script src="jq文件路径"></script>

- JQuery对象与js对象的转换

  - 同过JS方式来获取的名称叫div的所有html元素
    var divs = document.getElementsByTagName("div");
  - 通过jq方式获取名称叫div的所有html元素对象
    var $divs = $("div");

==js和jq获取出来的对象有多个同种标签时，都可以当作数组使用，jq与js对象的方法不通用，但两者可以转化：${js对象}，将js转化为jq对象。jq对象[索引]  或者  jq对象.get(索引)，将jq转化为js，因为jq对象就是一个封装了dom对象的数组==

- 入口函数：
  
```javascript
  $(function(){
  	$("#b1").click(function(){
	alert("事件被执行了");
  });
```
  ```javascript
  $(document).ready(function(){
  	alert("事件被执行了");
  });
  ```

==window.onload 和$(function)的区别：window.onload只能使用一次，使用多次会用后面的将前面的替换,生效时间比jquery慢，因为除了要等待页面的DOM结构加载完毕，还需要等待标签里的内容也加载完毕才会执行，而JQuery只需要DOM结构准备就绪即可，可以多次使用，为页面载入增加多个函数==

- css样式控制：
  
```javascript
  $(function(){
	$("#div1").css("background-color",pink); //只传一个属性可获取属性值
  })
```

  

##0.JQuery封装原理

闭包原理：通过在函数中创建对象，将函数中多个局部属性封装到对象中，然后返回对象，达到延长局部变量寿命的目的

```html
<!--声明在外部script文件中-->
<script>
    //声明匿名函数
	(function(obj){
        obj.test=function(){
            alert("匿名函数子调用");
        }
    })(window)  //通过()直接调用函数
    //在匿名函数的形参对象中添加属性然后返回对象，使用()调用匿名函数并传入实参window，把新添加的属性全部挂载到window对象下，只要加载了JQuery文件，window对象下就拥有了JQuery所定义的函数。以解决用户可能在JavaScript代码域中声明同名函数而覆盖了JQuery中的函数功能。JQuery文件中声明了使用方法：window.jQuery = window.$ = jQuery;而window可以省略
</script>
```

##1.选择器

### 1.1基本选择器

1. 标签选择器（元素选择器）

   语法：$("html标签名")   获得所有匹配标签名称的元素，传入`*`表示所有标签。若传入**html字符串**则会创建一个该dom对象

2. id选择器

   语法：$("#id值")   获得指定id的元素

3. 类选择器

   语法：$(".class值")  获得与指定class值相匹配的元素

4. 并级选择器
   语法：$("选择器1"，"选择器2")   同时选中两个选择器中的元素

### 1.2层级选择器

1. 后代选择器
   语法：$("A B")   选择A元素内部的所有B元素，若不写B元素，则为A元素下的所有后代元素
2. 子选择器
   语法：$("A > B")  选择A元素下一级的所有B子元素
3. 弟选择器
   语法：$("A + B") 选择A元素同级的下一个元素，例如：$("#btn + .mini")，选中id为btn同级的下一个class为mini的元素
4. 兄弟选择器
   语法：$("A ~ B")  选中与A元素同级的所有元素

### 1.3属性选择器

1. 属性名称选择器

   语法：$("标签[属性名]")  包含指定属性的元素

2. 属性选择器
   语法：$("标签[属性名 = '值']")   属性值是指定值的元素

3. 复合属性选择器
   语法：$("标签[属性名='值'] []....")  包含多个属性条件的元素

运算符：

1. 属性名 != '值' ，属性值不是指定值的元素
2. 属性名 ^= '值'，属性值以某个值开头的元素
3. 属性名 $= '值'，属性值以某个值结尾的元素
4. 属性名 *= '值'，属性值包含某个值的元素

###1.4过滤选择器

1. 首元素选择器
   语法：标签:first  获得选择的元素中的第一个元素
2. 尾元素选择器
   语法：标签:last  获得选择的元素中的最后一个元素
3. 非元素选择器
   语法：标签:not(selector)  不包括指定内容的元素
4. 偶数选择器
   语法：标签:even    偶数，从0开始计数
5. 奇数选择器
   语法：标签:odd     奇数，从0开始计数
6. 等于索引选择器
   语法：标签:eq(index)   指定索引元素，索引从0开始
7. 大于索引选择器
   语法：标签:gt(index)  大于指定索引的元素
8. 小于索引选择器
   语法：标签:lt(index)   小于指定索引元素
9. 标题选择器
   语法：标签:header   获得标题（h1~h6）元素，固定写法

###1.5表单过滤选择器

1. 可用元素选择器
   语法：表单标签:enabled    获得可用元素
2. 不可用元素选择器
   语法：表单标签:disabled   获得不可用元素
3. 选中选择器
   语法：表单标签:checked    获得单选/复选框中的元素
4. 选中选择器
   语法：表单标签:selected   获得下拉框选中的元素

##2.DOM操作

- 内容操作
  1. html("标签体内容")：获取/设置元素的标签体内容，包括该标签内的子标签
  2. text("标签体内容")：获取/设置元素的标签体纯文本内容。只会获取纯文本内容，但设置内容时会将其子标签也替换
  3. val()：获取/设置元素的value属性值  ==在获取input框的value值时，可以获取到用户输入的值==。对于单选框或下拉框等文档元素使用该函数并传入一个字符串或字符串数组作为参数时，如果参数字符串或字符串数组中的值与某个单选框或某些下拉框的value属性相同，该函数会将这个单选按钮或下拉框设置为选中状态
     例：var value = $("#myinput").val()           $("#myinput").val("李四")
  4. serialize()：通过表单对象调用该方法可以将表单要提交的内容（即提交表单时拼接到url后的内容）转换为“key=value”的字符串
  
- 属性操作

  1. 通用属性操作

     1. attr("属性名" [,"属性值"]);     获取/设置元素属性  ==在获取input框的value值时，只能获取到默认值，不能获取到用户输入的值==
     2. removeAttr("属性名");     删除元素属性
     3. prop("属性名" [,"属性值"]);    获取/设置元素属性
     4. removeProp();    删除元素属性
     5. css("css样式名"[,"样式值"]);   获取/设置css样式，css样式名为正常css样式名。参数可以是JSON，通过JSON数据格式可以以此设置多个css样式。例：$(#div1).css({"background-color":"black","border":"solid 1px"})

     attr和prop的区别：

     1. 如果操作的是元素固有属性，则建议使用prop
     2. 如果操作的是元素自定义属性，则建议使用attr，通常可以用于为动态添加DOM元素对象时为这个新增的DOM对象添加自定义属性和属性值，然后通过获取这个标签该属性的属性值来达到传值的目的

  2. 对class属性操作

     1. addClass("属性值");    追加class属性值
     2. removeClass("属性值");    删除class属性值
     3. toggleClass("属性值");    如果元素对象上存在class="one",则将属性值one删除掉，如果元素对象上不存在class="one",则添加

- 文档CRUD操作
==${}可以通过传递字符串的方法创造一个标签放入html文档==
  
  1. append()
     对象1.append(对象2/带有标签的字符串)：将对象2作为子元素添加到对象1的末尾，方法返回的是调用者对象，即==对象1==
  2. prepend()
     对象1.prepend(对象2/带有标签的字符串)：将对象2作为子元素添加到对象1的开头
  3. appendTo()
     对象1.appendTo(对象2)：将对象1作为子元素添加到对象2末尾
  4. prependTo()
     对象1.prependTo(对象2)：将对象1作为子元素添加到对象2开头
  5. after()
     对象1.after(对象2/带有标签的字符串)：将对象2添加到对象1之后，二者为同级元素
  6. before()
     对象1.before(对象2/带有标签的字符串)：将对象2添加到对象1之前，二者为同级元素
  7. insertAfter()
     对象1.insertAfter(对象2)：将对象1添加到对象2之后
  8. insertBefore()
     对象1.insertBefore(对象2)：将对象1添加到对象2之前
  9. remove()
     对象.remove()：将对象删除掉
  10. empty()
      对象.empty()：清空元素的所有后代元素，但保留当前对象节点及属性节点
  11. clone()
      对象.clone()：返回一个与调用该方法相同的对象，this是一个JS对象，使用此方法时需要先转换

##3.JQuery高级

###3.1动画

1. 默认显示和隐藏方式（向左上收缩）
   - show([speed], [easing] , [fn]])
   - hide([speed], [easing] , [fn]])
   - toggle([speed],[easing],[fn])
2. 滑动显示和隐藏方式（竖直方向收缩）
   - slideDown([speed],[easing],[fn])
   - slideUp([speed],[easing],[fn])
   - slideToggle([speed],[easing],[fn])
3. 淡入淡出显示和隐藏方式
   - fadeIn([speed],[easing],[fn])
   - fadeOut([speed],[easing],[fn])
   - fadeToggle([speed],[easing],[fn])

参数：

1. speed：动画的速度。三个预定义的值("slow","normal","fast")或表示动画时长的毫秒数值，如：1000
2. easing：用来指定切换效果，默认是"swing"(动画效果为先慢，后快，再慢)，可用参数"linear"(动画执行效果为匀速)
3. fn：动画完成时执行的函数，每个元素执行一次

###3.2遍历

1. for(初始值;循环条件;步长)

   `var cities = $("#city li")`
   `for(var i = 0; i < cities.length; i++){`

   ​	`alert(i + ":" + cities[i].innerHTML);`
   `}` ==可使用break和continue==

2. jq对象.each(callback)    callback：回调函数

   `cities.each(function([index] , [element]){ `   参数命名任意，主要是顺序，第二个参数就是cities对象（即dom对象数组）

   ​	`alert(this.innerHTML);`        获取li对象，第一种方式

   ​	`alter(index + ":" + $(element).html());` 	获取li对象，第二种方式

   ​	`alter(index + ":" + element.innerHTML);`
   `})`   ==若内部有方法返回了false则相当于break，true则相当于continue==

3. $.each(object,[callback])
   `$.each(cities,function(){
           alert($(this).html());
})`
   
4. for..of:  ==只有高版本才可以使用==
   `for(li of cities){`
     `alert($(li).html());
   }`

### 3.3事件绑定

==使用事件时会传递一个事件对象到回调函数中，可在回调函数中定义一个参数来接受，可以调用其中的属性来进行判断操作==

1. JQuery标准绑定方式
   jq对象.事件方法(回调函数);
   ==若调用事件方法，不传递回调函数，则会触发浏览器默认行为==
   
   - `$("#b1").click(function(){
          alert("事件被执行了");
     });`
   ==若子元素与父元素绑定同一事件方法（事件执行的函数可以不同）时，当子元素的事件被触发时，父元素的事件也会随后触发，若不想父元素事件触发，则在子元素事件的回调函数中添加return false;==
     
   - `$("#btn").bind("click mouseover",function(){
        alert("事件被执行了");
     });`  ==为按钮动态添加事件，此方法事件添加为追加效果，而js不能追加==
   
   - `$("#btn").one("click",function(){
   alert("事件被执行了");
       })` ==该方法为按钮添加的事件为一次性事件，执行过后自动销毁==
   
- `$("#btn").unbind("click");`  动态解除事件绑定
2. on绑定事件，off解除绑定
   `jq对象.on("事件名称",回调函数)`
   `jq对象.off("事件名称")`
on可以为DOM文档中动态生成的DOM元素绑定对象，使用方法为：在"事件名称"参数后再加一个选择器的字符串，调用该函的的jq对象中如果新增了符合这个选择器的后代元素，就会自动为这个新增的文档对象也添加同样的事件。
   如：当整个文档对象中新增了类属性为"edit_btn"的元素时，为这个元素绑定单击事件
   
   ```javascript
   ${document}.on("click",".edit_btn",function(){
       alert("单击事件已绑定");
   })
   ```
   
   
   
3. 事件切换
   `jq对象.toggle(function1,function2)`
   ==1.9版本该方法被删除，需要使用JQueryMigrate插件回复此功能==

##4.插件

插件：增强JQuery的功能，相当于定义实现特定功能的方法

**实现方式：**

- $.fn.extend(object)
  增强通过JQuery获取的对象的功能。$("#id")

  ```html
  <head>
      <script>
      	$.fn.extend({
              check:function(){
                  //该函数会被一个jq对象调用，所以此处使用this可以获取到调用该方法的jq对象
                  this.prop("checked",true);
              },
              uncheck:function(){
                  this.prop("checked",flase);
              }
          }); //为jq对象定义两个函数，所有jq对象都可以调用此函数
          //调用
          $(function(){
             $("#btn-check").click(function(){
                 $("input[type='checkbox']").check(); //jq对象被添加了该函数，IDE会提示
             }); 
              
              $("#btn-uncheck").click(function(){
                 $("input[type='checkbox']").uncheck();
             }); 
          });
      </script>
  </head>
  <body>
      <input id ="btn-check" type = "button" value="点击选中复选框">
      <input id ="btn-uncheck" type = "button" value="点击取消复选框选中">
  <br/>
      <input type = "checkbox" value="football">足球
      <input type = "checkbox" value="basketball">篮球
      <input type = "checkbox" value="volleyball">排球
  </body>
  ```

  

- $.extend(object)  ==对全局方法进行方法扩展==
  增强JQuery对象自身的功能   $或JQuery直接调用

  ```html
    <head>
           <script>
           	$.extend({
                   //定义判断两个参数哪个更大的方法
                   max:function(a,b){
                       return a >= b ? a:b;
                   },
                   //定义判断两个参数哪个更小的方法
                   min:function(a,b){
                       return a <= b ? a:b;
                   }
               });
               
               //调用方法
               var max = $.max(4,3);
               alert(max);
               var min = $.min(1,2);
               alert(min);
           </script>
       </head>
  ```

#十二.AJAX

概念：ASynchronous JavaScript And XML   异步的JavaScript 和 XML。一种浏览器与服务器的交互方式，由浏览器内置的ajax引擎对象（XMLHttpRequest）向服务器发送请求，将服务器响应回来的数据通过js或jq重新设置到html页面的标签中达到==局部刷新==

- 同步：客户端必须等待服务器端的响应。在等待期间客户端不能做其他操作
- 异步：客户端不需要等待服务器端的响应。在服务器处理请求的过程中，客户端可以进行其他操作。即在当前页面显示下一次响应的结果

1. $.ajax()

   ```html
   <head>
       <script src="jquery文件路径"></script>
       <script>
       	function fun(){
               //发送异步请求，可以使用EL表达式
               $.ajax({
                   url:"ajaxServlet",  //请求资源路径
                   type:"POST",  //请求方式，默认为GET
                   data:"username=jack&age=23",  //请求参数
                   //data:"{'username':'jack','age':23}",
                   async:true,//设置请求为异步，默认为ture
                   success:function(data){
                       alert(data);
                   },  //响应成功后的回调函数
                   error:function(){
                       alert("出错了...");
                   },  //表示如果请求响应出现错误会执行的回调函数
                   dataType:"json"   //指定响应回的数据以何种数据处理
               });
           }
       </script>
   </head>
   ```
   
2. $.get()

   ```html
   <head>
       <script>
       	$.get("ajaxServlet",{username:"rose"},function(data){
               alert(data);
           },"text");
       </script>
   </head>
   ```

3. $.post()

#十三.JOSN

JOSN：JavaScript Object Notation   JavaScript中的对象表示法，是一个字符串，方便数据在浏览器和客户端之间的传递。在JSP页面中内置了一个**JSON**对象，该对象的`stringify()`可以传入一个JS对象将其转为JSON字符串，只有JSON字符串才可以在浏览器与客户端之间传递，而`parse()`可以传入一个JSON字符串将其转为JS对象

JS对象定义语法：

- var p1 = {"name":"张三" , "age":23}

- var p2 = [{"name":"张三" , "age":23},{"name":"tom" , "age":23}]

- {"persons":[{},{}]}	{"address":{"province":"山西"}}

  ==属性可用单双引号，也可不用，花括号用于保存对象，[]用于保存数组==

1. 获取数据

   - JS对象.键名

   - JS对象["键名"]

   - 数组对象[索引]

   - JS对象.键名[索引].键名  ==一个JS对象的键所对应的值是数组，获取到这个数组后通过索引确定是数组中的哪一个JS，然后获取键。==
     例：
     
   ```javascript
     var persons = {
                   "persons":[
                         {"name":"张三","age":23},
                         {"name":"李四","age":23},
                         {"name":"王五","age":23}
                     ]
                 };
   ```
   
     persons.persons[2].name;    获取到的值为王五
   
2. 遍历获取所有值
  
> 　**for in**循环遍历获取JSON对象中的所有key

```javascript
for(var key in p1){
	alert(key+":"+p1[key]);
}
```

==不能使用p1.key来获取值，因为循环获取的key带有引号，相当于p1."name"，p1."age"==

> 　通过双层for循环遍历JSON类型的数组

```javascript
for(var i = 0;i < p2.length; i++){
    var p = p2[i];
    for(var key in p){
        alert(key+":"+p[key]);
    }
}
```

3. JOSN与java对象的相互转换
   - JSON解析器：
     Jsonlib，Gson，fastjson，jackson
   - JSON转java对象
     readValue(json字符串数据 , 对象的Class类型)

   ```Java
   public class JacksonTest{
       @Test
       public void test() throws Exception{
           String json = "{\"gender\":\"男\",\"name\":\"张三\",\"age\":23}";
           //创建ObjectMapper对象
           ObjectMapper mapper = new ObjectMapper();
           //转换为java对象
           Person person = mapper.readValue(json,Person.class);
           System.out.println(person);
       }
   }
   ```

   - Java对象转换JOSN
     使用步骤：
     1. 导入jackson的相关jar包
     2. 创建Jackson核心对象  ObjectMapper mapper = new ObjectMapper()
     3. 调用ObjectMapper的相关方法进行转换

   ```java
   public class JacksonTest{
       @Test
       public void test() throws Exception{
           Person p = new Person();
           p.setName("张三");
           p.setAge(23);
           p.setGender("男");
           //创建Jackson核心对象
           ObjectMapper mapper = new ObjectMapper();
           /*
           	转换方法：
           		writeValue(参数1,obj);
           			参数1：
   						File:将obj对象转化为JSON字符串，并保存到指定文件中
   						Writer:将obj对象转换为JSON字符串，并将json数据填充到字符输出流中
   						OutputStream:将obj对象转换为JSON字符串，并将json数据填充到字节输出流中
           		writeValueAsString(obj):将对象转化为json字符串
           */
           String json = mapper.writeValueAsString(p);
           System.out.println(json);
           
           response.setContentType("application/json;characterset=utf-8");
           mapper.writeValue(new File("d://a.txt"),p);
           mapper.writeValue(new FileWriter("d://b.txt"),p);
       /*
       	将json字符串使用response.getWriter().write()写回jsp页面，然后在jsp页面通过eval方法将其转化为JavaScript对象表示。也可以不使用jackson，直接将查询结果写为json字符串： {"name":"张三" , "age":23}再写回jsp
       */
   	}
   }
   ```

4. JSON注解
   - @JsonIgnore：排除属性，用在JavaBean中的变量上，在将此JavaBean的对象转化为JSON数据时排除此变量
   - @JsonFormat：用于格式化日期型变量。如：private Date birthday；在此变量上加@JsonFormat(pattern="yyy-MM-dd")注解，转化JSON数据时，该变量的内容会以指定格式转化
