```
try {
    var arr;
    for (var i=0;i<arr.length;i++) {
    console.log(i);
    }
} catch(e) {
    console.log(e);
    console.log(e.name);
    console.log(e.message);
    console.log(e.number);
} finally {
	console.log('finally');
}
```