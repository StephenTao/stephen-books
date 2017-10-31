## Enum 介绍

## Enum 使用
```java
public enum Light {

    // 利用构造函数传参
    RED(1), GREEN(3), YELLOW(2);

    // 定义私有变量
    private int nCode;

    // 构造函数，枚举类型只能为私有
    private Light(int _nCode) {
        this.nCode = _nCode;
    }

    @Override
    public String toString() {
        return String.valueOf(this.nCode);
    }
}

作者：Java红茶
链接：http://www.jianshu.com/p/3c3291ba1395
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 参考
* [Java enum的用法详解](http://www.jianshu.com/p/3c3291ba1395)
