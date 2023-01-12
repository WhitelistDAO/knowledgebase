邓晓阳

# 常用标签

***块级标签***：<div> <h> <ul> <li> <p> <form>　　占满一行,可以设置宽，高

***行级标签***：<span> <a>　　只占用自己的大小，不可以设置宽，高。

***换行标签***： <br>

空格： &nbsp

# 语法

HTML 元素以**开始标签**起始

HTML 元素以**结束标签**终止

元素的内容是开始标签与结束标签之间的内容

某些 HTML 元素具有*空内容（empty content）*

空元素**在开始标签中进行关闭**（以开始标签的结束而结束）

大多数 HTML 元素可拥有**属性**

一个元素可以设置它的属性 例如<a>链接

<a href="http://www.runoob.com">这是一个链接</a>

```html
<a href="http://www.runoob.com">这是一个链接</a>
```



钟诗宏

# 图像映射

```html
<img src="planets.gif" width="145" height="126" alt="Planets" usemap="#planetmap">

<map name="planetmap">
  <area shape="rect" coords="0,0,82,126" alt="Sun" href="sun.htm">
  <area shape="circle" coords="90,58,3" alt="Mercury" href="mercur.htm">
  <area shape="circle" coords="124,58,8" alt="Venus" href="venus.htm">
</map>
```

如果无法显示图像，浏览器将显示替代文本 :<alt=""/>(显示=后面的内容)值

描述x1,y1,x2,y2

如果 shape 属性设置为 "rect"，则该值规定矩形左上角和右下角的坐标。x,y,radius

如果 shape 属性设置为 "circ"，则该值规定圆心的坐标和半径。

x1,y1,x2,y2,..,xn,yn

如果 shape 属性设置为 "poly"，则该值规定多边形各顶点的值。如果第一个坐标和最后一个坐标不一致，那么为了关闭多边形，浏览器必                                              须添加最后一对坐标。
