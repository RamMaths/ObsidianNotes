To train an AI, you’ll need to make sure your data is uniform, and you’ll need to understand and see it.

# Tensors

Tensors are collections of data in a structured type, it's up to you to choose how the data is ultimately formed. Tensors are the preferred format of information, and they even have small abstractions for nonnumeric types. They are like electrical signals from the physical world to our AI's brain.

A tensor as defined mathematically, is simply a structured set of values of any dimension. This resolves to an optimized grouping of data as numbers that are ready for calculation.

As you add a new dimension to each array, it's often said you are increasing the rank of a tensor.

- Rank or Order: The rank of a tensor corresponds to the number of indices needed to specify its components. It indicates the number of dimensions of the tensor. For example, a rank-one tensor is a vector, while a rank-two tensor is a matrix.
- Shape and Size: The shape of a tensor refers to the number of elements along each dimension. For instance, a tensor with shape (3, 4) has three elements along the first dimension and four elements along the second dimension. The size of a tensor represents the total number of elements it contains.
- Components: Tensors are composed of components or entries, which are numerical values assigned to each element of the tensor. These components can be real numbers, complex numbers, or even functions, depending on the context.

one-dimensional (1D) array

``` 
[1, 2, 3, 4];
```

.
.
.

## Creating tensors

```js
// creating our first tensor
const dataArray = [8, 6, 7, 5, 3, 0, 9] ;
// 1
const first = tf.tensor(dataArray);
// 2 does the same thing
const first_again = tf.tensor1d(dataArray);
```

The second method provides an extra level of runtime checking since you’ve defined the expected dimensionality. Methods exist for verifying up to six dimensions with `tf.tensor6d()`.

The default data type to store numbers is Float32. Floating-point numbers are quite dynamic and impressive. They can usually handle most numbers you’ll need and be ready for values between. Unlike JavaScript arrays, a tensor’s data type must be homogeneous (all the same type). These types can be only Float32, Int32, bool, complex64, or string, with no mixing between.

The second parameter of the `tf.tensor` function, defines the shape of the tensor.
The third parameter of the `tf.tensor` function, which explicitly defines the ten‐ sor’s type structure.

#### Tic-tac-toe example

```js
//1D Float 32 tensor
const a = tf.tensor([1, 0, 0, 0, -1, 0, 1, 0, 0]);
//2D Float 32 tensor
const b = tf.tensor(
  [1, 0, 0],
  [0, -1, 0],
  [1, 0, 0]
);
//1D input array converted into a 2D Float 32 tensor
const c = tf.tensor([1, 0, 0, 0, -1, 0, 1, 0, 0], [3, 3]);
//2D Int 32 tensor
const d = tf.tensor([1, 0, 0, 0, -1, 0, 1, 0, 0], [3, 3], 'int32');

```

Tensors are equipped with methods to explain their properties. Useful properties include length (size), dimensionality (rank), and data type (dtype).

```js
const second = tf.tensor1d([8, 6, 7, 5, 3, 0, 9]);
console.log(`Rank: ${second.rank}`);
console.log(`Size: ${second.size}`);
console.log(`Data type: ${second.dtype}`);
```

Tensors are immutable, once a tensor is created you're stuck with it. While you cannot modify this tensor, you can easily create a new tensor with the correct type and data. For this, you can use `asType` 

```js
const nope = tf.tensor([4], null, 'float32');
const yep = nope.asType('int32');
```

It's important to note these conversions are quick and dirty.If you have the value 3.9999 and you convert it to Int32, it becomes 3. There's no logic in bringing the value to the nearest Int32. Boolean tensors switch to 0 and 1, and string tensors will flat-out error. If you're converting  a tensor's data type, make sure you're ready for the results.

## Tensors provide speed 

When you convert JavaScript arrays of numbers to tensors, you can perform matrix operations at breakneck speeds. Tensor speed comes with an overhead cost. Usually the memory is cleanly removed when all references to that variable are completed. This is called *automatic garbage detection and collection (AGDC)*, your tensors don't get that same kind of automatic care. They persist long after the variable that uses them has been collected.

Even if a variable is garbage-collected in JavaScript, the associated tensor is then orphaned in memory. You can access the current count and size using `tf.memory()`. This function returns an object with a report of active tensors.

```js
let speedy = tf.tensor([1, 2, 3]);
speedy = null;

console.log(tf.memory().numTensors);
console.log(tf.memory().numBytes);
```

Tensors are for handling large accelerated data, the idea of leaving these sizable chunks in memory is a problem. With one small loop, you could leak an entire computer's available RAM and GPU. 

All tensors and models have a `.dispose()` method that purges a tensor from memory.When you call `.dispose()` on a tensor, the `numTensors` will go down by the number of tensors you just released.

| |tensor live| tensor disposed|
| :--: | :--: | :--: |
|Javascript variable is live|This variable is live; you can read the tensor|An error will be raised if you attempt to use this tensor.|
|JavaScript variable has no reference|This is a memory leak|This is a properly destroyed tensor|


Tensors do have an auto-clean option called tidy(). You can use tidy to create a functional encapsulation that will clean all tensors that aren't returned or flagged for being kept with `keep()`. 

```js
//start at zeros tensors
console.log('start', tf.memory().numTensors);

let keeper, chaser, seeker, beater;

//Now we'll create tensors inside a tidy
tf.tidy(() => {
  keeper = tf.tensor([1, 2, 3]);
  chaser = tf.tensor([1, 2, 3]);
  seeker = tf.tensor([1, 2, 3]);
  beater = tf.tensor([1, 2, 3]);
  //now we're at four tensors in memory
  console.log('indisde tidy', tf.memory().numTensors);

  //protect a tensor
  tf.keep(keeper);
  //returned tensors survive
  return chaser;
});

console.log('after tidy', tf.memory().numTensors);

keeper.dispose();
chaser.dispose();

console.log('end', tf.memory().numTensors);
```

The tidy method takes a synchronous function, If you're going to need anything async, you will  have to call `.dispose` explicitly.

You can even mix tensors and JavaScript where applicable.

```js
const tensorArray = [];
for(let i=0; i<10; i++) {
	tensorArray.push(tf.tensor([i,i,i]));
}
```

The result is an array of 10 tensors. Unlike creating a 2D tensor to hold these values, you can access a particular tensor with ease by retrieving a normal JavaScript index in the array. You can then destroy the whole collection from memory with a simple `tf.dispose(tensorArray)`.

## Retrieving Tensor Data

There are two ways to retrieve tensors. Each of these methods has a synchronous method and an asynchronous method. You can use `.array()` /  `.arraySync()` if you'd like your data to be delivered in the same multidimensional array structure. Use `.data()` / `.dataSync()`  If you'd like to keep your values with extreme precision and flattened to a 1D typed array.  `print()` gives us an actual printout of the internal value directly to the console.

```js
const snap = tf.tensor([1, 2, 3]);
const crackle = tf.tensor([3.141592654]);
const pop = tf.tensor([[1, 2, 3], [4, 5, 6]]);

//this will show the structure but not the data 
console.log(snap);
//this will print the data but not the tensor structure
crackle.print();

// Now let's go back to JavaScript 
console.log('Welcome Back Array', pop.arraySync());
console.log('Welcome Back Array', pop.dataSync());

//clean up our remaining tensors!
tf.dispose([snap, crackle, pop]);
```

## Tensors and mathematics 

Tensors have mathematical benefits. While writing custom code would not be a problem, it would be unoptimized and redundant. Useful, scalable mathematical operations are builtin. TensorFlow.js makes linear algebra accessible and optimized for structures like tensors.

![[Screenshot 2023-05-17 at 12.29.22.png]]

```js
const mat1 = [
  [91, 82, 13],
  [15, 23, 62],
  [25, 66, 63]
];

const mat2 = [
  [1, 23, 83],
  [33, 12, 5],
  [7, 23, 61]
];

tf.matMul(mat1, mat2).print();
```

![[Screenshot 2023-05-17 at 12.30.40.png]]

