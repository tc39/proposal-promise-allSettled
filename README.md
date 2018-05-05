
# Promise.allSettled
ECMAScript Proposal, specs, and reference implementation for Promise.allSettled

## Rationale
A common use case that I, and many others have, is to want to settle a list of promises with an array. Due to the short circuit nature of Promise.all() any rejected promise will reject the entire operation and return a rejection.
The key feature of the .allSettled() method is that it allows us to collect Promise rejections and resolutions settling all promises.

Promise.allSettled() returns a promise that is fulfilled with an array of promise state snapshots, but only after all the original promises have settled, i.e. become either fulfilled or rejected.

This method is used in its static form on arrays of promises, in order to execute a number of operations concurrently and be notified when they all finish, regardless of success or failure.

## Example
Currently you would need to iterate through the array of promises and return a new value with the status known (either through the resolved branch or the rejected branch.

```js
function reflect(promise){
    return promise.then(function(v){ 
	    return {value:v, status: "resolved" }
	},
	function(e){ 
		return {error:e, status: "rejected" }
	});
}

let arr = [ fetch('index.html'), fetch('http://does-not-exist') ]
Promise.all(arr.map(reflect)).then(function(results){
    return success = results.filter(x => x.status === "resolved");
});
```

JavaScript programmers can currently accomplish the same thing through this syntax:
```js
let arr = [ fetch('index.html'), fetch('http://does-not-exist') ]
Promise.allSettled(promises).then((results) => {
		return results.filter(x => x.status === "resolved");
	});
});
```

## Userland Implementations
* https://www.npmjs.com/package/q
* http://bluebirdjs.com/docs/api/reflect.html
* https://www.npmjs.com/package/promise-settle 
* https://github.com/cujojs/when/blob/master/docs/api.md#whensettle
* https://www.npmjs.com/package/es2015-promise.allsettled

### Further Reading
* https://www.bennadel.com/blog/3289-implementing-q-s-allsettled-promise-method-in-bluebird.htm
