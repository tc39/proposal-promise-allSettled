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

JavaScript programmers can currently accomplish the same thing through this syntax:
```js
let arr = [ fetch('index.html'), fetch('http://does-not-exist') ]
Promise.allSettled(promises).then((results) => {
		return results.filter(x => x.status === "fulfilled");
	});
});
```

## Userland Implementations
* https://www.npmjs.com/package/q
* https://www.npmjs.com/package/rsvp
* http://bluebirdjs.com/docs/api/reflect.html
* https://www.npmjs.com/package/promise-settle 
* https://github.com/cujojs/when/blob/master/docs/api.md#whensettle
* https://www.npmjs.com/package/es2015-promise.allsettled

### Further Reading
* https://www.bennadel.com/blog/3289-implementing-q-s-allsettled-promise-method-in-bluebird.htm
* http://exploringjs.com/es6/ch_promises.html
* https://github.com/kriskowal/q/issues/257 [naming]
