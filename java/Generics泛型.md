## 1. 是否使用泛型对比

### 1.1 没有泛型以前
```java
List list = new ArrayList();
list.add("123");
list.add("456");
```
`存在的问题`：
* 当一个对象放入集合时，集合不会记住此对象的类型，当再次从集合中取出此对象时，该对象的编译类型变成了Object
* 运行时需要人为地强制转换类型到具体目标，实际的程序绝不会这么简单，一个不小心就会出现java.lang.ClassCastException，即类型转换异常

### 1.2 使用泛型
泛型是对Java语言类型系统的一种扩展，有点类似于C++的模板，可以把类型参数看作是使用参数化类型时指定的类型的一个占位符.
```java
List<String> list = new ArrayList<String>();
list.add("123");
list.add("456");
```
`优点`：
* 类型安全。类型错误现在在编译期间就被捕获到了，而不是在运行时当作java.lang.ClassCastException展示出来，将类型检查从运行时挪到
编译时有助于开发者更容易找到错误，并提高程序的可靠性
* 消除了代码中许多的强制类型转换，增强了代码的可读性
* 为较大的优化带来了可能

## 2. 泛型getClass()相同
```java
List<String> stringList = new ArrayList<String>();
List<Integer> integerList = new ArrayList<Integer>();
System.out.println(stringList.getClass() == integerList.getClass()); // true
```
`这就意味着不能依据泛型的类型不同而去实现以泛型作为参数的方法的重载。`

## 3. 泛型的类型通配符
List<Object>不是List<String>的父类型，List<Integer>不是List<Number>的父类型。
`<?>是类型通配符，表示是任何泛型的父类型`

### 3.1 方法可以泛型化
```java

```

### 3.2 静态资源不认识泛型


## 4. 泛型约束<T extends Object>


## 参考
[Java 泛型](http://wiki.jikexueyuan.com/project/java-special-topic/generic.html)  

[Java泛型详解](http://www.importnew.com/24029.html)  

[Java语法糖（3）：泛型](http://www.importnew.com/22529.html)
