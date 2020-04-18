翻译自：[https://itnext.io/error-handling-with-async-await-in-js-26c3f20bc06a](https://itnext.io/error-handling-with-async-await-in-js-26c3f20bc06a)

本文来自 Code Review 时发现的一些问题，以及与其他开发者的讨论。内容适合 JS 初学者。

### 简单的 Try Catch

让我们以一个简单的 `try...catch` 例子开始。

```
function thisThrows() {
    throw new Error("Thrown from thisThrows()");
}

try {
    thisThrows();
} catch (e) {
    console.error(e);
} finally {
    console.log('We do cleanup here');
}

// Output:
// Error: Thrown from thisThrows()
//   ...stacktrace
// We do cleanup here
```


这段代码中，我们调用 `thisthrows()`，抛出一个标准异常，然后捕获它，打印错误信息以及继续执行 `finally` 代码块中的内容。没啥特殊的。

### 在 Promise 中抛出异常

我们修改 `thisthrows()`，不使用标准异常，而是将异常封装在一个 promise 中，然后抛出。简单来说，就是我要将 `thisThrows()` 方法加上 `async` 关键字。记住，带有 `async` 关键字的方法，`总是`返回一个 promise 对象。

- 当我们没有明确的返回，或者没有返回值，调用方会收到一个 resolving promise，相当于 `return Promise.Resolve()`。

- 当我们明确的返回值，调用方会收到一个 resolving promise 以及返回的值，相当于 `return Promise.Resolve("My return String")`

- 当一个异常被抛出，调用方将会收到一个 rejected promised，以及异常，相当于 `return Promise.Reject(error)`。

```
async function thisThrows() {
    throw new Error("Thrown from thisThrows()");
}

try {
    thisThrows();
} catch (e) {
    console.error(e);
} finally {
    console.log('We do cleanup here');
}

// output:
// We do cleanup here
// UnhandledPromiseRejectionWarning: Error: Thrown from thisThrows()
```

现在我们遇到了经典的问题，`thisThrow` 返回一个 rejecting promise，我们以普通的异常处理，try...catch 无法捕获。因为 `thisThrows()` 是一个 async，所以当调用时，代码不会被阻塞，所以 `finally` 代码块最先被执行，然后这个 promise 被执行，接着才是抛出异常。所以我们没能捕获这个 rejected promise。

两种方法可以解决这个问题：

- 我们调用`thisThrows()`在一个 async 函数里，使用 `await` 等待函数执行完成。
- 我们链式调用`thisThrows()`，在catch代码块中。

第一种方案看起来：

第二种方案看起来：

两种方案都工作的很好，但是 async/await 更加直观。（至少我这么认为）




