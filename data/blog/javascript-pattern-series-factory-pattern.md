---
title: Javascript Pattern Series - Factory Pattern
date: '2023-02-16'
tags: ['design-patterns']
draft: false
summary: Factory Pattern in Javascript
layout: PostLayout
---

Next up on our Javascript Design Pattern series is the Factory pattern!

# What is the Factory Pattern?

The factory pattern is the method by which we can manufacture new objects with shared properies using **factory functions**.
A factory function returns new pure objects without the use of the `new` keyword.

This is particularly useful when you need to create multiple objects that have a lot in common but differ slightly. Factories allow you to
create multiple objects easily and keeps your code DRY (Don't Repeat Yourself).

# What are factories useful for?

Let say you run a daycare and are writing a program to catalog all the dogs that you have. You can create dogs manually one by one like so

```javascript
const dog1 = {
  name: 'Walter',
  breed: 'maltese',
  bark() {
    console.log('woof')
  },
}
const dog2 = {
  name: 'Sam',
  breed: 'labrador retriever',
  bark() {
    console.log('woof')
  },
}
const dog3 = {
  name: 'Buster',
  breed: 'german shepherd',
  bark() {
    console.log('woof')
  },
}
```

Wait a minute, the shape of the dog objects all look similar, but the only differences are the names and the breeds. Instead of manually instantiating them,
we can instead create a factory called `dogFactory` and have it create these objects for us.

```javascript
const dogFactory = (name, breed) => {
  return {
    name,
    breed,
    bark() {
      console.log('woof')
    },
  }
}

const dog1 = dogFactory('Walter', 'maltese')
```

Now we have a concise and reusable function to create dog objects. It's also extendable. If in the future, we want to add more methods, or properties, we can just modify the factory.

# Factories vs Constructors

You may have noticed that these look alot like a constructor, especially if you're used to OOP.

Why should you use this instead of a constructor function?

Factories create a standalone object. We can use these objects without any affect on the others, aka a singleton. Class instances all refer to the same methods on the prototype chain defined on the class. Meaning,
if the class method changes, all objects will be affected. Where as, if you were to change a method on one factory object, the other factory object is not affected.

Factories are also less verbose than a class, and shine better when you need to create objects based on dynamic parameters.
