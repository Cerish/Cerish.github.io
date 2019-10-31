---
title: js 单线程异步,宏任务，微任务
date: 2019-10-31 14:56:11
tags: hexo
---

在一次面试中，遇到了这样一个题目，请给出这段的运行程序。

    setTimeout(function(){
      console.log('1')
    });
    new Promise(function(resolve){
      console.log('2');
      resolve();
    }).then(function(){
      console.log('3')
    });
    console.log('4');

当时就有点懵逼，同步异步见过，但是同时出现两个异步函数，还真没遇到过。回来查资料才知道，宏任务与微任务的概念。

先看这张图，明确了异步任务中（如settimeout）等回调函数会在主线程的所有任务执行完成后，才进行执行。如果在异步回调时间内完成主线程任务，则回调函数可以规定的时间执行。不然的话，只能等到主队列完成之后在执行。

![](/images/js/Asynchronous/Asynchronous.png)

但是当有多个回调函数时，就得区分什么时宏任务与微任务了。在主线程中执行的任务，就属于宏任务。当一个宏任务执行完时，会先去查找任务队列中有没有微任务，如果有，优先执行所有微任务，再接着执行宏任务。

属于宏任务的有
![](/images/js/Asynchronous/macrotask.png)
属于微任务的有
![](/images/js/Asynchronous/microtask.png)

下面附上js单线程如何处理异步操作
![](/images/js/Asynchronous/singleThread.png)

event-loop 的作用：每办理完一个业务，柜员就会问当前的客户，是否还有其他需要办理的业务。（检查还有没有微任务需要处理）而客户明确告知说没有事情以后，柜员就去查看后边还有没有等着办理业务的人。（结束本次宏任务、检查还有没有宏任务需要处理）。

回到面试题，由于 promise.then 属于微任务，所以优先执行于settimeout 这个宏任务。运行结果为， 2 4 3 1。