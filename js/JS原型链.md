
## prototype对象
所有实例对象需要共享的属性和方法，都放在这个对象中，那些不需要共享的属性和方法，就放在构造函数中。

## 原型链
在javascript中，每个对象都有一个指向它的原型(prototype)对象的内部链接。每个原型对象又有自己的原型，直到某个对象的原型为null为止，组成这条链的最后一环。

## 参考
* [Javascript继承机制的设计思想](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)
* [Javascript原型链与继承](http://www.jianshu.com/p/826b485c5696)
