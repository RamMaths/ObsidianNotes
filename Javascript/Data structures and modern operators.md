# Sets
sets can only save unique values

### size

```js
const ordersSet = new Set (['Pasta', 'Pizza', 'Pizza', 'Risotto',  'Pasta', 'Pizza']);

//the size of a set
console.log(ordersSet.size);
//this will print 3
```

### has
this mehod returns a boolean

```js
const ordersSet = new Set (['Pasta', 'Pizza', 'Pizza', 'Risotto',  'Pasta', 'Pizza']);

// knowing if an element is actually inside a set
console.log(ordersSet.has('Pizza'));
console.log(ordersSet.has('Bread'));
```

### add

```js
const ordersSet = new Set (['Pasta', 'Pizza', 'Pizza', 'Risotto',  'Pasta', 'Pizza']);

// adding elements to a set
console.log(ordersSet.add('Garlic Bread'));
```

### delete

```js
const ordersSet = new Set (['Pasta', 'Pizza', 'Pizza', 'Risotto',  'Pasta', 'Pizza']);

// adding elements to a set
console.log(ordersSet.delete('Garlic Bread'));
```

### clear

```js
const ordersSet = new Set (['Pasta', 'Pizza', 'Pizza', 'Risotto',  'Pasta', 'Pizza']);

// delete all the elements in a set
console.log(ordersSet.clear());
```


![[Screen Shot 2023-01-11 at 9.18.32.png]]