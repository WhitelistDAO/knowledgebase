#js基础知识
####1. js的组成
js主要分为BOM（操作浏览器的部分）、DOM(操作文档流的内容)、ECMAScript（js的语法和书写规则）三个部分。

####2. js的书写位置
2.1 行内式——直接把code写在标签上
   a标签——书写在href属性上     
    非a标签——书写在行为属性上

  2.2 内嵌式——把code写在script标签对内（不需要依赖任何行为，打开页面就会执行）
  ```
         <script>
        alert('hello world')            
        </script>
        ```
   2.3 外链式——把code书写在一个.js文件内（不需要依赖任何行为，打开页面就会执行）(推荐使用）
   ```
       src=""--引入js文件的属性，括号内是需要引入的js文件路径
       text.js中只需要想要执行的代码，其他不需要 e.g.alert('hello world')  
       <script src="./text.js"></script>
       ```
####3. js定义变量
1. var 定义的变量可以修改，如果不初始化会输出undefined，不会报错。
2. let let是块级作用域，定义的变量只在let 命令所在的代码块内有效，变量需要先声明再使用。
3. const 定义的变量不可以修改，而且必须初始化，定义的是一个恒定的常量
4. 格式 var num = 100  （一个变量只会存储一个值，只保存当前的值）（var 相当于一个容器，存储一个名为num的变量）
```
<script>
 var num =100
 alert(num)--出现100的弹窗
 console.log(num)  //在控制台打印num的值
 document.write(num)  //直接在页面输出num的值
</script>
```
5. 变量可以由数字 字母 下划线 美元符组成（不能以数字开头，不能使用空格和其他标点符号，区分大小写）
6. 变量名必须有意义
7. 遵守驼峰命名法。首字母小写，后面单词的首字母需要大写。例如：userName、userPassword