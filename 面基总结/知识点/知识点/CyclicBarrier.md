# CyclicBarrier

## 概念

CyclicBarrier直译为:循环屏障(是可循环使用（Cyclic）的屏障（Barrier）).可以让一组线程达到一个屏障时被阻塞. 直到最后一个线程达到屏障时,所有被阻塞的线程才能继续执行

CyclicBarrier好比一扇门，默认情况下关闭状态，堵住了线程执行的道路，直到所有线程都就位，门才打开，让所有线程一起通过。

## 使用场景

CyclicBarrier可以用于多线程计算数据，最后合并计算结果的应用场景。比如我们用一个Excel保存了用户所有银行流水，每个Sheet保存一个帐户近一年的每笔银行流水，现在需要统计用户的日均银行流水，先用多线程处理每个sheet里的银行流水，都执行完之后，得到每个sheet的日均银行流水，最后，再用barrierAction用这些线程的计算结果，计算出整个Excel的日均银行流水。

示例:

```java
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        CyclicBarrier barrier = new CyclicBarrier(3, new Runnable() {
            @Override
            public void run() {
                System.out.println("所有线程都完成自己的任务了，现在可以合并结果了。");
            }
        });
//        CyclicBarrier barrier = new CyclicBarrier(3);
        new Thread(new Runnable() {
            @Override
            public void run() {

                try {
                    System.out.println("线程1执行自己的一部分工作...");
                    barrier.await();
                    System.out.println("最终结果合并完成，线程1可以退出....");
                } catch (Exception e) {
                    e.printStackTrace();
                }

            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {

                try {
                    System.out.println("线程2执行自己的一部分工作...");
//                    Thread.sleep(5000);
                    barrier.await();
                    System.out.println("最终结果合并完成，线程2可以退出....");
                } catch (Exception e) {
                    e.printStackTrace();
                }

            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {

                try {
                    System.out.println("线程3执行自己的一部分工作...");
                    barrier.await();
                    System.out.println("最终结果合并完成，线程3可以退出....");
                } catch (Exception e) {
                    e.printStackTrace();
                }

            }
        }).start();
    }
}
```

输出:

```text
线程1执行自己的一部分工作...
线程2执行自己的一部分工作...
线程3执行自己的一部分工作...
所有线程都完成自己的任务了，现在可以合并结果了。
最终结果合并完成，线程3可以退出....
最终结果合并完成，线程1可以退出....
最终结果合并完成，线程2可以退出....
```

上面的例子，启动了3个线程，分别作了一些事情，最后3个线程执行完成后，回调了事先定义好的一个Runnable的run方法。

有点类似于大数据中的分布式计算的思想，分而治之，最后汇总数据的意思。

## 源码分析

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200828151257705.png" alt="image-20200828151257705" />

可以看到, CyclicBarrier的源码还算是比较清晰, 主要参数是一个ReetratLock: lock, 2个int变量:count, parties, 1个Runnable任务, 还有一个内部类

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200828151500387.png" alt="image-20200828151500387" style="zoom:50%;" />

​																							                     (图片来自网络)

### 构造方法

```java
 public CyclicBarrier(int parties) {
   this(parties, null);
 }

public CyclicBarrier(int parties, Runnable barrierAction) {
  if (parties <= 0) throw new IllegalArgumentException();
  this.parties = parties;
  this.count = parties;
  this.barrierCommand = barrierAction;
}
```

可以看到, 就是初始化了变量count, parties值, 记录下传入的Runnable任务.

从上面的示例中, 接着启动了3个线程, 每个线程开始执行,各自分别调用了barrier.await()的方法。它的源码如下：

```java
public int await() throws InterruptedException, BrokenBarrierException {
  try {
    return dowait(false, 0L);
  } catch (TimeoutException toe) {
    throw new Error(toe); // cannot happen
  }
}

private int dowait(boolean timed, long nanos)
  throws InterruptedException, BrokenBarrierException,
	TimeoutException {
  final ReentrantLock lock = this.lock;
  lock.lock();
  try {
    final Generation g = generation;

    if (g.broken)
      throw new BrokenBarrierException();

    if (Thread.interrupted()) {
      breakBarrier();
      throw new InterruptedException();
    }
		// 扣减count
    int index = --count;
    if (index == 0) {  // tripped
      // 扣减count为0,调用定义好的Runnable任务
      boolean ranAction = false;
      try {
        final Runnable command = barrierCommand;
        if (command != null)
          command.run();
        ranAction = true;
        nextGeneration();
        return 0;
      } finally {
        if (!ranAction)
          breakBarrier();
      }
    }

    // loop until tripped, broken, interrupted, or timed out
    // 如果count没有扣减为0,当前线程进入condition等待队列
    for (;;) {
      try {
        if (!timed)
          trip.await();
        else if (nanos > 0L)
          nanos = trip.awaitNanos(nanos);
      } catch (InterruptedException ie) {
        if (g == generation && ! g.broken) {
          breakBarrier();
          throw ie;
        } else {
          // We're about to finish waiting even if we had not
          // been interrupted, so this interrupt is deemed to
          // "belong" to subsequent execution.
          Thread.currentThread().interrupt();
        }
      }

      if (g.broken)
        throw new BrokenBarrierException();

      if (g != generation)
        return index;

      if (timed && nanos <= 0L) {
        breakBarrier();
        throw new TimeoutException();
      }
    }
  } finally {
    lock.unlock();
  }
}

private void nextGeneration() {
  // signal completion of last generation
  // 唤醒队列中线程
  trip.signalAll();
  // set up next generation
  // 将count重置为parties, 就是构造方法中初始化值.
  count = parties;
  generation = new Generation();
}
```

这个方法核心脉络如下：

入参timed=false, nanos=0L

1、首先通过ReentrantLock进行了加锁,因为要操作count、parties这些int变量，非线程安全的，所以需要加锁。

2、其次两个if是一些校验而已，可以过，核心是扣减了—count

3、之后是if判断，如果扣减为0会执行线程任务和nextGeneration();这个方法。（可以猜到，当3个线程都扣减为0后，会做一些事情,基于parties，将count恢复成3，唤醒Condition等待队列中所有线程，重新创建一个Generation，这个不是很核心的内部类，可以暂时忽略。）

4、最后的脉络是一个for循环，核心会执行trip.await();这个trip不就是Condition么，await后会释放Reentrant锁，进入Condition等待队列。

上述整个流程如下所示:

![image-20200828153006959](/Users/lee/Library/Application Support/typora-user-images/image-20200828153006959.png)

![image-20200828153021077](/Users/lee/Library/Application Support/typora-user-images/image-20200828153021077.png)

![image-20200828153032474](/Users/lee/Library/Application Support/typora-user-images/image-20200828153032474.png)

![image-20200828153042599](/Users/lee/Library/Application Support/typora-user-images/image-20200828153042599.png)

## 实现原理

上述流程图就是CyclicBarrier原理，其实Cylic是重用的意思，所以重置了count数量，它的基本原理很简单，就是基于ReentrantLock+Condition等待队列+Count数量做到了分而治之线程执行效果。

## 小结

CyclicBarrier与CountDownLatch很类似, 使用场景上也有些类似.CountDownLatch的计数器只能使用一次。而CyclicBarrier的计数器可以使用reset() 方法重置。所以CyclicBarrier能处理更为复杂的业务场景，比如如果计算发生错误，可以重置计数器，并让线程们重新执行一次。

CyclicBarrier还提供其他有用的方法，比如getNumberWaiting方法可以获得CyclicBarrier阻塞的线程数量。isBroken方法用来知道阻塞的线程是否被中断。比如以下代码执行完之后会返回true。