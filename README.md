

# Promise.allSettled
ECMAScript Proposal and reference implementation for Promise.allSettled  

**Author:** Jason Williams (BBC) 

**Champion:** Mathias Bynens (Google)

**Stage:** 0 

## Overview and motivation
A common use case that I and many others come across, is to want to settle all promises within an array. Due to the short circuit nature of Promise.all() any rejected promise will cancel the entire operation and return a rejection.
The key feature of the .allSettled() method is that it allows us to settle all promises.

Promise.allSettled() returns a promise that is fulfilled with an array of promise state snapshots, but only after all the original promises have settled, i.e. become either fulfilled or rejected.

This method is used in its static form on arrays of promises, in order to execute a number of operations concurrently and be notified when they all finish, regardless of success or failure.

## Why allSettled?
We say that a promise is _settled_ if it is not pending, i.e. if it is either fulfilled or rejected.

## Examples
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

The proposed API allows a developer to handle these cases without creating a reflect function and/or assigning intermediate results in temporary objects to map through:
```js
let promises = [ fetch('index.html'), fetch('http://does-not-exist') ]
Promise.allSettled(promises).then((results) => {
		return results.filter(x => x.status === "fulfilled");
	});
});
```
Collecting errors example:
Here we are only interested in the promises which failed, and thus collect the reasons. allSettled allows us to do this quite easily.
```js
let promises = [ fetch('index.html'), fetch('http://does-not-exist') ]

const errors = Promise.allSettled(promises).then(results => {
	  return results.filter(p => p.status === "rejected")
	  .map(p => p.reason);
	})
```

### Real World Scenarios
A common operation is knowing when all requests have completed regardless of the state of each request. This allows developers to build with progressive enhancement in mind. Not all API responses will be mandatory.
Here you can see that using Promise.all would not be suitable for the operation we wish to do
```js
const URLs = [..];
const requests = URLs.map(x => fetch(x)); // some of these will fail, some will succeed

// Short Circuits on first rejct, all other responses are lost
Promise.all(requests).then(() => {
  console.log('all requests have completed, now i can remove the loading indicator');
).catch(() => {
  console.log('but oops! at least one request has failed - but some of the requests still might not be finished!');
});

// We know all API calls have finished.
Promise.allSettled(requests).finally(() => {
  console.log('all requests are completed: either failed or succeeded, i don‘t care');
  removeLoadingIndicator();
});
```


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
* https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md
* http://exploringjs.com/es6/ch_promises.html
* https://github.com/kriskowal/q/issues/257 [naming]

