---
title: "Promises and Microtask queue"
date: 2020-06-06T16:42:13-04:00
---

### Promises execution context 

Before we dive deep into promises execution context, try to figure out the output of the following piece of code.

```java
console.log("Hello there");

setTimeout(function () {
  console.log('Hello - setTimeout');
}, 0);

Promise.resolve()
  .then(function () {
    console.log('Hello - Promise 1 resolved');
  })
  .then(function () {
    console.log('Hello - Promise 2 resolved');
  });

console.log("Good Bye");

```

The answer is 

```console
Hello there
Good Bye
Promise 1 resolved
Promise 2 resolved
Hello - setTimeout
```
Ever wondered how the javascript execution context decided to come up with this execution order? shouldn't  `Hello - setTimeout` come before promises as it was pushed before. 

The answer is the microtask queue. Yes!! there is one more type of queue which takes precedence over the event loop tasks queue. 

### Task in javascript

In javascript, every piece of code that is scheduled to run by any mechanism is a task. A task is added since the javascript starts executing like from `<script>` tag. An event like a click, mouse hover, etc is added as a task. Timers and Intervals are also added to the task queue. 

A task queue will start executing each item until the queue is empty before pulling in another item from the event loop in the execution context. 

### Microtask queue

In a hindsight, a task and a microtask queue are very similar. The difference is that the event loop handles the microtask differently. 

* When event loop finishes executing tasks, it first checks the microtask queue and makes sure that there are no pending tasks. If there are any tasks in the microtask queue, it gets scheduled first before timer or any other tasks.

* If the execution of microtask results in another microtask then it gets scheduled first. 

So the priority of execution is 

* Current tasks in the stack execution context.
* Microtasks, even if they're added after the execution of the previous microtask.
* Pending task in the event loop. 

so let's rewrite the above code differently to understand it better. The lower the number the higher the priority is.


```java
console.log("Priority-1");

setTimeout(function () {
  console.log("Priority-5");
}, 0);

Promise.resolve()
  .then(function () {
    console.log("Priority-3");
  })
  .then(function () {
    console.log("Priority-4");
  });

console.log("Priority-2");
```

### Summary

Promises are part of Microtask, which is a special queue for promises and mutation observers which is executed before scheduling new callbacks from the event loop. Microtask is executed at the end of the current round of execution whereas tasks in the event loop have to wait before the next round starts. 

There is a lot more to cover about microtask beyond promises. 
[Jake Archibald has a great, in-depth article about tasks, microtasks, and queues](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/). A must-read. 
