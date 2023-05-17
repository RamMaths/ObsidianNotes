- We use the *let* keyword to declare variables *var* can also be used
- To concatenate a variable to a string we can do the follow
```js
let myName = "Ramses";

console.log("My name is ${myName}");
//we add a dollar sign followd by curly braises with the binding or the name of the variable
```
- with the function `typeOf()` we can know a type of a variable
```js
let myAge = 20;

console.log(typeOf(myAge));
//this will return "number";
```
- 
#### Math functions

random() this method return a random number between 0 and 1 so to get an upper value you need to multiply the upper bound that you want by this function. It is important to realize that this function generates a floor number between 0 and 1 so if you want an integer you need to use antoher function like `trunc()` or `floor()` and `ceil()`.

```js
//this is an example
//if we want an integer between 1 and 20 (exclusive)
let randomNum = Math.floor(Math.random() * 20);
```

#### Spread  and Rest Operators '...'
These two operators use the same notation but we are using spread while we use '...' at the right side of the equal sign and rest at left side

the spread operator will deconstruct any element of an iterable as individual element and this

```js
const name = 'Jonas';
console.log(...name);
//this will print
//['J', 'o', 'n', 'a', 's']
```

the rest operator will create a new array with the elements of an other array. The rest element must be the last element.
 ```js
// REST, because on LEFT side of = 
const [a, b, ...others] = [1, 2, 3, 4, 5];
console.log(a, b, others);
//this will print
// 1 2 [3, 4, 5]
 ```

Any variable that is created in a global scope in one script can be accessed in other scripts (of course in the same project)