---
title: Javascript Pattern Series - Singleton Pattern
date: '2023-02-13'
tags: ['design-patterns']
draft: false
summary: Singleton Pattern in Javascript
layout: PostLayout
---

Welcome to the first installment of my Javascript Design Patterns series! Today we'll be going over the Singleton Pattern in Javascript, it's uses, and the pros and cons.

# What is the Singleton Pattern?

A singleton or a singleton object, is a method of limiting the number of instances for an object down to one. This instance is called a singleton and can be shared throughout our application. Singletons are useful for managing global state in an application.

A singleton class should only be able to be instantiated once, and the class should not be open to any modifications.

# Example Singleton Stack Class

Here's an example of a simple implementation of a stack, which also adhere's to the singleton pattern.

```javascript
let instance = null
let stack = []

class Stack {
  constructor() {
    if (instance) {
      throw new Error('You cannot create more than one instance!')
    }
    instance = this
  }

  addToStack(val) {
    stack.push(val)
    return stack
  }

  removeFromStack() {
    return stack.pop()
  }

  peek() {
    return stack.at(-1)
  }
}

const singletonStack = Object.freeze(new Stack())

export default singletonStack
```

Here you'll notice in the constructor, we keep a global variable called `instance`. This variable gets set on the first instantiation of `Stack`, and is then leveraged as a check to see if an instance already exists. If it does exist, an error is thrown.

The singleton stack class also gets frozen in order to prevent any consuming code from adding or modifying the Singleton.

Now, when you import the `singletonStack` in another file, any consumer code will have a single source of truth pertaining to what is in the stack, regardless of where the object is called.

# Pros and Cons

## Pros

- One instance can potentially save alot of memory space.
- It allows you to keep global state in your application

## Cons

- Singletons are susceptible to hidden dependencies. It may not be obvious that a module is importing one and thus making the code base harder to understand and maintain.
- Testing is tricky. Since we can only create one instance, all tests must rely on one global state, and must be reset every time. Otherwise, on small change can make the entire test suite fail.
- Singletons can pollute the global scope. Since it is mutable, any code that relies on it can encounter unexpected behavior.

Singletons are typically an anti-pattern in Javascript. They can create complex, hard to maintain, and difficult to test code.
