# Promise.allSettled (Stage 0 Strawman)
ECMAScript Proposal and reference implementation for Promise.allSettled

## Rationale
A common use case that I, and many others have, is to want to settle a list of promises with an array. Due to the short circuit nature of Promise.all() any rejected promise will cancel the entire operation and return a rejection.
The key feature of the .allSettled() method is that it allows us to settle all promises.

Promise.allSettled() returns a promise that is fulfilled with an array of promise state snapshots, but only after all the original promises have settled, i.e. become either fulfilled or rejected.

This method is used in its static form on arrays of promises, in order to execute a number of operations concurrently and be notified when they all finish, regardless of success or failure.

## Example
Currently you would need to iterate through the array of promises and return a new value with the status known (either through the resolved branch or the rejected branch.

```js
function reflect(promise){
    return promise.then(function(v){ 
	    return {value:v, status: "fulfilled" }
	},
	function(e){ 
		return {reason:e, status: "rejected" }
	});
}

let arr = [ fetch('index.html'), fetch('http://does-not-exist') ]
Promise.all(arr.map(reflect)).then(function(results){
    return success = results.filter(x => x.status === "fulfilled");
});
```

JavaScript programmers can currently accomplish the same thing through this API:
```js
let promises = [ fetch('index.html'), fetch('http://does-not-exist') ]
Promise.allSettled(promises).then((results) => {
		return results.filter(x => x.status === "fulfilled");
	});
});
```
Collecting errors example:
```js
let promises = [ fetch('index.html'), fetch('http://does-not-exist') ]

const errors = Promise.allSettled(promises)
  .filter(p => p.status === "rejected")
  .map(p => p.reason);
```

## Exploration of return types
Currently this proposal uses objects within an array, this is the most commonly used pattern in implementations today.
### [Object]
```js
[{status: <string>, reason: <string>, value: <any>},...]
```
### A new Maybe type
There's the opportunity to use a new type to combine rejections and resolutions. Such types have been implemented, like Maybe https://www.npmjs.com/package/maybe which is inspired by [Haskell's Data.Maybe](http://www.haskell.org/ghc/docs/7.4-latest/html/libraries/base-4.5.1.0/Data-Maybe.html). 
Rust also has [`Result`](https://doc.rust-lang.org/std/result/) enum which can represent both a success and containing a value, and [`Err(E)`](https://doc.rust-lang.org/std/result/enum.Result.html#variant.Err), representing error and containing an error value.
##### Pros

By using Maybe, you are declaring that your value may or may not exist when it is accessed. This encourages explicit handling of these cases, and ensures uniform treatment of such cases.

There could be some potential usage with pattern matching for explicit handling of errors.
```js
let unsure =  Maybe(Math.random()  <  0.5  ||  undefined);
const val = match (unsure) {
  true => `We have a value less than 0.5`,
  Maybe.Nothing => 'There is no value!',
}
```

##### Cons
The disadvantage is that a `Maybe` type could add complexity to the Promise API and to the language.


## Userland Implementations
* https://www.npmjs.com/package/q
* https://www.npmjs.com/package/rsvp
* http://bluebirdjs.com/docs/api/reflect.html
* https://www.npmjs.com/package/promise-settle 
* https://github.com/cujojs/when/blob/master/docs/api.md#whensettle
* https://www.npmjs.com/package/es2015-promise.allsettled
* https://www.npmjs.com/package/maybe

### Further Reading
* https://www.bennadel.com/blog/3289-implementing-q-s-allsettled-promise-method-in-bluebird.htm
* http://exploringjs.com/es6/ch_promises.html
* https://github.com/kriskowal/q/issues/257 [naming]

