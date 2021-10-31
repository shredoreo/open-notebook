# javascript 

## 单线程

每一个操作在执行的时候，其他任何事情都没有发生 — 网页的渲染暂停. 因为前篇文章提到过 [JavaScript is single threaded](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Concepts#JavaScript_is_single_threaded). 任何时候只能做一件事情, 只有一个主线程，其他的事情都阻塞了，直到前面的操作完成。 

## 异步js

 在JavaScript代码中，你经常会遇到两种异步编程风格：老派callbacks，新派promise。 



### Promise

```javascript
fetch('products.json').then(function(response) {
  return response.json();
}).then(function(json) {
  products = json;
  initialize();
}).catch(function(err) {
  console.log('Fetch problem: ' + err.message);
});
```

这里`fetch``()` 只需要一个参数— 资源的网络 URL — 返回一个 [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise). promise 是表示异步操作完成或失败的对象。可以说，它代表了一种中间状态。 本质上，这是浏览器说“我保证尽快给您答复”的方式，因此得名“promise”。

这个概念需要练习来适应;它感觉有点像运行中的[薛定谔猫](https://zh.wikipedia.org/wiki/薛定谔猫)。这两种可能的结果都还没有发生，因此fetch操作目前正在等待浏览器试图在将来某个时候完成该操作的结果。然后我们有三个代码块链接到fetch()的末尾：

- 两个 `then()` 块。两者都包含一个回调函数，如果前一个操作成功，该函数将运行，并且每个回调都接收前一个成功操作的结果作为输入，因此您可以继续对它执行其他操作。每个 `.then()`块返回另一个promise，这意味着可以将多个`.then()`块链接到另一个块上，这样就可以依次执行多个异步操作。
- 如果其中任何一个`then()`块失败，则在末尾运行`catch()`块——与同步`try...catch`类似，`catch()`提供了一个错误对象，可用来报告发生的错误类型。但是请注意，同步`try...catch`不能与promise一起工作，尽管它可以与[async/await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await)一起工作，稍后您将了解到这一点。

### 事件队列

 像promise这样的异步操作被放入事件队列中，事件队列在主线程完成处理后运行，这样它们就不会阻止后续JavaScript代码的运行。排队操作将尽快完成，然后将结果返回到JavaScript环境。 

###  Promises 对比 callbacks 

promises与旧式callbacks有一些相似之处。它们本质上是一个返回的对象，您可以将回调函数附加到该对象上，而不必将回调作为参数传递给另一个函数。

然而，`Promise`是专门为异步操作而设计的，与旧式回调相比具有许多优点:

- 您可以使用多个then()操作将多个异步操作链接在一起，并将其中一个操作的结果作为输入传递给下一个操作。这对于回调要难得多，回调常常以混乱的“末日金字塔”告终。 (也称为[回调地狱](http://callbackhell.com/))。
- `Promise`总是严格按照它们放置在事件队列中的顺序调用。
- 错误处理要好得多——所有的错误都由块末尾的一个.catch()块处理，而不是在“金字塔”的每一层单独处理。



### 小结

 在最基本的形式中，**JavaScript是一种同步的、阻塞的、单线程的语言**，在这种语言中，一次只能执行一个操作。但web浏览器定义了函数和API，允许我们当某些事件发生时不是按照同步方式，而是**异步地调用函数**(比如，时间的推移，用户通过鼠标的交互，或者获取网络数据)。这意味着您的代码可以同时做几件事情，而不需要停止或阻塞主线程。 





### setTimeOut

### 传递参数给setTimeout() 



我们希望传递给`setTimeout()`中运行的函数的任何参数，都**必须**作为列表末尾的附加参数传递给它。例如，我们可以重构之前的函数，这样无论传递给它的人的名字是什么，它都会向它打招呼：