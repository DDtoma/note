# AQS

## 什么是AQS
AbstractQueuedSynchronizer（简称为AQS），AQS是一种提供了原子式管理同步状态、阻塞和唤醒线程功能以及队列模型的简单框架。

![[Pasted image 20210726121630.png]]

> A synchronizer that may be exclusively owned by a thread. 保存当前执行的线程
`public abstract class AbstractOwnableSynchronizer`


> Provides a framework for implementing blocking locks and related synchronizers (semaphores, events, etc) that rely on first-in-first-out (FIFO) wait queues.
> 抽象的队列式的同步器
`public abstract class AbstractQueuedSynchronizer`

## AQS实现

![[Pasted image 20210726122825.png]]


```java
public final void acquire(int arg) {  
    if (!tryAcquire(arg) &&  
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))  
        selfInterrupt();  
}
```
> 获取锁：尝试获取，在获取不成功的情况下加入等待队列。中断当前执行线程

```java
private Node addWaiter(Node mode) {  
    Node node = new Node(Thread.currentThread(), mode);  
    // Try the fast path of enq; backup to full enq on failure  
    Node pred = tail;  
    if (pred != null) {  
        node.prev = pred;  
        if (compareAndSetTail(pred, node)) {  
            pred.next = node;  
            return node;  
        }  
    }  
    enq(node); //Inserts node into queue, initializing if necessary.
    return node;  
}
```
> 将当前线程封装为node，加入queue


```java
public final boolean release(int arg) {  
    if (tryRelease(arg)) {  
        Node h = head;  
        if (h != null && h.waitStatus != 0)  
            unparkSuccessor(h);  
        return true;  
    }  
    return false;  
}
```
> 释放锁：释放持有的资源，然后运行头节点。


各种不用的同步器，通过实现tryAcquire、tryRelease来实现不同的同步策略和功能。比如可冲入锁，公平锁，非公平锁等。

### ReentrantLock
`ReentrantLock`实现了AQS的`Sync`来进行同步，ReentrantLock是可重入锁，同时实现了公平锁和非公平锁。
> 可重入实现
```java
// 线程一样可重入锁状态+1  
else if (current == getExclusiveOwnerThread()) {  
    int nextc = c + acquires;  
    if (nextc < 0) // overflow  
        throw new Error("Maximum lock count exceeded");  
    setState(nextc);
    return true;  
}
```
当前线程一直的情况下，state进行+1

> 非公平锁 NonfairSync
```java
final void lock() {  
    if (compareAndSetState(0, 1))  
        setExclusiveOwnerThread(Thread.currentThread());  
    else  
        acquire(1);  
}
```
先主动获取线程，不成功的情况下，进行acquire

### CountDownLatch

### CyclicBarrier

### Semaphore


[三太子敖丙：1.5w字，30图带你彻底掌握 AQS！](https://mp.weixin.qq.com/s/iNz6sTen2CSOdLE0j7qu9A)

[美团：从ReentrantLock的实现看AQS的原理及应用](https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html)

[终于有人把 CountDownLatch，CyclicBarrier，Semaphore 说明白了!](https://mp.weixin.qq.com/s/TDw7GnzDw5FK3RWwkIzzZA)