## Java transient 关键字

## 单词词义
```
transient

英 ['trænzɪənt]  美 [ˈtrænʃnt; trænʃənt]
n. 瞬变现象；过往旅客；候鸟
adj. 短暂的；路过的
```

## 特点
* transient 关键字只能修饰变量，不能修饰类、方法。
* 被 transient 关键字修饰的变量不能被自动序列化（实现Serializable接口）。
* 被 static 关键字修饰的变量，无论是否被 transient 关键字修饰，都不能被自动序列化。
* 被 static 关键字修饰的变量，在反序列化时，看似被得到了之前序列化对象的值了，其实不然，是 JVM 方法区中的动态属性，非反序列化后的变量。
* 一个类若实现了 Externalizable 接口，它的属性无论是被 static 还是 transient 修饰，只要该属性被应用到Externalizable接口的writeExternal、readExternal方法中，都能够实现序列化，不过这是手动序列化。

## 作用
在一个可序列化的对象中，通过增加transient限定可以指定不被序列化的属性。

## 参考

* [Java transient 关键字](https://zhuanlan.zhihu.com/p/25558135)
