---
layout: post
title: A tutorial on Javascript Promise
date: 2017-06-06 17:10:00 +0900
categories:
  - Javascript
tags:
  - promise
  - tutorial
references:
  - https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
  - https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises
comments: true
---

## tl;dr

```javascript
// prom1(: promise) starts to work immediately after defined
const prom1 = new Promise((resolve, reject) => {
  console.log("promise 1 started to work");
  const rand = Math.random();
  let timeoutFunc = setTimeout(() => {
    // mimicking async
    rand > 0.5? resolve("promise 1 resolved"): reject("promise 1 rejected");
  }, 1000);
});

prom1.then(console.log).catch(console.log);
/* same as
prom1.then(console.log, console.log); */

// func1(: function) starts to work when called
const func1 = () => {
  console.log("function 1 started to work");
  const rand = Math.random();
  return rand > 0.5? Promise.resolve("function 1 resolved"): Promise.reject("function 1 rejected");
  /* same as
  return new Promise((resolve, reject) => {
    rand > 0.5? resolve("function 1 resolved"): reject("function 1 rejected");
  }); */
};
func1().then(console.log, console.log);

/* thenable means returning a promise
of course, a naive function is not thenable */
const func2 = () => {
 console.log("function 2 started to work");
 return rand = Math.random();
};
// func2().then(console.log); // TypeError: returned value of func1 is not thenable

/* revisit prom1
instance methods (then and catch) return a resolved promise in default,
therefore manual coding for returning a promise is not needed.
*/
prom1.then().then(console.log).catch(console.log)
prom1.then()/*.then(). ... .then()*/.then(console.log, console.log) // same results

// define another promise
const prom2 = new Promise((resolve, reject) => {
  console.log("promise 2 started to work");
  const rand = Math.random();
  // doing much longer work
  let timeoutFunc = setTimeout(() => {
    rand > 0.5? resolve("promise 2 resolved"): reject("promise 2 rejected");
  }, 10000);
});

/* Promise.all: iterable => a promise which
resolves when all promises resolve and
rejects whenever any promise rejects */
Promise.all([prom1, prom2]).then(console.log, console.log)
/* if prom1 rejects => p.all rejects in 1 second
else if prom2 rejects => p.all rejects in 10 seconds
else both resolve => p.all resolve in 10 seconds
*/
```

## basic syntax
A `Promise` object needs a function `(resolve, reject) => {}` as an argument to deal with an async job and then deliver its returning values.

```javascript
// prom1(: promise) starts to work immediately after defined
const prom1 = new Promise((resolve, reject) => {
  console.log("promise 1 started to work");
  const rand = Math.random();
  let timeoutFunc = setTimeout(() => {
    // mimicking async
    rand > 0.5? resolve("promise 1 resolved"): reject("promise 1 rejected");
  }, 1000);
});
const callbackLog = val => console.log(val);
prom1.then(callbackLog).catch(callbackLog);
/* same as
prom1.then(console.log).catch(console.log); // or
prom1.then(console.log, console.log); */
console.log("hello, prom1 is working ...");
```

Depending on the result of `prom1`'s work, which takes a second, `prom1` would invoke `resolve` or `reject`. After it is decided, `resolve` or `reject`, `prom1`'s methods `then` and `catch` take a function, any function you want to work with. In my case of output, `prom1` resolves the job.

```
promise 1 started to work
hello, prom1 is working ...
promise 1 resolved
```

Also note that the order of `console.log` output, it is async!

## then is thenable

"thenable" means returning a promise. Of course, a naive function is not thenable.

```javascript
const func1 = () => {
 console.log("function 1 started to work");
 return rand = Math.random();
};
func1().then(console.log); // TypeError: there is no then method
```

Below is the typical example of thenable function.

```javascript
// func2(: function) starts to work when called
const func2 = () => {
  console.log("function 2 started to work");
  const rand = Math.random();
  return rand > 0.5? Promise.resolve("function 2 resolved"): Promise.reject("function 2 rejected");
  /* same as
  return new Promise((resolve, reject) => {
    rand > 0.5? resolve("function 2 resolved"): reject("function 2 rejected");
  }); */
};
func2().then(console.log, console.log);
console.log("hello, func2 is working ...");
```

Also after the method `then` has its job done, it returns another promise resolved with its return value (provided it exists). Therefore additional chaining is available.

```javascript
/* revisit prom1
instance methods (then and catch) return a resolved promise in default,
therefore manual coding for returning a promise is not needed.
*/
prom1.then().then(console.log).catch(console.log)
prom1.then()/*.then(). ... .then()*/.then(console.log, console.log) // same results
```

## dealing with multiple promises

Class methods `Promise.all` and `Promise.race` is here for us. In this post, only `Promise.all` method is explained via the following code.

```javascript
// define another promise along with prom1
const prom2 = new Promise((resolve, reject) => {
  console.log("promise 2 started to work");
  const rand = Math.random();
  // doing much longer work
  let timeoutFunc = setTimeout(() => {
    rand > 0.5? resolve("promise 2 resolved"): reject("promise 2 rejected");
  }, 10000);
});
/* Promise.all: iterable => a promise which
resolves when all promises resolve and
rejects whenever any promise rejects */
Promise.all([prom1, prom2]).then(console.log, console.log)
```

`Promise.all` takes many promises and invoke next step if all promises resolve, or any promise rejects. There are possible outcomes for example,

* if only `prom1` rejects => `Promise.all` rejects in a second,
* else if only `prom2` rejects => `Promise.all` rejects in 10 seconds,
* else if both reject => `Promise.all` rejects in a second right after `prom1` rejects,
* else if both resolve => `Promise.all` resolve in 10 seconds.

You may check another method, namely `Promise.race`. Also MDN documents with its various examples would be helpful.
