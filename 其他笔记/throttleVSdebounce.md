#### 防抖

- 当持续触发事件时，保证只执行最后一次事件处理函数

在给DOM绑定事件时，有些事件我们是无法控制触发频率的。 如鼠标移动事件`onmousemove`, 滚动滚动条事件`onscroll`，窗口大小改变事件`onresize`，瞬间的操作都会导致这些事件会被高频触发。 如果事件的回调函数较为复杂，就会导致响应跟不上触发，出现页面卡顿，假死现象。 在实时检查输入时，如果我们绑定`onkeyup`事件发请求去服务端检查，用户输入过程中，事件的触发频率也会很高，会导致大量的请求发出，响应速度会大大跟不上触发。

****

#### 节流

- 当持续触发事件时，保证一定时间段内只调用一次事件处理函数

当用户连续操作的时候，我们设置一个定时器，一段时间后执行函数，并且执行完把标记改为true，所以在函数没有执行完之前一直是false，可能有的同学认为连续操作那么函数连续运行，那么canRun这个标记不一直是true吗，并不是，注意这里是一个闭包结构，真正每次调用的其实是throttle里面return的函数并不是每次都调用throttle，所以canRun只是提供初始值，并不会每次都重新赋值为true。所以用户连续操作的时候，比如用户一秒钟连续操作了十次，但是对于我们这个代码来说，只可能每500毫秒执行一次，因为只有500ms之后canRun才为true，才能开启下一个定时器。所以哪怕用户一秒钟之内连续点了十次，其实也只是能执行两次。这就符合节流的本意。

****

###### 总结

防抖动和节流本质是不一样的。防抖动是将多次执行变为最后一次执行，节流是将多次执行变成每隔一段时间执行。