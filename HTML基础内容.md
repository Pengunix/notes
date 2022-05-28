## HTML基础内容

### <!DOCTYPE>标签

**文档类型声明标签** 当前页面表示使用html5显示页面

必须写在第一行，不是html标签

### lang语言种类

```html
<!DOCTYPE html>
<html lang="zh-CN"></html>
```

不同的lang属性可能会触发浏览器翻译工具

### 声明字符集

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
</html>
```

常用字符集：GB2312、BIG5、GBK、UTF-8

utf-8也被称为万国码，最为常用

**在vscode输入!自动生成**

### 标题标签（双标签）

1. 共六个标签等级<h1\>到<h6\>

2. 带有结束标签</h1\>

3. 从一标签到六标签字体大小递减

4. 独占一行

### 段落与换行标签

```html
<p>段落标签</p>
<p>段落标签2</p>
```

不使用标签源代码中的空格无论多少只显示一个

特点：

1. 段落中文本根据浏览器大小自动换行
2. 段落之间保留空隙

```html
<br />
```

在html中，一个段落中的文字会从左到右排列，直到浏览器右端，然后才会自动换行。如果希望某段文本强制换行显示，则会用到**换行标签 <br /\>**

br为单词break的缩写

### 文本格式化标签

在网页中，有时需要为文本设置粗体、斜体或下划线效果, 这时就需要html中的文本格式化标签，使文字以特殊方式显示。

标签语义：突出重要性，比普通文字重要

| 语义   | 标签                         | 说明                                |
| :----- | :--------------------------- | ----------------------------------- |
| 加粗   | <strong></strong>或者<b></b> | 更推荐使用strong标签加粗 语义更强烈 |
| 倾斜   | <em></em>或者<i></i>         | 更推荐使用em                        |
| 删除线 | <del></del>或者<s></s>       | 更推荐使用del                       |
| 下滑线 | <ins></ins>或者<u></u>       | 更推荐使用ins                       |

### div与span标签（双标签）

<div\>和<span\>没有语义，目的使用来创建“盒子”布局网页

div是division的缩写，表示分割 span意为跨度，跨距。

特点：

1. div标签用来布局，但一行只能放一个div
2. span一行可有多个，span之间留有空隙

### HTML常用标签

图像标签和路径

1. 图像标签(单标签)

   <img\>标签用于定义html页面中的图像。

   ```html
   <img src="图像url"/>
   ```

   src是img标签的必须属性，用于制定图像文件的路径

   | 属性   | 属性值   | 说明                                 |
   | ------ | -------- | ------------------------------------ |
   | src    | 图片路径 | 必须属性                             |
   | alt    | 文本     | 替换文本，图像不能显示时出现的文字   |
   | title  | 文本     | 提示文本，鼠标放在图像上，显示的文字 |
   | width  | 像素大小 | 设置图像宽度                         |
   | height | 像素大小 | 设置图像高度                         |
   | border | 像素大小 | 设置图像的边框粗细(一般通过css设定)  |

   ```html
   <img src="" alt="" title="" width="450" height="450" border="15">
   ```

   width与height均不剪切图片，而时对对应方向像素点压缩

2. 路径

   目录文件夹：存放网站素材

   根目录：文件夹第一层（一般为index所在位置）

3. 相对路径与绝对路径

   同级路径：" "
   
   子级路径：“/”
   
   父级路径：“../”
   
   ```html
   <img src="demo.gif"/>
   <img src="images/demo.gif"/>
   <img src="../baidu.gif"/>
   ```
   
   绝对路径为主机路径或者图片url
   
   

### 超链接标签

```html
基本语法
<a href="目标url" target="目标窗口弹出方式">文本或图像</a>
```

| 属性   | 作用                                                         |
| ------ | ------------------------------------------------------------ |
| href   | 用于指定链接目标的url，（必需属性）当存在href才会拥有超链接功能 |
| target | 用于指定链接页面打开方式，其中_self为默认值, __blank为在新窗口打开 |

<a\>为单词anchor的缩写，意为 锚

**链接分类**

1. 外部链接：带有http://

2. 内部链接：无需http:// 例如 href="demo.html"

3. 空链接：href="#" 暂时替换

4. 下载链接：href="http://demo.com/a.zip"如果url指向文件，则点击会下载这个文件

5. 网页元素链接：在网页中的各种网页元素，如文本、图像、表格、音频、视频等都可以添加超链接

   ```html
   <a href="http://baidu.com"><img src="a.jpg"/></a>
   ```

6. 锚点链接：点击链接跳转到页面中的某个位置

   * 设置href属性为**#名字**的形式，如

     ```html
     <a href="#two">第二季</a>
     <h3 id="two">Some text.</h3>
     ```

   * 找到目标位置标签，添加id属性

### 注释标签

```html
<!--some description-->
```

### 特殊字符

显示一些符号

| 特殊字符 | 描述     |           |
| -------- | -------- | --------- |
|          | 空格符   | \&nbsp;   |
| <        | 小于号   | \&lt;     |
| >        | 大于号   | \&gt;     |
| &        | 和       | \&amp;    |
| ￥       | 人民币   | \&yen;    |
| ©        | 版权     | \&copy;   |
| ®        | 注册商标 | \&reg;    |
| °        | 摄氏度   | \&deg;    |
| ±        | 正负号   | \&plusmn; |
| ꭓ        | 乘       | \&times;  |
| ÷        | 除       | \&divide; |
| ²        | 平方     | \&sup2;   |
| ³        | 立方     | \&sup3;   |

### 表格标签

用于展示数据

```html
<!--基本语法-->
<table>
    <tr>
        <td>单元格内文字</td>
        ...
    </tr>
    ...
</table>
<table>
    <tr><td>a</td><td>b</td><td>c</td></tr>
    <tr><td>1</td><td>2</td><td>3</td></tr>
</table>
<!--a b c
    1 2 3-->
```

1. <table\></table\>用于定义表格标签
2. <tr\></tr\>定义表格中的行，必须嵌套在<table\>标签中
3. <td\></td\>定义表格中的单元格，必须嵌套在<tr\>中
4. 字母td指表格数据（table data）

**表头单元格标签**

```html
<table>
    <tr><th>a</th><th>b</th><th>c</th></tr>
    <tr><td>1</td><td>2</td><td>3</td></tr>
</table>
```

表头单元格内文字会**加粗居中**显示

**表格属性（通常通过css设置）**

| 属性名      | 属性值              | 描述                                       |
| ----------- | ------------------- | ------------------------------------------ |
| align       | left, center, right | 规定表格相对周围元素的对齐方式             |
| border      | 1或""               | 规定单元格是否拥有边框，默认为"", 没有边框 |
| cellpadding | 像素值              | 规定单元格沿与其内容之间的空白，默认1px    |
| cellspacing | 像素值              | 规定单元格之间的空白，默认2px              |
| width       | 像素值或百分比      | 规定单元格的宽度                           |

```html
<table align="center" border="1" cellpadding="20" cellspacing="0" width="500" height="249">
    <tr><th>a</th><th>b</th><th>c</th></tr>
    <tr><td>1</td><td>2</td><td>3</td></tr>
</table>
```

**表格结构标签**

当表格非常长时，为了更好表示表格的语义，可以将表格分割成表格头部和表格主体两大部分。

```html
<table align="center" border="1" cellpadding="20" cellspacing="0" width="500" height="249">
    <thead>
    <tr><th>a</th><th>b</th><th>c</th></tr>
    </thead>
    <tbody>
    <tr><td>1</td><td>2</td><td>3</td></tr>
    </tbody>
</table>
```

**合并单元格**

合并单元格方式：

1. 跨行合并：rowspan="合并单元格的个数"
2. 跨列合并：rowspan="合并单元格的个数"

目标单元格：

1. 跨行：最上侧单元格为目标单元格，写合并代码
2. 跨列：最左侧单元格为目标单元格，写合并代码

步骤：

1. 确定是跨行合并还是跨列合并
2. 找到目标单元格，写上合并方式=合并单元格数量，例如：<td colspan="2"\></td\>
3. 删除多余单元格

```html
<table align="center" border="1" cellpadding="20" cellspacing="0" width="500" height="249">
    <thead>
    <tr><th>a</th><th>b</th><th>c</th></tr>
    </thead>
    <tbody>
    <tr><td>1</td><td colspan="2"></td></tr>
    </tbody>
</table>
```

### 列表标签

列表用来布局数据

1. 无序列表：<ul\>标签表示无序列表，列表项使用<li\>来定义

   ```html
   <ul>
       <li>1</li>
       <li>2</li>
   </ul>
   ```

   无序列表各个列表项之间没有顺序级别之分，是并列的。

   ˂ul˃˂/ul˃中只能嵌套˂li˃˂/li>，不能直接在ul标签中输入其他标签或文字。

   ˂li>˂/li>之间相当于一个容器，可以容纳所有元素。

   无序列表会带有自己的属性，但在实际应用中，我们会用css来设置

2. 有序列表

   ```html
   <ol>
       <li>1</li>
       <li>2</li>
   </ol>
   ```

   性质与无序列表相同

3. 自定义列表

   自定义列表常用于对术语或名词进行解释描述，定义列表的列表项前没有任何项目符号

   ```html
   <dl>
       <dt>名词1</dt>
       <dd>名词解释1</dd>
       <dd>名词解释2</dd>
   </dl>
   ```

   ˂dl></dl\>里面只能包含<dt\>和<dd\>

   <dt\>和<dd\>个数没有字数限制，经常是一个<dt\>对应多个<dd\>

   

   ### 表单标签

   使用表单的目的是收集信息

   1. 表单的组成

      一个完整的表单通常由**表单域 表单控件（也称为表单元素）和提示信息**三部分组成

      <form\>用于定义表单域，实现用户信息的收集与传递
      
      ```html
      <form action="url地址" method="提交方式" name="表单域名称">
          各种控件
      </form>
      ```
      
      常用属性
      
      | 属性   | 属性值   | 作用                                           |
      | ------ | -------- | ---------------------------------------------- |
      | action | url地址  | 用于指定接收并处理表单数据的服务器程序地址     |
      | method | get/post | 用于设置表单数据的提交方式                     |
      | name   | 名称     | 用于指定表单名称，以区分同一页面中的多个表单域 |

2. 表单控件（表单元素）

在表单域中可以定义各种表单元素，这些表单元素就是允许用户在表单中输入或者选择的内容控件。

有input输入 select下拉 textarea文本区域等元素

1. <input\>表单元素

   ```html
   <input type="属性值" />
   ```

   type属性值及描述

   | 属性值   | 描述                                                   |
   | -------- | ------------------------------------------------------ |
   | button   | 定义可点按钮（多数情况下用于通过javascript启动脚本）   |
   | checkbox | 定义复选框                                             |
   | file     | 定义输入字段和“浏览”按钮，供文件上传                   |
   | hidden   | 定义隐藏的输入字段                                     |
   | image    | 定义图像形式的提交按钮                                 |
   | password | 定义密码字段 该字段中的字符被掩码                      |
   | radio    | 定义单选按钮                                           |
   | reset    | 定义重置按钮 清除表单所有数据                          |
   | submit   | 定义提交按钮 把表单数据发送给服务器                    |
   | text     | 定义单行输入字段 用户在其中输入文本 默认宽度为20格字符 |

   例子

   ```html
   <form>
       用户名：<input type="text"> <br>
       密码：<input type="password"> <br>
       性别：男 <input type="radio"> 女<input type="radio">
       爱好：c <input type="checkbox"> t <input type="checkbox"> r <input type="checkbox"> l <input type="checkbox">
   </form>
   ```

2. name 与 value属性

   name 属性值由用户自定义 用来定义input元素名称

   ```html
   <form>
       用户名：<input type="text" name="username"> <br>
       密码：<input type="password" name="password"> <br>
       <!-- name为表单元素名字 性别单选按钮必须有相同的名字 才可实现多选一-->
       性别：男 <input type="radio" name="sex"> 女 <input type="radio" name="sex"> <br>
       爱好：c <input type="checkbox" name="hobby"> t <input type="checkbox" name="hobby"> r <input type="checkbox"> l <input type="checkbox">
   </form>
   ```

   value 属性值由用户定义 规定input元素的值

   ```html
   <form>
       用户名：<input type="text" name="username" value="请输入用户名..."> <br>
       性别：男 <input type="radio" name="sex" value="男"> 女 <input type="radio" name="sex" value="女">
   </form>
   ```

   两个属性值主要是给后台人员使用

3. checked与maxlenth

   checked属性值为checked 规定此input元素首次加载时应当被选中

   ```html
   <form>
       <!-- 单选和复选框可以设置checked属性，当页面打开是可以默认选中这个-->
       性别：男 <input type="radio" name="sex" value="男" checked="checked"> 女 <input type="radio" name="sex" value="女">
   </form>
   ```

   maxlenth 属性值为正整数 规定输入字段的字符最大长度

   ```html
   用户名：<input type="text" name="username" value="请输入用户名..." maxlenth="5">
   ```

4. type中submit与reset

   ```html
   <!--提交按钮-->
   <input type="submit" value="按钮文本">
   <!--重置按钮-->
   <input type="reset" value="按钮文本">
   ```

5. type中button与file

   ```html
   <!--普通按钮-->
   <input type="button" value="按钮文本">
   <!--文件域-->
   <input type="file">
   ```

6. select下拉表单元素

   在页面中，如果有多个选项让用户选择，并且想要节约页面空间时，我们可以使用<select\>下拉标签定义下拉列表

   ```html
   <select>
       <option>选项1</option>
       <option>选项2</option>
       ...
   </select>
   ```

   <select\>中至少包含一对<option\>

   在<option\>中定义selected="selected"时，当前项默认被选中

7. textarea文本域表单元素

   当用户输入内容较多的情况下，我们可以使用<textarea\>标签

   其用于定义多行输入

   ```html
   <textarea rows="3" cols="20">
   文本内容
   </textarea>
   ```

   cols与rows属性不经常使用，都用css改变大小

### label标签

label标签为input元素标注

label标签用于绑定一个表单元素，当点击label标签内文本时，浏览器就会自动将焦点（光标）转到或者选择对应的表单元素上

```html
<label for="sex">male</label>
<input type="radio" name="sex" id="sex" />
```

lable标签的*for属性*应当与相关元素的*id属性***相同** 

