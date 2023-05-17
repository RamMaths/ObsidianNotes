‹# DOM Manipulation

DOM (Document Object Model) is a representation of html documents. allows javascript to acces html eleemtns and styles to manipulate them.

![[Screen Shot 2022-12-11 at 9.30.55.png]]


## querySelector() 

this method returns the first element within the document that matches the specified selector (this can be a css class name).

```js
//look at what sign we use if the selector is an id or a class
document.querySelector("#idName");
document.querySelector(".className");
```

it's important to say that if we have more than one element named like what we passed in the querySelector() arguments, we are going to get only the first that's why we can use another mehtod in these cases which is the following.

#### textContent method

 this will  let you access to the content of a specific element provided with the querySelector() like below
 
```js
console.log(document.querySelector(".className").textContent);
```

#### value method

this method let you access to the value of an input html tag 

```js
console.log(document.querySelector(".className").value);
```

#### addEventListener() method

this mehtod it's like a trigger which is activated when an event is happening this method has two parameters the first one is the type of event and the second one what do we want to do with that event.

```js
document.querySelector('.className').addEventListener('click', someFunction());
```

this method has three different types of events for the keboard, they are
- **keyup** : this event will trigger when  we left our fingers off the keboard
- **keydown** : this event will trigger as soon as we press any key on the keyboard
- **keypress**: this is fired continously as we keep our finger on a certain key 


## querySelectorAll() 
it's likely to the method above but this let you acces to more than one element with the same class name.

## classList 

This allows for manipulation of element's class content attribute as a set of whitespace-separated tokens through a DOMTokenList object.

**Example**
imagine that we have a tag like the follow 
```html
<div class="modal hidden">
```

what the class hidden adds is the property display: none;
so in order to unhide this element we would do something with the following methods

#### remove() 
removes all arguments passed, if they are present.

so if we keep working in the example above to unhide the tag mentioned we would do

```js
const modal = querySelector('.modal');

// note that we don't use the dot with these methods
modal.classList.remove('hidden');
```

#### add()
adds all arguments passed, except those already present.
so in order with the example above; to hide again this element we would do

```js
modal.classList.add('hidden');
```

#### contains()
