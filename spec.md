## Promise.allSettled ( _iterable_ )  

When the `allSettled` method is called with argument _iterable_, the following steps are taken:
  1. Let _C_ be the **this** value.
  1. If <a href="https://tc39.github.io/ecma262/#sec-ecmascript-data-types-and-values">Type</a>(_C_) is not Object, throw a **TypeError** exception.
  1. Let _promiseCapability_ be ? <a href="http://www.ecma-international.org/ecma-262/6.0/index.html#sec-newpromisecapability">NewPromiseCapability</a>(_C_).
  1. Let _iteratorRecord_ be <a href="https://tc39.github.io/ecma262/#sec-getiterator">Call</a>(_GetIterator_, **iterable**, &laquo; &raquo;).
  1. Assert: _status_ is a <a href="https://tc39.github.io/ecma262/#sec-completion-record-specification-type">Completion Record</a>.
  1. If _status_ is an <a href="https://tc39.github.io/ecma262/#sec-completion-record-specification-type">abrupt completion</a>, then
    1. Perform ! <a href="http://www.ecma-international.org/ecma-262/6.0/index.html#sec-call">Call</a>(_promiseCapability_.[[Reject]], **undefined**, « _status_ »).
  1. Else
    1. Perform ! <a href="http://www.ecma-international.org/ecma-262/6.0/index.html#sec-call">Call</a>(_promiseCapability_.[[Resolve]], **undefined**, « _status_.[[Value]] »).
  1. Return _promiseCapability_.[[Promise]].
