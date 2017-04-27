```javascript
setTimeout(function() {
  console.log(1)
}, 0);
new Promise(function executor(resolve) {
  console.log(2);
  for( var i=0 ; i<10000 ; i++ ) {
    i == 9999 && resolve();
  }
  console.log(3);
}).then(function() {
  console.log(4);
});
console.log(5);
```

```
----------------------------------
VM1106:5 2
VM1106:9 3
VM1106:13 5
VM1106:11 4
undefined
VM1106:2 1
```
