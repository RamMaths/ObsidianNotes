# JAVASCRIPT

Javascript is a ==high-level==, ==prototype-based object-oriented==, ==multi-paradigm==, ==interpreted== or just-in-tme compile, ==dynamic==, ==single threaded==, ==garbage-collected== programming language with ==first-class functions== and a non-blocking ==event loop concurrency model==.

### High-level

In these kind of languages the developers do not have to worry about any computer resources, everything happens automatically.ahout 

### Garbage collector

It is basically an algorithm inside th ejavaascript engine which automatically removes old and unused object from the computer memory  in order not to plugged out with unneccesary stuff.

### Multiparadigm

**Paradigm**: An approach and mindset of structuring code, which will direct your coding style and technique.

- Procedural programming
- Object-oriented programming
- Functional programming

### First-class functions
In a langauage with first-class functions, functions are simply treated as variables. We can paass them into other functionsk and return them from fuctions.

### Dynamic
In javascript we don't assign data types to variables instead they only became noun when the javascript's engine executes our code. Also  the type of variables can easy be changed when we reassign variables.

### Non-blocking event loop 

##### Concurrency model
How javascript's engine handles multiple tasks happening at the same time.
- **Why do we need that?** JavaScript runs in one single thread, so it can only do one thing at a time.

![[Screen Shot 2022-12-19 at 7.32.52.png]]

# What is javascript engine
Program that executes javascript code. Every borwser has its own javascript engine. The most famous could be V8 engine from google 
![[Screen Shot 2022-12-19 at 7.36.56.png]]

## How modern javascript engine works

![[Screen Shot 2022-12-19 at 7.48.17.png]]

## What is an execution context 

Environment in which a piece of JavaScript is executed. Stores all the neccesary information for some code to be executed. Such as local variables or arguments.
In every javascript project there is only one execution context.

![[Screen Shot 2022-12-19 at 8.13.28.png]]

# Scopes 

## Scoping
How our program variables are organized and accessed "Where do variables live?" or "Where can we access a certain variable and where not?"
## Lexical scoping
The way the variables are organized and accesseed is entierly controlled by the placement of functions and of blocks in the program's code. 
## Scope
Space or environment in which a certain variable is declared (variable environment in case of functions). There is **global** scope, **function** scope and **block** scope;

![[Screen Shot 2022-12-19 at 8.51.37.png]]

- **block scoped** -> let, const and function keyword
- **function scoped** -> var

## Scope chain
Order in which functions are written in the code. It is important to say that the scope chain has **nothing** to do with order in which functions were called`.

![[Screen Shot 2022-12-19 at 9.03.09.png]]

## Hoisting
Makes some types of variables accessible/usable in the code before they are actually decalred. "Variables lifted to the top of their scope". Before execution, code is scanned for variable declarations, and for each variable, a new property is created in the **variable environment object**.

![[Screen Shot 2022-12-19 at 9.48.50.png]]

## "this" keyword

- it is an especial variable that is created for every execution context (every function). ==Takes the value of (points to) the "owner" of the function in which the **this** keyword is used.==
- **this** is not static. It depends on how the function is called, and its value is only assigned when the function is actually called.

- simple functions -> this = undefined.
- arrow functions -> this = <this of surrounding function (lexical this, which means that it uses the **this** keyword of its parent)>
- Event listener -> this = <DOM element that the handler is attached to()> 

![[Screen Shot 2022-12-23 at 9.35.29.png]]




## PRIMITIVE VS. REFERENCE VALUES

![[Screen Shot 2022-12-27 at 9.21.34.png]]

![[Screen Shot 2022-12-27 at 9.33.04.png]]