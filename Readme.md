BFTaskPromise
=============

## About

An Objective-C category for BFTask class in [Bolts-iOS](https://github.com/BoltsFramework/Bolts-iOS).

With this category, you can:

* chain tasks with dot-notation as JavaScript Promise-like syntax. (no more counting brackets!)
* write a catch block which will be called in error case only.
* write a finally block which won't change the result value.

### Example

```objc
[self countUsersAsync].then(^id(BFTask *task) {
    NSNumber *count = task.result;
    if ([count intValue] <= 0) {
        return [BFTask taskWithError:[NSError errorWithDomain:@"MyDomain"
                                                         code:-1
                                                     userInfo:nil]];
    } else {
        return [self makeTotalUserStringAsync:count];
    }
}).then(^id(BFTask *task) {
    // this block is skipped when task is failed.
    [self showMessage:task.result];
    return nil;
}).catch(^id(BFTask *task) {
    // this block is called in error case.
    [self showErrorMessage:task.error];
    return nil;
}).finally(^{
    [self updateList];
});
```

### Executors

If you want to specify the executor, use `thenOn`, `catchOn` and `finallyOn`.
You can also use `thenOnMain`, `catchOnMain` and `finallyOnMain`, they use `mainThreadExecutor`. 

```objc
[User findAllAsync].catchOn([BFExecutor mainThreadExecutor], ^id(BFTask *task) {
    [self showAlert:task.error];
});

// same as above
[User findAllAsync].catchOnMain(^id(BFTask *task) {
    [self showAlert:task.error];
});
```

### Not a Promise!

To be exact, Bolts's task is not a Promise. So this is not a Promise too.
The purpose of this project is making the task-chain much readable.


### Hate Dot-Notation?

If you don't think dot-notation is preferable but do want `catch` and/or `finally`, you can still use `catchWithExecutor:withBlock:` and `finallyWithExecutor:withBlock:`. They are normal methods.

To tell the truth, I hate dot-notation for methods basically. I want to use it for only properties. I prefer to use bracket-notation for usual methods.
But the task-chaining is a special case for me. It's unusual. I feel the task-chaining is not a nested method call, but a flow control.


## Install

Add `BFTask+PromiseLike.h` and `BFTask+PromiseLike.m` in `Classes` folder to your project.


## License

The MIT License.
