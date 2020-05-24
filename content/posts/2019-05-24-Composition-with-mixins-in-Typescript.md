---
title: "Mixin class in Typescript - The Good? bad? ugly? "
date: 2020-05-24T16:03:52-04:00
tags: ["Javascript"]
---

### What is mixin? 

Typescript introduced mixin pattern via mixin classes with this [push](https://github.com/microsoft/TypeScript/pull/13743)

Wikipedia defines mixin as followed

> Mixin programming is a style of software development, in which units of functionality are created in a class and then mixed in with other classes.

Mixin is a composition pattern where a unit of functionality is injected in a class by a wrapper function or an object. In the typescript world, it usually contains a small class with a single behavior that wraps another class to extend the functionality or behavior.


A behavior supported by mixin is usually stand-alone with little or no outside dependency. Most importantly, the point of mixin is to create a type that can be mixed into any other type via inheritance without affecting the type of that class or class inheritance.  

### Use Cases

A mixed in the usually used when there is a single, small, and very generic feature to be used in a lot of different classes. There is also a specialized case where you want to apply a lot of non-essential yet orthogonal features to a class but for the sake of clarity and simplicity, you also want to keep them in separate classes.

### Mixin in Typescript

I've created [a simple app to demo mixin](https://stackblitz.com/edit/typescript-vcsnb8?file=index.ts)

There is a mixin which injects hover behavior to any class. Most probably, you wouldn't do this in a real app but the point here is to demonstrate how to inject functionalities to a class without affecting its type.

The basic building blocks are something like this. 

```typescript
type Constructor<T = {}> = new (...args: any[]) => T;

export function Hoverable<TBase extends Constructor >(Base: TBase) {

  return class extends Base implements IHover {
    ...
    constructor(...args: any[]){
      super(...args);
      ...
      this.element = document.querySelector(this.selector);
      this.element && this.element.addEventListener('mouseover', this.onHover)
    }

  };
}
```

Let's break it down step by step 

- The `type Constructor<T = {}>` provides a constructor signature that constructs generic type T whose constructor take `any` parameter as constructor arguments.

- Hoverable function takes a `Base` class and return a new class which extends `Base` class with hoverable functionality injected in it. 

- Finally, to inject a new class mixed in with hoverable behavior 

`const MyHoverableApp = Hoverable(MyApp);`

### Mixin can be Anti-pattern
The larger the application gets, having wild mixins can make the application complex really fast. 

Check out the whole react blog about how they're bad in 2016- https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html

Although, *__cough__* *__cough__* hooks 🤨🤨. I think that react hooks are mixins with just extra steps. 

### Conclusion
Mixin is a very simple and powerful programming pattern. Languages like Scala and C# have really embraced it. Having personal experience with `traits` in Scala, I think that patterns are really useful in so many ways. But in just about anything, it can be abused very fast and can make the codebase complex.  