## 前端javaScript

内容

* javascript基础
* DOM和BOM ——相当于编程语言的内置模块
* jQuart ——第三方模块

```html
<div>
    <div class="header" onclick="myFunc()">
    </div>
</div>
<script type="text/javascript">
	function myFunc() {
        alert("aaa");
        confirm("shifou")
    }
</script>
```

### 1.1 代码存放位置

```html
<link ...> / <style></style>
<script type="text/javascript"></script>

<body>
    ....
    <script type="text/javascript">
    </script>
</body>
```

js代码存在形式

* <script ...>标签内

* 其他js文件中

  ```html
  <script src=""></script>
  ```

  ```javascript
  function func() {
      
  }
  ```

  ### 1.2 注释

  * html中的注释

    ```html
    <!-- 注释 -->
    ```

  * css注释，style代码块

    ```css
    /* 注释 */
    ```

  * javascript，script代码块

    ```javascript
    // 注释
    /* 注释 */
    ```

  ### 1.3 变量

  ```javascript
  var name = "lihua";
  ```

  输出

  ```javascript
  console.log(name);
  ```

  ### 1.4 字符串

  ```javascript
  var name "lihua";
  // 字符串功能
  var v1 = name.lenth;
  var v2 = name[0]; // 只有正索引 name.charAt(3);
  var v3 = name.trim();
  var v4 = name.substring(1,2); // 前取后不取
  ```

  获取html标签内容

  ```html
  <div id="txt">text
  </div>
  <script type="text/javascript">
      // 获取标签对象 DOM
  	var tag = document.getElementById("txt");
     	// 获取标签内容
      var data = tag.innerText;
      // 更新内容
      tag.innerText = "lihua";
      functionn show() {}
      // 每秒执行一次show函数
      setInterval(show, 1000);
  </script>
  ```

  

  