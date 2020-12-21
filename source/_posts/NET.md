---
title: .NET
date: 2020-11-29 11:01:28
tags:
- 前端
- .NET
---

# 1. 选择判断

### 1. HTML

1. 常见块级元素和行内元素：
	1. div：块级元素，没有特定含义，通常用于逻辑上的分块，会换行 ，
	2. span：行内元素，无特殊含义，可作为文本容器，不会换行
	3. table：块级元素，是HTML表格，会换行
	 <img src="C:\Users\zhoux\AppData\Roaming\Typora\typora-user-images\image-20201129110949676.png" alt="image-20201129110949676" style="zoom: 67%;" />
	4. form：块级元素，会换行
2. ul、ol标签区别
	1. ul是无序列表，使用list-style-type:none可以去掉实心圆 <img src="http://cdn.zhouxug.cn/20201129111459.png" alt="image-20201129111459633" style="zoom:67%;" />
	2. ol是有序列表，可以设置 start = "100" 是的序号从100开始 <img src="http://cdn.zhouxug.cn/20201129111512.png" alt="image-20201129111512154" style="zoom:67%;" />
	3. 以上两者内部都是用li标签
3. video标签支持的格式：mp4,webm,ogg三种格式
4. input常用属性
	1. name：传值必须使用name，否则该数据不会被提交
	2. 使用get方法会在url后以?连接请求串，多个值之间使用&相连
	3. value：初始值
	4. title：悬浮提示
	5. autofocus：获得焦点
	6. placeholder：站位提示，输入会自动消失
	7. type：
		1. text：单行文本框，搭配maxlength表示接收的最长字符
		2. password：密码
		3. checkbox：复选框，收到的内容为 name = value，<img src="http://cdn.zhouxug.cn/20201129113148.png" alt="image-20201129113148280" style="zoom:80%;" />
		4. hidden：隐藏域，有name值的隐藏域会和其value一起提交
		5. submit：提交
		6. reset：重填
5. form表单
  1. 概览

    <img src="http://cdn.zhouxug.cn/20201129112040.png" alt="image-20201129112040621" style="zoom: 50%;" />
  2. target = "\_blank" 是从新窗口打开页面，默认是 target = "\_self"，从当前页面打开
  3. 

# 2. CSS

> 样式定义如何显示HTML元素，解决内容与表现分离的问题

### 1. 常用选择器

1. 通配符选择

```css
* {
    color : red;
}
```

2. 标签选择器

```css
body, input{
    background-color : silver;
}
```

3. id选择器

```css
# id {
    background-color : red;
}
```

4. class选择器

```css
.class {
    
}
```

5. 标签 class组合选择器

```css
div.class {} // 注意选择器之间没有空格
```

6. 后代选择器

```css
ul li a {            //空格分隔选择器 可以跨代
	color:red;		 //本例含义 ul后代的li后代的a标签字体为红色
} 
```


```html
<div id="nav">
	<a href="#">首页</a>
	<ul class="menu">
		<li> <a href="http://www.wust.edu.cn/">武科大</a> </li>
		<li> <a href="http://www.hust.edu.cn/">华科</a> </li>
		<li> <a href="http://www.whu.edu.cn/">武大</a> </li>
	</ul>
</div>

```

7. 子选择器（**直接儿子**）

```css
div>strong {           // div 所有直接儿子为strong元素字体为红色 
    color : red;
}
```

8. 相邻兄弟选择器

```css
h3+p {}  // 选择紧跟在另一元素后的元素(第一个相邻的) ，二者有相同父元素
```

9. 同级兄弟选择器

```css
h3~p{}  // 选择同级的元素(后面相邻的兄弟)，二者有相同父元素
```

10. 伪类

```css
// 1. a标签
<a href="http://www.wust.edu.cn" target="_blank">武科大</a>

a:link { /* 未访问的链接 */
color: red;
}
a:visited { /* 已访问的链接 */
color: grey;
}
a:hover { /* 鼠标移动到链接上 */
color: purple;
}
a:active { /* 选定的链接(鼠标按下时) */
color: blue;
}

// 2. focus
input:focus {
background-color: yellow;
}

<form>
	用户名:<input type="text" name="username" />
	<br />
	密码: <input type="text" name="psd" />
</form>

// 3. first-child
p:first-child {    //作为第一个子元素的 p 元素
color: red;
}
<body>
	<div>
		<p>第一段</p> // 变红
		<p>第二段</p>
	</div>
</body>

// 4. before在元素之前插入新内容
h1:before {  //含义：在 h1 元素之前添加内容
content: url("images/1.gif");
}
<h1>武汉科技大学</h1>
// tips: 在css3中推荐写法为 ::before ::after
```

### 2. 常用属性

#### 1. 背景属性

```css
.example {
    // 颜色
    background-color : red;
    
    // 图片
    background-image : url(image/1.jpg); // 没有引号
    backgroung-repeat : no-repeat; // 背景图片仅显示一次 
    							   // 默认为 repeat 背景图片将在垂直和水平方向重复
    background-attachment : fixed; // 页面滚动时，背景图不会滚动 
                                   // 默认为scroll 会滚动
    // 上述三种属性可简写为 
    background : url(image/1.jpg) no-repeat fixed;
}
```

#### 2. 字体属性

```css
p {
	font-size: 20px;
	font-family: 黑体,arial,sans-serif;   //如果浏览器不支持第一个字体，则会尝试下一个
	font-style: italic; //字体样式 分为 normal italic 斜体 oblique 倾斜 
	font-weight: bold;  //字体粗细
	line-height: 30px;  //行高
}
```

#### 3. 文本属性

1. 文字居中

```css
div.center {
	background-color: silver;
	width:200px;
	height: 50px;
	text-align: center; 
}
<div class="center">武汉科技大学</div>
```

![image-20201129151930663](http://cdn.zhouxug.cn/20201129151930.png)

2. 文字水平居中

```css
div.center {
	background-color: silver;
	width:200px;
	height: 50px; 
	text-align: center;
	line-height: 50px;  // 字体行高与 div的行高相同 则文字可以垂直居中
}

```

3. 图片居中

```css
img {
	border: 1px solid blue;
	vertical-align: middle;
}
<p>
	这是一幅<img src="images/1.gif" />位于段落中的图像。
</p>
```





























