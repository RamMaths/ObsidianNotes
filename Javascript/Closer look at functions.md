# First-class vs higher-order functions

## First-class functions

Javascript treats functions as first-class citizens, this means that funcftions are simply values, function are just another "type" of object 
![[Screen Shot 2023-01-18 at 9.14.02.png]]



## Higher order functions

A function that recives another function as an argument, that returns a new function, or both.
This is only possible because of first-class functions

![[Screen Shot 2023-01-18 at 9.19.13.png]]

## Example

```js
//first-class functions (these have the lower level of
//abstraction because they have a solid functionality)
const upperFirstWord = function (str) {
	const [first, ...others] = str.split(' ');
	return [first.toUpperCase(), ...others].join(' ');
};

const noSpaces = function (str) {
	return str.replace(/ /g, '').toLowerCase();
};

//this is the higher-level function
//and this has the greater level of abstraction
//because the transform function don't worry about how it is going
//to transform the string
const transform = function (str, fn) {
	console.log(`Original string: ${str}`);
	console.log(`Transformed string: ${fn(str)});
};
```