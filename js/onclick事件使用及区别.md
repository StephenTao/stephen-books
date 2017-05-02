## 给元素绑定click事件的方式：

例如HTML标签：
```html
<button type="submit" id="foo">Bar</button>
```

#### 方式一：jQuery绑定点击事件
```javascript
$("#foo").click(function(event) {
/* Act on the event */
});
```
或者
```javascript
$("#foo").on('click', function() {
    //function code here.
});
```
* 推荐使用的方式

#### 方式二： 原生js事件绑定
```javascript
document.getElementById('#foo').addEventListener('click', function() {
/* Act on the event */
}, false);
```
* 原生js，真正熟练者的使用工具

#### 方式三：HTML事件绑定，很多标签支持直接添加事件，我们也可以直接在button标签中使用onclick
```html
<button id="foo" onclick="dosomething()">Bar</button>
```
* 不建议使用方式三，html前端和js前端的工作混在了一起，难以分离工作任务，进而难以维护。


##区别：
`HTML或原生js是单一对应绑定的，绑多了只留最后一个。jQuery是追加绑定的，绑多少执行多少。`在单一绑定的条件下，由于jQuery底层其实也是js实现，所以速度区别并不大。至少“绑定”这个环节并不会成为速度的瓶颈.
HTML或原生js绑定的事件是复写的方式，后者（jQuery）是添加的方式，也就是说如果用jQuery给同一个按钮绑定两次点击事件，那么这两个事件都会被触发执行，而如果是原生js绑定的事件，则后面的会覆盖前面的.

[参考](https://segmentfault.com/q/1010000000332625)
