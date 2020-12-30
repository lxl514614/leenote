# CountDownLatch

## 概念

一个同步辅助类，在完成一组正在其他线程中执行的操作之前，它允许一个或多个线程一直等待。

用给定的*计数* 初始化 `CountDownLatch`。由于调用了 [`countDown()`](https://aisia.moe/java6api-cn/java/util/concurrent/CountDownLatch.html#countDown()) 方法，所以在当前计数到达零之前，[`await`](https://aisia.moe/java6api-cn/java/util/concurrent/CountDownLatch.html#await()) 方法会一直受阻塞。之后，会释放所有等待的线程，[`await`](https://aisia.moe/java6api-cn/java/util/concurrent/CountDownLatch.html#await()) 的所有后续调用都将立即返回。这种现象只出现一次——计数无法被重置。如果需要重置计数，请考虑使用 [`CyclicBarrier`](https://aisia.moe/java6api-cn/java/util/concurrent/CyclicBarrier.html)。

`CountDownLatch` 是一个通用同步工具，它有很多用途。将计数 1 初始化的 `CountDownLatch` 用作一个简单的开/关锁存器，或入口：在通过调用 [`countDown()`](https://aisia.moe/java6api-cn/java/util/concurrent/CountDownLatch.html#countDown()) 的线程打开入口前，所有调用 [`await`](https://aisia.moe/java6api-cn/java/util/concurrent/CountDownLatch.html#await()) 的线程都一直在入口处等待。用 *N* 初始化的 `CountDownLatch` 可以使一个线程在 *N* 个线程完成某项操作之前一直等待，或者使其在某项操作完成 N 次之前一直等待。

`CountDownLatch` 的一个有用特性是，它不要求调用 `countDown` 方法的线程等到计数到达零时才继续，而在所有线程都能通过之前，它只是阻止任何线程继续通过一个 [`await`](https://aisia.moe/java6api-cn/java/util/concurrent/CountDownLatch.html#await())。

示例:

```java
public static void main(String[] args) throws Exception {
    CountDownLatch countDownLatch = new CountDownLatch(2);
    new Thread(() -> {
      try {
        Thread.sleep(1000);
        System.out.println("线程1执行完成");
      } catch (Exception e) {
        e.printStackTrace();
      } finally {
        countDownLatch.countDown();
      }
    }).start();

    new Thread(() ->{
      try {
        Thread.sleep(2000);
        System.out.println("线程2执行完成");
      } catch (Exception e) {
        e.printStackTrace();
      } finally {
        countDownLatch.countDown();
      }
    }).start();

    System.out.println("等待两个线程执行完成");

    countDownLatch.await(); // 阻塞

    System.out.println("任务执行完成");
}
```

简单的说，CountDownLatch作用：

其实就像线程加入方法，join()类似，阻塞了main线程,需要等待加入的线程执行完成才能继续执行。它的await()方法，要求需要达到CountDownLatch指定数量的次数countDown方法调用后，await()才可以继续执行。

## 使用场景

CountDownLatch非常适合于对任务进行拆分，使其并行执行，比如某个任务执行2s，其对数据的请求可以分为五个部分，那么就可以将这个任务拆分为5个子任务，分别交由五个线程执行，执行完成之后再由主线程进行汇总，此时，总的执行时间将决定于执行最慢的任务，平均来看，还是大大减少了总的执行时间。

1. 开始执行前等待n个线程完成各自任务: 例如table_A、table_B、table_A、table_D统计4个表数量，然后进行统计总数量。

另一种典型用法是，将一个问题分成 N 个部分，用执行每个部分并让锁存器倒计数的 Runnable 来描述每个部分，然后将所有 Runnable 加入到 Executor 队列。当所有的子部分完成后，协调线程就能够通过 await。

1. 实现最大的并行性：模拟了100米赛跑，10名选手已经准备就绪，只等裁判一声令下。当所有人都到达终点时，比赛结束。

### CountDownLatch在我们程序中的应用

`MultiThreadDownLoad`, 主要作用: 在爬取小视频文件时,在下载文件中,做断点下载

## 源码分析

![image-20200814104149113](/Users/lee/Library/Application Support/typora-user-images/image-20200814104149113.png)

由上图可以看出, CountDownLatch的脉络非常简单, 核心方法主要就有两个`await()` 和`countDown()`,	`countDown()`方法用于使计数器减一, 一般是执行任务的线程调用.

countDown()方法并没有规定一个线程只能调用一次，当同一个线程调用多次countDown()方法时，每次都会使计数器减一

另外，await()方法也并没有规定只能有一个线程执行该方法，如果多个线程同时执行await()方法，那么这几个线程都将处于等待状态，并且以共享模式享有同一个锁。

### 实现原理

 CountDownLatch是基于AbstractQueuedSynchronizer实现的，在AbstractQueuedSynchronizer中维护了一个volatile类型的整数state，volatile可以保证多线程环境下该变量的修改对每个线程都可见，并且由于该属性为整型，因而对该变量的修改也是原子的。创建一个CountDownLatch对象时，所传入的整数n就会赋值给state属性，当countDown()方法调用时，该线程就会尝试对state减1，而调用await()方法时，当前线程就会判断state属性是否为0，如果为0，则继续往下执行，如果不为0，则使当前线程进入等待状态，直到某个线程将state属性置为0，其就会唤醒在await()方法中等待的线程。



### CountDownLatch的创建

```java
public CountDownLatch(int count) {
  if (count < 0) throw new IllegalArgumentException("count < 0");
  this.sync = new Sync(count);
}
```

从源码上可以看到, 其实就是初始化了个Sync组件.Sync是CountDownLatch的内部类.也同样继承了`AbstractQueuedSynchronizer`类.初始化state为入参传入的值.比如上面代码中count=2,所以state=2

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200814105917598.png" alt="image-20200814105917598" style="zoom:50%;" />

​																														(图片来自网络)

### CountDownLatch的countDown方法

```java
public void countDown() {
  // 计数器减1, 尝试释放锁
  sync.releaseShared(1);
}

public final boolean releaseShared(int arg) {
  // 根据tryReleaseShared返回值尝试释放共享锁
  if (tryReleaseShared(arg)) {
    doReleaseShared();
    return true;
  }
  return false;
}


protected boolean tryReleaseShared(int releases) {
  // 对aqs的state进行操作.
  for (;;) {
    // 获取当前state属性值
    int c = getState();
    // 如果state为0, 则说明当前计数器已经完成计数,直接返回
    if (c == 0)
      return false;
    int nextc = c-1;
    // CAS算法对state值设置,保证原子性
    if (compareAndSetState(c, nextc))
      // 设置成功后返回当前是否为最后一个设置state的线程
      return nextc == 0;
  }
}

// 唤醒,出队
private void doReleaseShared() {
  for (;;) {
    // 记录等待队列中头结点的线程
    Node h = head;
    // 头结点不为空，且头结点不等于尾节点
    if (h != null && h != tail) {
      int ws = h.waitStatus;
      // SIGNAL状态表示当前节点正在等待被唤醒
      if (ws == Node.SIGNAL) {
        // 清除当前节点的等待状态
        if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
          continue;    // loop to recheck cases
        // 唤醒当前节点的下一个节点
        unparkSuccessor(h);
      }
      else if (ws == 0 &&
               !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
        continue;                // loop on failed CAS
    }
    // 如果h还是指向头结点，说明前面这段代码执行过程中没有其他线程对头结点进行过处理
    if (h == head)                   // loop if head changed
      break;
  }
}
```



核心是两步

- 第一步tryReleaseShared()尝试释放锁
- 第二步doReleaseShared会进行唤醒和出队。

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200814112453328.png" alt="image-20200814112453328" style="zoom:50%;" />

​																												(图片来自网络)

### CountDownLatch的await方法

```java
public void await() throws InterruptedException {
  sync.acquireSharedInterruptibly(1);
}


public final void acquireSharedInterruptibly(int arg)
            throws InterruptedException {
  if (Thread.interrupted())
    throw new InterruptedException();
  if (tryAcquireShared(arg) < 0)
    doAcquireSharedInterruptibly(arg);
}

protected int tryAcquireShared(int acquires) {
  return (getState() == 0) ? 1 : -1;
}

private void doAcquireSharedInterruptibly(int arg)
        throws InterruptedException {
   // 使用当前线程创建一个共享模式的节点
  final Node node = addWaiter(Node.SHARED);
  boolean failed = true;
  try {
    for (;;) {
      // 获取当前节点的前一个节点
      final Node p = node.predecessor();
      // 判断前一个节点是否为头结点
      if (p == head) {
        // 查看当前线程是否获取到了执行权限
        int r = tryAcquireShared(arg);
        // 大于0表示获取了执行权限
        if (r >= 0) {
          // 将当前节点设置为头结点，并且唤醒后面处于等待状态的节点
          setHeadAndPropagate(node, r);
          p.next = null; // help GC
          failed = false;
          return;
        }
      }
      // 走到这一步说明没有获取到执行权限，就使当前线程进入“搁置”状态
      if (shouldParkAfterFailedAcquire(p, node) &&
          parkAndCheckInterrupt())
        throw new InterruptedException();
    }
  } finally {
    if (failed)
      cancelAcquire(node);
  }
}
```

核心步骤:

- 获取锁，tryAcquireShared

- 之后如果获取失败，当前线程入队，挂起阻塞等待



<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200814113340449.png" alt="image-20200814113340449" style="zoom:50%;" />

​																												(图片来自网络)

入队后对头是一个空节点，之后就是等待线程Main线程了。当线程1和线程2执行完成countDown后，state变为0，无论是谁将state变成0，都会唤醒AQS等待的线程Main。这样Main线程就会继续执行了。

## 小结

CountDownLatch的基本原理，**countDown+await底层是基于继承AQS的Sync组件中的state+queue实现的。**

