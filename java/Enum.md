## Enum 介绍

## Enum 使用
* 1. 定义常量
* 2. switch支持
* 3. 枚举类可以添加构造方法，可以实现接口，真正当作一个类来使用。

```java
package com.openstephen.utils;

public class TestEnum {
    
    interface TrafficLightAction {
        void notifyMsg();
    }

    enum TrafficLight implements TrafficLightAction {

        // 利用构造函数传参
        //如果打算自定义自己的方法，那么必须在enum实例序列的最后添加一个分号。而且 Java 要求必须先定义 enum 实例
        RED("red", "红灯停"), GREEN("green", "绿灯行"), YELLOW("yellow", "黄灯亮了等一等");

        // 定义私有变量
        private String colour;
        private String msg;

        public String getColour() {
            return colour;
        }

        public String getMsg() {
            return msg;
        }

        // 构造函数，枚举类型只能为私有
        private TrafficLight(String _colour, String _msg) {
            this.colour = _colour;
            this.msg = _msg;
        }

        // 重写父类方法
        @Override
        public String toString() {
            return String.valueOf(this.colour);
        }

        // 实现接口方法
        @Override
        public void notifyMsg() {
            System.out.println(this.msg);
        }
    }

    public static void main(String[] args) {
        TrafficLight trafficLight = TrafficLight.RED;
        
        switch(trafficLight){
        case RED : {
            TrafficLight.RED.notifyMsg();
            break;
        }
        case GREEN: {
            TrafficLight.GREEN.notifyMsg();
            break;
        }
        case YELLOW : {
            TrafficLight.YELLOW.notifyMsg();
            break;
        }
        default:
            break;
        }
    }

}

```

## 参考
* [Java enum的用法详解](http://www.jianshu.com/p/3c3291ba1395)
