## JS闭包 ==> 就是能够读取其他函数内部变量的函数

### 作用
* 读取函数内部的变量
* 这些变量始终保存在内存中

### 闭包的本质：就是将函数内部和函数外连接起来的一座桥梁。

### 实例
```javascript
function outer() {
  var a = 100;
  function inner() {
    console.log(a);
  }
  return inner;// 间接的可以访问a，定义在一个函数内部的函数。
}

console.log(a);// error
var test = outer();
test();// can print log about a
```
### 闭包的其他写法
```javascript
(function () {
  //函数闭包
})();
```

### 再来一个实例
```javascript
var Status = function (status) {
  return {
    get_status : function() {
      return status;
    }
  };
};
var myStatus = Status("ok");
console.log(myStatus.get_status());
```
* 当Status函数返回时，get_status方法依然可以访问status，函数可以访问它被创建时所处的上下文环境。
