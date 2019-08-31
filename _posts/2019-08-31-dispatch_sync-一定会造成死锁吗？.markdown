---
layout: post
title:  "dispatch_sync 一定会造成死锁吗？"
---

```
dispatch_queue_t queueA; // assume we have this
dispatch_sync(queueA, ^(){  // (a)
    dispatch_sync(queueA, ^(){ // (b)
        foo();
    });
});

```
上述代码，在执行第二个dispatch_sync时，一定会造成死锁吗？
按照苹果官方描述，是会造成死锁的。
>Do not call the dispatch_sync function from a task that is executing on the same queue that you pass to your function call. Doing so will deadlock the queue. If you need to dispatch to the current queue, do so asynchronously using the dispatch_async function.

但是，这段描述是不严谨的。实际上，只有当queueA是serialQueue时，才会造成死锁。   
原因是：   
- dispatch_sync 会阻塞当前线程，直到block中的代码执行结束。  
- 如果你在serialQueue上调用dispatch_sync，你也同时阻塞了这个queue，因为此时无法另外开启线程执行block中的代码。  
- 但是，如果是concurrentQueue，则不会死锁，因为，虽然当前线程阻塞了，还可以在其他线程执行block中的代码，等代码执行结束后，被阻塞的线程也就恢复正常执行。

另外，
>As an optimization, this function invokes the block on the current thread when possible.

根据文档描述，这里有个特殊优化。blockd中的代码会尽量在当前线程执行。  
但是，不是说当前线程已经被阻塞了吗？为何又能执行了？看来这里GCD有做一些特殊处理。

  
参考链接：  
[dispatch_sync inside dispatch_sync causes deadlock](https://stackoverflow.com/questions/23939730/dispatch-sync-inside-dispatch-sync-causes-deadlock)  
