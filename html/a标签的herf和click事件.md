### Test code

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
    <h1>A 标签的使用</h1>
    <hr><br>
    <div>
        <h4>只设置onclick事件 => 无超链样式</h4>
        <a onclick="javascript:alert('click A1');">A1</a><hr>
        <h4>设置onclick，href="#"=> 跳转到页面顶部</h4>
        <a href="#" onclick="javascript:alert('click A2');">A2</a><hr>
        <h4>设置onclick，href="" => 跳转到空白页面</h4>
        <a href="" onclick="javascript:alert('click A3');">A3</a><hr>
        <h4>只设置href事件 代替onclick事件 => 超链样式不跳转</h4>
        <a href="javascript:alert('Click A4');">A4</a><hr>
    </div>
</body>
</html>
```

### Test result
![]()


### 参考
* [a标签的herf和click事件](https://www.cnblogs.com/zbx123/p/3495300.html)
