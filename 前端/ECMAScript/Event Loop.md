## 浏览器内核

* 浏览器是多进程的，每一个 tab 页面就是一个独立的进程，浏览器内核（浏览器渲染进程）属于浏览器进程的一种。
* 浏览器内核有多种线程在工作。
    * GUI 渲染线程： 
        * 用于渲染页面的线程。
        * 回流和重绘都会调用这个线程。
        * 和 JS 引擎线程是互斥的，当 JS 引擎线程在工作的时候，GUI 渲染线程会被挂起，GUI 更新被放入在 js 任务队列中，等待 js 引擎线程空闲的时候继续执行。

    * JS 引擎线程
        * 单线程工作，负责解析运行 javascrupt 脚本。
        * GUI 渲染线程互斥，js 运行耗时过长就会导致页面阻塞

    * 事件触发线程
        * 当事件符合触发条件被触发时，该线程会把对应的任务添加到任务队列的队尾，等待 js 引擎处理。
    * 定时器触发线程
        * 浏览器定时计数器并不是由 js 引擎计数的，阻塞会导致计时不准确
        * 开启定时器触发线程来计时并触发计时，计时完成后会被添加到任务队列中，等待 js 引擎处理。
    * 异步 http 请求线程
        * http 请求的时候会开启一条请求线程
        * 请求完成有结果了之后，将请求的回调函数添加到任务队列中，等待 js 引擎处理。

## 任务队列

* JS 是单线程的，同一时间只能执行一项任务，只有当前的任务执行完成才会往后执行下一个任务，所有的任务都要排队等待主线程执行。

* 函数分为同步函数和异步函数。
    * 函数在被调用执行后，调用者可以预期的知道执行的结果，这个函数就是同步的。
    * 函数在被调用执行后，调用者不能预期的知道执行的结果，并且需要通过某些手段得到，那么这个函数就是异步的。

* JS 事件任务分为同步任务和异步任务。
    * 同步任务会按照顺序排队等待主线程执行，形成一个执行栈。
    * 异步任务会被添加进入任务队列(消息队列)中，等待着当执行栈中的任务全部执行完成后，主线程有空闲了，才会从任务队列中拉取事件放到主线程中执行。

* 事件需要指定回调函数，才会被放入任务队列中，事件包括 IO 设备事件，也包括用户产生的事件行为(鼠标点击，键盘敲击等)。

* 任务队列是先进先出的数据结构，排在前面的事件，优先被主线程拉取。

## Event Loop

* 主线程会在空闲的时候不断的从任务队列中拉取事件执行，每次事件执行完成，主线程被清空的时候，又会重复这一操作，形成一个循环操作。

## 定时器

* 定时器会开启一条定时器触发线程来触发计时。定时器会在等待了指定的时间后将事件放入到任务队列中等待主线程拉取执行，注意是要等待到了指定的时间才被放入任务队列中而不是一开始就放入进去，比如 setTimeout 延时了 1000ms，那么当代码执行到了 setTimeout 时必须是等待了 1000ms 之后再被放入到任务队列中。

* 定时器指定的时间执行其实并不准确，因为定时器只是在到了指定的时间时将事件放入到任务队列中，必须要等到同步的任务和现有的任务队列中的事件全部执行完成之后，才会去拉取定时器的事件到主线程执行，中间可能会存在耗时比较久的任务，那么就不可能保证在指定的时间执行。

## 参考

[http://www.ruanyifeng.com/blog/2014/10/event-loop.html](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

[https://juejin.im/post/59c25c936fb9a00a3f24e114](https://juejin.im/post/59c25c936fb9a00a3f24e114)

[https://zhuanlan.zhihu.com/p/26229293](https://zhuanlan.zhihu.com/p/26229293)

[https://segmentfault.com/a/1190000012925872](https://segmentfault.com/a/1190000012925872)