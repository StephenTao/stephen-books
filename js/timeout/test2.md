```javascript
setTimeout(function() {
    console.log("setTimeout 0 ms");
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
-------------------------
VM1007:7 20
VM1007:8 function fn() {
    setTimeout(function() {
        console.log('setTImeout 10ms.');
    }, 10);
}
VM1007:22 30
undefined
VM1007:2 setTimeout 0 ms
VM1007:14 setTImeout 10ms.
VM1007:25 setTimeout 20ms.
```
