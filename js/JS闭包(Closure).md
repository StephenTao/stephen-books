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

### 补充实例

```javascript  
var fn = function() {
    var n = 0;
    
    add = function() {
        n++;
    }
    
    f2 = function () {
        console.log(n);
    }
    
    return f2;
}  

var test = fn();
test();//输出0
add();//可以执行函数
test();// 此刻输出1
var test1 = fn();
test1();// 输出0
test();//输出1
add();
test1();//输出1
test();//输出1
```
* 当函数fn返回后，我们依然可以访问add(),还可以访问并维护它创建时的上下为数据n。
* 如果我再创建一个test1 = fn(); test()依然可以执行，找到最近的数据n，但是我们在执行add()时，add执行的就是test1的add().
* add()因为没写 var/let 所以相当于window声明，后面任意时刻都可以调用


### 参考
1. [学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
