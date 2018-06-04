## JS Function Tail Call

### 用js写一个Fibonacci函数，`定义F(1)=1，F(2)=1, F(n)=F(n-1)+F(n-2)（n>=2，n∈N*)`
#### 1. 一般都会这么写，看似乎没有毛病。
```javascript
function Fibonacci(n) {
	if (n === 1 || n === 2) {
		return 1;
	}
	return Fibonacci(n - 1) + Fibonacci(n - 2);
}
Fibonacci(10); //55
Fibonacci(100); //堆栈溢出
Fibonacci(1000); //堆栈溢出
```
#### 2. 这是什么原因呢？
递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stackoverflow）。但对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。 我们知道，函数调用会在内存形成一个“调用记录”，又称“调用帧”（call frame），保存调用位置和内部变量等信息。如果在函数A的内部调用函数B，那么在A的调用帧上方，还会形成一个B的调用帧。等到B运行结束，将结果返回到A，B的调用帧才会消失。如果函数B内部还调用函数C，那就还有一个C的调用帧，以此类推。所有的调用帧，就形成一个“调用栈”（call stack）。


#### 3. 优化解决
尾调用（Tail Call）是函数式编程的一个重要概念，本身非常简单，一句话就能说清楚，就是指某个函数的最后一步是调用另一个函数。
尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用帧，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用帧，取代外层函数的调用帧就可以了。
```javascript
function Fibonacci2(n, temp = 1, result = 1) {
	if (n === 1 || n === 2) {
		return result;
	}
	return Fibonacci2(n - 1, result, temp + result);
}
Fibonacci2(100); //354224848179262000000
Fibonacci2(1000); //4.346655768693743e+208
Fibonacci2(10000); //Maximum call stack size exceeded,Infinity
```

#### 4. Example Factorial `F(n)= n * F(n - 1)（n∈N*)`
```javascript
function Factorial(n) {
	if (n === 1) {
		return 1;
	}
	return n * Factorial(n - 1);
}
```
上面代码是一个阶乘函数，计算n的阶乘，最多需要保存n个调用记录，复杂度 O(n) 。
如果改写成尾递归，只保留一个调用记录，复杂度 O(1) 。
```javascript
function Factorial2(n, total = 1) {
  if (n === 1) return total;
  return Factorial2(n - 1, n * total);
}
```
## 参考

* [ES6尾调用优化](http://es6.ruanyifeng.com/#docs/function#%E5%B0%BE%E8%B0%83%E7%94%A8%E4%BC%98%E5%8C%96)
