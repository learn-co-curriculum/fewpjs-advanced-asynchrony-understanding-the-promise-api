# Understanding the Promise API

## Learning Goals

- Understand the definition of a Promise in JavaScript
- Compose a Promise

## Introduction

In previous lessons, we looked at how we can use `fetch()` to send get and post
requests, allowing us to get resources across a network asynchronously. We can
use `fetch()` to retrieve remote data, and then, **once the data is received**,
use it in our application. Meanwhile, any other code after the `fetch()` will
continue to execute synchronously.

We are able to do this because `fetch()` returns a `Promise`. In this lesson,
we're going to take a closer look at what `Promise`s are and how we use them.

## Define Promise

In JavaScript, we can wrap code that takes time to execute in something called a
`Promise`. A `Promise` is almost exactly what it sounds like, a special object
that _promises_ to do something and resolve correctly, but just not right now.
It helps us handle situations where we need to wait for something to occur and
then provide a resolution.

A `Promise` represents the eventual result of an asynchronous operation. It is a
placeholder in our code where a result will materialize.

## How to Create a Promise

The basic syntax of a `Promise` is as follows:

```js
new Promise(resolve => {
	resolve('Return Value');
});
```

We instantiate an instance of `Promise`, passing the constructor a callback
function of what the `Promise` is promising to do. The argument that callback
takes, `resolve`, is a special function that is called when the `Promise` is
done.

Instead of just using `return` in a `Promise` to specify what the `Promise`
should return, we invoke the `resolve()` function, saying this is the end of my
`Promise`, considered it fulfilled. You don't always have to explicitly state a
return of the `resolve()` function (i.e. `return resolve()`) inside of the
`Promise` unless you want the new `Promise` function to complete upon calling
resolve.

We can attach code to execute upon the resolution of a promise using the then function on an unresolved `Promise`. Here's an example.

```js
const myFirstPromise = new Promise(resolve => {
	// We call resolve(...) when what we were doing asynchronously was successful
	// In this example, we use setTimeout(...) to simulate asynchronous code.
	setTimeout(function() {
		resolve('Success!'); // Yay! Everything went well!
	}, 1000);
});

myFirstPromise.then(returnValueOfPromise => {
	// returnValueOfPromise is whatever we passed in the resolve(...) function above.
	console.log('Yay! ' + returnValueOfPromise);
});
```

## Conclusion

## Resources

- [Promises][mdn]

[mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
