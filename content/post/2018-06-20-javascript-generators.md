---
title: "Javascript Generators - 1: Introduction"
date: 2018-06-20
draft: true
tags: ["JavaScript", "Fundamentals"]
---

## Why we need Generators ?
You may already know from {{< relref "post/2017-02-02-event-loop.md" >}} that that Javascript has an unique take on asynchronous processing and it's never been easy to write clean succinct async code. Initially there were call backs. Need something to happen outside of execution context of javascript eventloop ?  just use `setTimeout` and pass your callback function. Soon enough, people started abusing( and no mistake of their own as there wasn't any alternative ) callbacks and turned it into a call back hell. Generators in javascript, especially combined with promises can be very powerful tool mitigate complexity and issues with asynchronous programming.

## What is Javascript Generators ?
Simply put, Javascript Generators are functions which can be paused and re-entered while preserving their context. Calling the generator function will not execute it right away. Instead it will return an `Iterator` object which can be used to *get in and out of* function and *loop through* the result. All of this may come as very confusing so let's understand it by example. 

### A simple for loop
```javascript
for( var i=0;i<= 5; i++){
   console.log(i)
}
```

### Execute - Pause - Execute
Generator equivalent of the function above is this.
```javascript
function *generatorLoop() {
  for (var i = 0; i <= 5; i++) {
    yield i;
  }
}

const val = generatorLoop();
console.log(val.next());
console.log(val.next());
console.log(val.next());
console.log(val.next());
console.log(val.next());
console.log(val.next());
console.log(val.next());

```

#### Output
```console
{value: 0, done: false}
{value: 1, done: false}
{value: 2, done: false}
{value: 3, done: false}
{value: 4, done: false}
{value: 5, done: false}
{value: undefined, done: true}
```

### Breaking down the generator function

* A Generator function has `*` prefix which lets the javascript engine know that it's a type of generator function

* The most important part of Generator function syntax  is `yield`. `yield` is an expression which specifies the value to be returned to an iterator. This is where the function will be paused. 

* As you may have noticed, simply calling `const val = generatorLoop();` doesn't execute the function itself. It will return a special `Generator` object. 

* To actually loop through it, we have to call `next` function this generator object. It wil execute function till yield and waits for next execution command.

* Lastly, it will return `done: true` as the last return. As you may have noticed till now that we can easily re-write the above code using `while` loop and till `done === true`. This may be a good exercise if you want to try it out.

* Optionally we can also `return` from generator function but keep in mind that in the `for..of` loop like above, the last return value will be discarded.

### Passing the value to generators.

Let's look at more complex example to understand how the yield works. 

```Javascript
function *calcWith(val){
	const a = 6;
	const b = yield a + val;
	yield b * a;
    yield a + b ;
}

const it = calcWith(2);
console.log(it.next())
console.log(it.next(5))
console.log(it.next())
console.log(it.next())

```

#### Output

```Console
{value: 8, done: false}
{value: 30, done: false}
{value: 11, done: false}
{value: undefined, done: true}
```
* In the example above, we're passing `2` to the function which will be the value of `val`.
* On first `it.next()`, It returns generator function by adding the `val` to `a` and pause the execution there. Not that at this time, value of `b` hasn't been assigned. It is waiting for next command to continue execution. 
* In next iteration we are passing `5`, which becomes the value of `b`;
* Next two yields are called with out passing any argument as it doesn't rely on any value. 
* As you may notice in this example, the default value of `yield` is undefined but if we pass any value then it will return that value from `yield`.This is a very powerful feature which we'll discuss in our next post in this series.

### Summary
First of all, it may take some time to digest Generators as our brain is hard wired to visualize a function which executes fully. There was no such thing as pausable function up until Generators came along( Some languages like python supports this ). So far, we've covered very basics of Generators. In subsequent posts we'll go over 
* Generator calling on another generator and error handling. 
* Asynchronous programming.