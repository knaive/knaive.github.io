---
layout: post
title: java threads
category: java
tags:
    - java
---

## basics
### `Thread.sleep` will acquire the monitor
### `Object.wait` 
- 必须在当前线程获得monitor之后调用，否则会抛出异常IllegalMonitorStateException
- 调用这个方法会释放掉monitor并阻塞。
- 被唤醒的过程：wait首先获得monitor，然后才被唤醒。
### `Object.notify` 
- 发送信号唤醒一个等待的线程。
- 必须调用这个方法的线程获得了object的monitor之后才能调用，否则抛出IllegalMonitorStateExcepion。
- notify不会释放或者获取monitor，所以等待线程只有在调用notify的线程释放了monitor之后才会被唤醒

## race condition

- check and act
```java
if (someConditionHappens()) {
    doSomething();
}
```

- java code for `wait` and `notify`

```java
public class BlockingQueue {
    private Queue<String> queue = new LinkedList();

    public void add(String item) {
        queue.add(item);
        notify();
    }

    public String take() {
        if (queue.isEmpty()) {
            wait();
        }
        return queue.remove();
    }
}
```

## two issues

### why `wait` and `nofity` has to be in `synchronized` block
- **race condition**发生，假使`wait`和`notify`都不需要线程获得monitor：
    - 线程T1在`take`中如果发现队列不为空，正准备执行代码`queue.remove()`，但是还没有执行
    - 正在此时T2执行`take`执行了`if`语句，发现队列不空，不等待直接去除队列中唯一的item
    - T1从空队列中取item，异常

### why use loop to check condition instead of `if`

- **spurious wakeup**:因为硬件或者软件失灵，内核调度在很短的一段时间内故障，无法处理线程之间的信号。内核恢复后，对于故障发生期间可能存在的信号该如何处理？如果什么都不做，那么消费者就会错过信号，继续等待下去，并有可能永远等待下去。所以内核在故障恢复后选择给所有等待的线程（消费者）发送信号，如果生产者在故障期间并没有发送过信号，那么它们的消费者此时仍然会收到信号，这些信号就称之为*spurious wakeup*。消费者收到虚假唤醒，并不意味着等待的条件已经出现，仍然需要检查条件是否满足（使用`while`），这样就在应用层解决了虚假唤醒问题。
- IEEE Std 1003.1
    - On a multi-processor, it may be impossible for an implementation of `pthread_cond_signal` to avoid the unblocking of more than one thread blocked on a condition variable.


## references
- [synchronized for wait and notify](https://programming.guide/java/why-wait-must-be-in-synchronized.html)
- [spurious wakeup](http://opensourceforgeeks.blogspot.com/2014/08/spurious-wakeups-in-java-and-how-to.html)