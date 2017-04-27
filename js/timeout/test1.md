```javascript
setTimeout(function() {
    console.log(a);
}, 0);

var a = 10;

console.log(b);
console.log(fn);

var b = 20;

function fn() {
    setTimeout(function() {
        console.log('setTImeout 10ms.');
    }, 10);
}

fn.toString = function() {
    return 30;
}

console.log(fn);

setTimeout(function() {
    console.log('setTimeout 20ms.');
}, 20);

fn();
```


```
--------------------------------------------
VM964:7 undefined
VM964:8 function fn() {
    setTimeout(function() {
        console.log('setTImeout 10ms.');
    }, 10);
}
VM964:22 30
undefined
VM964:2 10
VM964:14 setTImeout 10ms.
VM964:25 setTimeout 20ms.
```

