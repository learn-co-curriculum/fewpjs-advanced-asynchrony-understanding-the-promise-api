# Understanding the Promise API

## Learning Goals

- Understand the definition of a Promise in JavaScript
- Compose a Promise

## Introduction

In the previous lessons, we looked at how we can use `fetch()` to send get and
post requests, allowing us to retrieve resources across a network
asynchronously. We can use `fetch()` to retrieve remote data, and then, **once
the data is received**, use it in our application. Meanwhile, any other code
after the `fetch()` will continue to execute synchronously.

We are able to do this because `fetch()` returns a `Promise`. In this lesson,
we're going to take a closer look at what a `Promise` is and how we use it.

## Define Promise

Using a `Promise`, we can wrap any code that takes time to execute. This allows
us to write code that executes synchronously, even some parts of the code aren't
finished. A `Promise` is almost exactly what it sounds like, a special object
that _promises_ to do something and resolve correctly, but just not right now.
It helps us handle situations where we need to wait for something to occur and
then provide a resolution.

A `Promise` represents the eventual result of an asynchronous operation. It is a
placeholder in our code where a result will materialize. Once a `Promise`
resolves, anything in our code that relies on the _results_ of the `Promise`
can begin executing.

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

## Using `then()` with `resolve()`

We can attach code to execute upon the resolution of a `Promise` using the
`then()` function on an _unresolved_ `Promise`. Here's an example:

```js
const myFirstPromise = new Promise(resolve => {
	// We call resolve(...) when what we we're doing asynchronously was successful
	// In this example, we use setTimeout(...) to simulate asynchronous code.
	setTimeout(() => {
		resolve('Success!'); // Yay! Everything went well!
	}, 1000);
});

myFirstPromise.then(returnValueOfPromise => {
	// returnValueOfPromise is whatever we passed in the resolve(...) function above.
	console.log('Yay! ' + returnValueOfPromise);
	// LOG: Yay! Success!
});
```

When using `then()`, we pass in a callback function that we define; in this
case, a function that calls `console.log()`. Once our `Promise` resolves,
`then()` executes our callback, passing in whatever value or object was passed to `resolve()`. In this case, 'Success!'.

## Handling Rejection with Promises

The `resolve()` function is actually just one of _two_ functions available to us
in the callback we pass to a `Promise`. The second function is `reject()`, and
is used to handle _unsuccessful_ results.

```js
const mySecondPromise = new Promise((resolve, reject) => {
	// We call reject(...) when what we we're doing asynchronously was NOT successful

	setTimeout(function() {
		reject('Failure!'); // Yay! Everything went well!
	}, 1000);
});
```

Just like `resolve()`, we use `reject()` in place of a `return` statement.

## Using `catch()` with `reject()`

If a `Promise` resolves successfully, whatever we pass into `resolve()` is
available to us by using `then()`. With `reject()`, we use a different function:
`catch()`:

```js
mySecondPromise.catch(returnValueOfUnsuccessfulPromise => {
	// returnValueOfUnsuccessfulPromise is whatever was passed into the reject(...) function
	console.log('Oh no! ' + returnValueOfUnsuccessfulPromise);
});
```

This is often an error or message providing some information about what went
wrong. We can use both `then()` and `catch()` together for the same `Promise`,
and often see this when using

```js
const myThirdPromise = new Promise((resolve, reject) => {
	setTimeout(() => {
		let randomNumber = Math.random();
		if (randomNumber > 0.5) {
			resolve('Success!'); // Yay! Everything went well!
		} else {
			reject('Failure!'); // No! Everything is terrible!
		}
	}, 1000);
});

myThirdPromise
	.then(returnValueOfSuccessfulPromise => {
		console.log('Yay! ' + returnValueOfSuccessfulPromise);
	})
	.catch(returnValueOfUnsuccessfulPromise => {
		console.log('No! ' + returnValueOfUnsuccessfulPromise);
	});
```

If you run the above code in a browser console, it will randomly fail half of
the time.

> When using `fetch()`, `catch()` can be very helpful for handling situations
> where your code attempted to retrieve remote data, but failed. For instance, web
> page resources didn't load, or a form failed to send. We can use `catch()` to
> gracefully handle unexpected results, or even provide an alternative user
> experience in the event that a user is offline.

## A Promise Can Be in One of Three States

A `Promise` can either be _pending_, _resolved_, or _rejected_. `Promise` start
in a _pending_ state, and remain pending until resolved or rejected. Notice that
if we were to log the value of `myThirdPromise` _immediately_ after we've
declared it, it will show that it is still pending:

```js
const myThirdPromise = new Promise((resolve, reject) => {
	setTimeout(() => {
		let randomNumber = Math.random();
		if (randomNumber > 0.5) {
			resolve('Success!'); // Yay! Everything went well!
		} else {
			reject('Failure!'); // No! Everything is terrible!
		}
	}, 1000);
});
console.log(myThirdPromise);
// LOG: Promise {<pending>}
```

Any code that relies on the resolution of the `Promise` must be contained within
its corresponding `then()` function.

## How `fetch()` Uses a `Promise`

Getting remote data takes time. By using a `Promise`, `fetch()` can be called,
our code can go do something else, and then come back to handle our newly
retrieved data.

Inside `fetch()` is a `resolve()` function, which means that when remote data is
successfully received, we will be able to access it by chaining a `then()` to
our `fetch()`:

```js
fetch(`http://api.open-notify.org/astros.json`).then(response =>
	response.json()
);
```

The data we receive is typically a [`Response`][response] object. In the snippet
above, we see the `Response` object's `json()` method, which takes the body of
the `Response` and converts it to [JSON][json] format. Once we have JSON, we can
do whatever we want with the data.

You'll often see `fetch()` with _two_ `then()` functions chained one after the
other like this:

```js
fetch(`http://api.open-notify.org/astros.json`)
	.then(response => response.json())
	.then(json => {
		console.log(json);
	});
```

Here, `fetch()` returns a `Promise`, which, once resolved, returns `response`.
We can access `response` within the callback inside the first `then()`,
`response => response.json()`.

> Remember: arrow functions implicitly return when written without brackets, so this function returns whatever the return value of `response.json()` is.

What about the second `then()`? Well, we know that the `then()` function is used
to handle the successful resolution of a `Promise`, right?

As it turns out, `response.json()` is an asynchronous operation! It _also_
returns a `Promise`. The resolution of this second `Promise` is [JSON][json],
which we can then use as we please.

## Conclusion

`Promise`s provide an organized way to handle any operation that takes time to
complete. Getting remote data can be a slow process, so by having `fetch()`
return a `Promise`, we can execute a request to get data, continue on with any
other processes we need to do, then handle the retrieved data, rather than
waiting.

We saw in the example of `response.json()`, that `Promises` are not restricted
to remote requests, however. Sometimes, a process, in this case, converting
String response data to JSON, can take a variable amount of time, depending on
the amount of data. In this case, a `Promise` is also helpful, as it lets the
rest of our code execute until the data is fully converted.

With `resolve()` and `reject()`, and their counterparts, `then()` and `catch()`,
`Promises` allow us to gracefully handle the success or failure of asynchronous
operations in JavaScript.

## Resources

- [Promises][mdn]
- [resolve][resolve]
- [then][then]
- [reject][reject]
- [catch][catch]
- [Using Promises][using]

[using]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises
[mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[then]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then
[catch]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch
[json]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON
[resolve]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve
[reject]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject
