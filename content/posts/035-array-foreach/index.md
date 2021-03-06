---
title: How to Use forEach() to Iterate an Array in JavaScript
description: "forEach() in JavaScript calls the provided function on each array item with 3 arguments: item, index, the array itself. Learn how to use forEach()."
published: "2019-07-03"
modified: "2019-10-30T09:00Z"
thumbnail: "./images/cover.jpg"
slug: foreach-iterate-array-javascript
tags: ["javascript", "array", "foreach"]
recommended: ["how-to-iterate-easily-over-object-properties-in-javascript", "power-up-the-array-creation-in-javascript"]
type: post
---

What's the usual thing you do with an array? Iterate through its items! This is where `forEach()` array method shines.  

This post describes how to use `forEach()` method to iterate the items of an array in JavaScript. 

The presented best practices (correct handling of `this`, how to iterate array-like objects, etc) will help you even better understand `forEach()`.  

## 1. Basic forEach example

> `forEach()` method of the array object iterates over the array's items, in ascending order, without mutating the array 

The first argument of `forEach()` is the callback function called for every item in the array. The second argument (optional) is the value of `this` set in the callback.

```javascript
array.forEach(callback [, thisArgument])
```

Let's see how `forEach()` works in practice.  

In the following example, `colors` array has 3 colors. Let's use `forEach()` to log to console every color:  

```javascript
const colors = ['blue', 'green', 'white'];

function iterate(item) {
  console.log(item);
}

colors.forEach(iterate);
// logs "blue"
// logs "green"
// logs "white"
```

`iterate` is the callback function. `colors.forEach(iterate)` executes `iterate` function for every item in `colors`, setting the iterated item as the first argument:

* `iterate('blue')`
* `iterate('green')`
* `iterate('white')`

## 2. Index of the iterated element

> `forEach()` executes the callback function with 3 arguments: the current iterated array *item*, the *index* of the iterated item and the *array* instance itself. 

```javascript
array.forEach(callback(item [, index [, array]]))
```

Let's access the index of each item in the colors array:

```javascript
const colors = ['blue', 'green', 'white'];

function iterate(item, index) {
  console.log(`${item} has index ${index}`);
}

colors.forEach(iterate);
// logs "blue has index 0"
// logs "green has index 1"
// logs "white has index 2"
```

`iterate()` function has access to the current iterated item and its index. The callback is executed 3 times:

* `iterate('blue', 0)`
* `iterate('green', 1)`
* `iterate('white', 2)`

## 3. Access the array inside the callback

You might want to access the array itself during the iteration. One option is to use the 3rd parameter inside the callback function.  

Let's log the message `The last iteration!` when JavaScript executes the last iteration on the array items.

```javascript
const colors = ['blue', 'green', 'white'];

function iterate(item, index, array) {
  console.log(item);
  if (index === array.length - 1) {
    console.log('The last iteration!');
  }
}

colors.forEach(iterate);
// logs "blue"
// logs "green"
// logs "white"
// logs "The last iteration!"
```

The 3rd parameter `array` inside the callback function is the array on which `forEach()` method was called on. `array` parameter and `colors` variable point to the same array object.  

## 4. *this* inside the callback

Let's run the following example in a browser, and pay attention to the value of `this`:
```javascript{4}
const letters = ['a', 'b', 'c'];

function iterate(letter) {
  console.log(this === window); // true
}

letters.forEach(iterate); // logs 3 times "true"
```

`this` inside `iterate()` equals to `window`, which is the global object in the browser environment. Follow [regular function invocation](/gentle-explanation-of-this-in-javascript/#2-function-invocation) to get more information.  

In some situations, you might need to set `this` to an object of interest. Then indicate this object as the second argument when calling `forEach()`:

```javascript
array.forEach(callback, thisArgument)
```

Let's implement a `Unique` class, which always holds an unique list of items:
```javascript{8,9,11}
class Unique {
  constructor(items) {
    this.items = items;
  }
  
  append(newItems) {
    newItems.forEach(function(newItem) {
      if (!this.items.includes(newItem)) {
        this.items.push(newItem);
      }
    }, this);    
  }
}

const uniqueColors = new Unique(['blue']);
console.log(uniqueColors.items); // => ['blue']

uniqueColors.append(['red', 'blue']);
console.log(uniqueColors.items); // => ['blue', 'red']
```

`newItems.forEach(function() {}, this)` is called with the second argument pointing to `this`, i.e. the instance of `Unique` class. 

Because of that, inside the callback of `forEach()`, `this` points also to an instance of `Unique`. Now it's safe to access `this.items`.

However, using an arrow function as the callback of `forEach()` would be better. The [arrow function preserves](/gentle-explanation-of-this-in-javascript/#7-arrow-function) the value of `this` from the lexical scope, so there would be no need to use the second argument on `forEach()`.  

## 5. forEach skips empty slots

`forEach()` skips the empty slots of the array (named [sparse array](/power-up-the-array-creation-in-javascript/#third-case-no-element-between-commas)).  

```javascript
const sparseArray = [1, , 3];
sparseArray.length; // => 3

sparseArray.forEach(function(item) {
  console.log(item);
}); // logs 1, 3
```

`sparseArray` contains `1`, an empty slot, and `3`. `forEach()` iterates over `1` and `3`, but skips the empty slot.  

## 6. Iterate array-like objects using forEach

`forEach()` can iterate over array-like objects. The downside is the need to make the `forEach()` call indirectly:

```javascript{12}
const arrayLikeColors = {
  "0": "blue",
  "1": "green",
  "2": "white",
  "length": 3
};

function iterate(item) {
  console.log(item);
}

Array.prototype.forEach.call(arrayLikeColors, iterate);
// logs "blue"
// logs "green"
// logs "white"
```
`arrayLikeColors` is an array-like object. In order to iterate over its items, you have to call indirectly `forEach()` method, taking it from `Array.prototype`.  

Alternatively, you can transform the array-like object into an array using `Array.from()`, then iterate. With a sligh performance impract, the code becomes shorter:

```javascript
//...

Array.from(arrayLikeColors).forEach(iterate);
// logs "blue"
// logs "green"
// logs "white"
```

## 7. When to use forEach()

`forEach()` is best used to iterate over all items of the array, without breaking, and have simultaneously some side-effects. 

Side-effects examples are a mutation of an outer scope variable, I/O operations (HTTP requests), DOM manipulations, and alike.  

For example, here's how you would clear the values of the input fields in a DOM document:

```javascript
const inputs = document.querySelectorAll('input[type="text"]');

inputs.forEach(function(input) {
  input.value = '';
});
```
The side effect in the callback function is clearing the value of the input field.  

Keep in mind that you cannot normally break the iteration of `forEach()` (other than a tricky way to throw an error to stop the iteration, which is a cheap hack). The method will always iterate over all the items.  

If your case requires an early break from the cycle, a better option is the classic [for](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for) or [for..of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of).  

When the array items iteration computes a result, without involving side-effects, a better alternative is to select an array method like:   

* [array.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
* [array.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
* [array.every()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)
* [array.some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

For example, let's determine whether all numbers of an array are even.  

The first solution involves `forEach()` method:

```javascript
let allEven = true;

const numbers = [22, 3, 4, 10];

numbers.forEach(function(number) {
  if (number % 2 === 1) {
    allEven = false;
    // Break here
  }
});

console.log(allEven); // => false
```

The code determines correctly if all numbers are even. The problem is the impossibility to break after finding the first odd number `3`.  

A better solution is to apply `array.every()` method:

```javascript
const numbers = [22, 3, 4, 10];

const allEven = numbers.every(function(number) {
  return number % 2 === 0;
});

console.log(allEven); // => false
```
`array.every()` not only makes the code shorter and more expressive. It is also optimal, because `.every()` method breaks iterating right after finding the first odd number.  

## 8. Conclusion

`forEach()` method is an efficient way to iterate over all array items. Its first argument is the callback function, which is invoked for every item in the array with 3 arguments: item, index and the array itself.  

You would use `forEach()` to iterate over all array items, without breaking, involving simultaneously some side-effects. Otherwise, consider an alternative [array method](https://javascript.info/array-methods) that solves better your task. 

*Do you know good uses cases of `forEach()`? Write them in a comment below.*