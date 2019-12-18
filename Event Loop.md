# Event Loop 和 Vue.nextTick

## 引入

在浏览器中，通常伴随着很多事件的发生，比如用户点击、页面渲染、脚本执行、网络请求，等等。为了协调这些事件的处理，浏览器使用事件循环机制。

# Vue.nextTick()

Vue.nextTick()的回调什么时候会执行？适用于什么情况下？

Vue.nextTick()的原理是什么？


## 同步和异步

### 一. 正常情况下，浏览器是单线程的

JavaScript最大的特点是单线程，当Javascript要操作Dom的时候，就不能出现两个线程同时操作Dom的情况了。

### 二. 同步和异步
    
所以如果全部代码都是同步执行的，这会引发很严重的问题，比如，当获取网络请求的数据，只能等到返回了结果，才能继续执行后面的代码。

于是就有了异步事件的概念，注册一个回调函数，比如说发一个网络请求，我们告诉主程序等到接收到数据后
通知我，然后我们就可以去做其他的事情了。

然后在异步完成后，会通知到我们，但是此时可能程序正在做其他的事情，所以即使异步完成了也需要在一旁
等待，等到程序空闲下来才有时间去看哪些异步已经完成了，可以去执行。

#### 常见的异步方法

1. setTimeout、setInterval

2. 事件监听

采用事件驱动模式。

监听函数有：on，bind，listen，addEventListener，observe。

3. promise

4. async/await

5. 网络请求

等等。

#### 浏览器的线程

一个浏览器进程通常由以下五个线程组成：

1. GUI渲染线程。

GUI渲染线程负责渲染浏览器界面HTML元素,当界面需要重绘(Repaint)或由于某种操作引发回流(reflow)时,
该线程就会执行。在Javascript引擎运行脚本期间,GUI渲染线程都是处于挂起状态的,也就是说被”冻结”了。

2. JavaScript引擎线程。

Javascript引擎，也可以称为JS内核，主要负责处理Javascript脚本程序，例如V8引擎。Javascript引擎
线程负责解析Javascript脚本，运行代码。

3. 事件触发线程。

当一个事件被触发时该线程会把事件添加到待处理事件队列的队尾，等待JS引擎的处理。这些事件可以是当前执行
的代码块如定时任务、也可来自浏览器内核的其他线程如鼠标点击、AJAX异步请求等，但由于JS的单线程关系
所有这些事件都得排队等待JS引擎处理。

4. 定时器线程。

浏览器定时计数器并不是由JavaScript引擎计数的, 因为JavaScript引擎是单线程的,如果处于阻塞线程状态
就会影响记计时的准确, 因此通过单独线程来计时并触发定时是更为合理的方案。

5. 异步HTTP请求线程。

## 任务队列

### 1. 主线程

所有同步任务都在主线程上执行，形成一个执行栈

### 2. 任务队列

主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置
一个事件。

### 3. 执行栈执行完毕

一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，
于是结束等待状态，进入执行栈，开始执行。

### 4. 只要主线程空了，就会去读取"任务队列"，这就是JavaScript的运行机制。


## Event Loop

同步和异步任务分别进入不同的执行环境，同步的进入主线程，即主执行栈，异步的进入任务队列。主线程内的
任务执行完毕为空，会去任务队列读取对应的任务，推入主线程执行。 上述过程的不断重复就是我们说的 
Event Loop (事件循环)。

### 异步的任务执行为什么也分前后？

task分为两大类, 分别是 Macro Task （宏任务）和 Micro Task（微任务）, 并且每个宏任务结束后, 
都要执行所有的微任务。

每一次事件循环都包含一个microtask队列，在循环结束后会依次执行队列中的microtask并移除，然后再开始下一次事件循环。

我们知道，在执行microtask的过程中后加入microtask队列的微任务，也会在下一次事件循环之前被执行。也就是说，macrotask总要等到microtask都执行完后才能执行，microtask有着更高的优先级。

microtask的这一特性，简直是做队列控制的最佳选择啊！vue进行DOM更新内部也是调用nextTick来做异步队列控制。而当我们自己调用nextTick的时候，它就在更新DOM的那个microtask后追加了我们自己的回调函数，从而确保我们的代码在DOM更新后执行，同时也避免了setTimeout可能存在的多次执行问题。

### 问题

```
async function async1() {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}
async function async2() {
    console.log('async2');
}
console.log('script start');
setTimeout(function() {
    console.log('setTimeout');
}, 0)
async1();
new Promise(function(resolve) {
    console.log('promise1');
    resolve();
}).then(function() {
    console.log('promise2');
});
console.log('script end');
```

### vue的降级策略

上面我们讲到了，队列控制的最佳选择是micro task，而micro task的最佳选择是Promise.但如果当前环境不支持Promise，vue就不得不降级为macrotask来做队列控制了。

macrotask有哪些可选的方案呢？前面提到了setTimeout是一种，但它不是理想的方案。因为setTimeout执行的最小时间间隔是约4ms的样子，略微有点延迟。还有其他的方案吗？

不卖关子了，在vue2.5的源码中，macrotask降级的方案依次是：setImmediate、MessageChannel、setTimeout.

setImmediate是最理想的方案了，可惜的是只有IE和nodejs支持。

MessageChannel的onmessage回调也是microtask，但也是个新API，面临兼容性的尴尬...

所以最后的兜底方案就是setTimeout了，尽管它有执行延迟，可能造成多次渲染，算是没有办法的办法了。

## 总结

以上就是vue的nextTick方法的实现原理了，总结一下就是：

vue用异步队列的方式来控制DOM更新和nextTick回调先后执行
microtask因为其高优先级特性，能确保队列中的微任务在一次事件循环前被执行完毕
因为兼容性问题，vue不得不做了microtask向macrotask的降级方案

最后的最后，记住，JavaScript 是一门单线程语言，异步操作都是放到事件循环队列里面，等待主执行栈来执行的，并没有专门的异步执行线程。

## 浏览器渲染的时机

我们知道浏览器的线程中的两个JavaScript引擎线程和GUI渲染线程，我们也知道一个事件循环，会先执行宏任务再执行微任务，那么浏览器渲染是在哪个步骤？

实际上浏览器渲染界面的GUI渲染线程会阻塞JavaScript引擎线程，这两个线程不能同时存在，而一个时间循环结束后，就会进入GUI渲染，也就是微任务结束后。

### 问题

下面代码界面刷新了几次？

```
for(let i=0; i<100; i++){
    dom.style.left = i + 'px';
}
```

# 问题

如果是GUI渲染结束前，执行微任务，那Vue.nextTick如果能获取到dom？
