
# slice method

Extracts a subarray from a array, passing one argument will be the index where the subarray starts (inclusive), and passing two arguments will be the start (inclusive) and the end (exclusive).

Giving a negative argument will mean that it is going to start from the end of the array.

```js
let arr = ['a', 'b', 'c', 'd', 'e'];

console.log(arr.slice(2));
console.log(arr.slice(2, 4));
console.log(arr.slice(-2));
console.log(arr.slice(1, -2));
console.log(arr.slice());
```

# splice method

It does almost the same but the difference is that slice creates another array and **splice** mutates the original array which called the splice method.

```js
let arr = ['a', 'b', 'c', 'd', 'e'];

console.log(arr.splice(2));
console.log(arr);
//this would log ['c', 'd', 'e']
```

# Data transformations with map, filter and reduce

![[Screen Shot 2023-01-20 at 11.00.20.png]]

# Which array method to use? 

![[Screenshot 2023-01-25 at 8.35.38.png]]
