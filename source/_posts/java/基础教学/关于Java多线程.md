---
title: 关于 Java 多线程
date: 2019-12-23 16:00:00
categories:
 - java
 - java 基础
tags:
 - java
 - java 多线程
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---


## java 多线程

 Java 给多线程编程提供了内置的支持。 一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。多线程是多任务的一种特别的形式，但多线程使用了更小的资源开销。多线程能满足程序员编写高效率的程序来达到充分利用 CPU 的目的。本节主要讲解 Java 多线程的一些概念以及其实现。

* 多线程
* 线程变量
* 线程同步 
* Lock 与 Unlock
* 死锁
* 线程生命周期 
* ArrayBlockingQueue
* 生产者消费者模式
* 线程池

> 线程：程序执行流的最小单元。它是进程内一个相对独立的、可调度的执行单元，是系统独立调度和分派 CPU 的基本单位。

如同大自然中的万物，线程也有“生老病死”的过程，下图表示了一个线程从创建到消亡的过程，以及过程中的状态。  

结合线程的生命周期来看看多线程的定义：

> 多线程：从软件或者硬件上实现多个线程并发执行的技术。在单个程序中同时运行多个线程完成不同的工作。  

在 Java 中，[垃圾回收机制](http://baike.baidu.com/view/159846.htm)就是通过一个线程在后台实现的，这样做的好处在于：开发者通常不需要为内存管理投入太多的精力。反映到我们现实生活中，在浏览网页时，浏览器能够同时下载多张图片；实验楼的服务器能够容纳多个用户同时进行在线实验，这些都是多线程带来的好处。  

从专业的角度来看，多线程编程是为了最大限度地利用 CPU 资源——当处理某个线程不需要占用 CPU 而只需要利用 IO 资源时，允许其他的那些需要 CPU 资源的线程有机会利用 CPU。这或许就是多线程编程的最终目的。当然，你也可以进一步了解。  

对于多线程和线程之间的关系，你可以这样理解：一个使用了多线程技术的程序，包含了两条或两条以上并发运行的线程（***Thread***）  

Java 中的***Thread***类就是专门用来创建线程和操作线程的类。

### 创建线程

创建线程的方法：  

1. 继承 Thread 类并重写它的run()方法，然后用这个子类来创建对象并调用start()方法。  
2. 定义一个类并实现 Runnable 接口，实现run()方法

总的来说就是线程通过 start()方法启动而不是 run()方法，run()方法的内容为我们要实现的业务逻辑  

### 编程示例

```
public class CreateThread {

    public static void main(String[] args) {
        Thread1 thread1 = new Thread1();
        //声明一个Thread1对象，这个Thread1类继承自Thread类的

        Thread thread2 = new Thread(new Thread2());
        //传递一个匿名对象作为参数

        thread1.start();
        thread2.start();
        //启动线程
    }
}

class Thread1 extends Thread {
    @Override
    public void run() {
        //在run()方法中放入线程要完成的工作

        //这里我们把两个线程各自的工作设置为打印100次信息
        for (int i = 0; i < 100; ++i) {
            System.out.println("Hello! This is " + i);
        }

        //在这个循环结束后，线程便会自动结束
    }
}

class Thread2 implements Runnable {
    //与Thread1不同，如果当一个线程已经继承了另一个类时，就建议你通过实现Runnable接口来构造

    @Override
    public void run() {
        for (int i = 0; i < 100; ++i) {
            System.out.println("Thanks. There is " + i);
        }
    }
}
```
你在控制台就可以看到两个线程近似交替地在输出信息。受到系统调度的影响，两个线程输出信息的先后顺序可能不同。  

### 线程变量

> ThreadLocal，即线程变量，是一个以 ThreadLocal 对象为键、任意对象为值的存储结构。这个结构被附带在线程上，也就是说一个线程可以根据一个 ThreadLocal 对象查询到绑定在这个线程上的一个值。
> 可以通过 set(T)方法来设置一个值，在当前线程下再通过 get()方法获取到原先设置的值。

示例代码：  

```
public class ThreadLocalDemo {

    public static void main(String[] args) {
        ThreadDemo threadDemo = new ThreadDemo();
        //启动2个线程
        new Thread(threadDemo).start();
        new Thread(threadDemo).start();

    }
}

class ThreadDemo implements Runnable {
    //使用ThreadLocal提供的静态方法创建一个线程变量 并且初始化值为0
    private static ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 0);

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            //get方法获取线程变量值
            Integer integer = threadLocal.get();
            integer += 1;
            //set方法设置线程变量值
            threadLocal.set(integer);
            System.out.println(integer);
        }
    }
}
```

通过输出控制台的结果我们可以看到，两个线程之间的变量互不干涉  

### 线程共享变量  

如果我们去掉了ThreadLocal,其他的流程都不改变,已经使用2个线程自增变量会如何呢？  

修改ThreadLoacalDemo.java

```
public class ThreadLocalDemo {

    public static void main(String[] args) {
        ThreadDemo threadDemo = new ThreadDemo();
        new Thread(threadDemo).start();
        new Thread(threadDemo).start();

    }
}

class ThreadDemo implements Runnable {
    private Integer integer = 0;

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            integer++;
            System.out.println(integer);
        }
    }
}
```

在没有加入 ThreadLocal 的情况下，发现 integer 变量的值增加到了 20，那是因为这个时候两个线程都是使用同一对象threadDemo的变量，这个时候的 integer 就变成了线程共享变量，如果同学们多运行几次，还有可能出现最后结果是 18 19 的情况，那是因为如果不做任何处理，线程共享变量都不是线程安全的，也就是说在多线程的情况下，共享变量有可能会出错  

### 线程同步 

当多个线程操作同一个对象时，就会出现线程安全问题，被多个线程同时操作的对象数据可能会发生错误。线程同步可以保证在同一个时刻该对象只被一个线程访问。  

#### Synchronized

关键字 synchronized 可以修饰方法或者以同步块的形式来进行使用，它确保多个线程在同一个时刻，只能有一个线程处于方法或者同步块中，保证了线程对变量访问的可见性和排他性。它有三种使用方法：  

* 对普通方式使用,将会锁住当前实例对象  
* 对静态方法使用,将会锁住当前类的Class对象  
* 对代码块使用,将会锁住代码块中的对象  

使用示例  

在下面的代码中, 演示了三种加锁方式.  

```
public class SynchronizedDemo {
    private static Object lock = new Object();

    public static void main(String[] args) {
        //同步代码块 锁住lock
        synchronized (lock) {
            //doSomething
        }
    }

    //静态同步方法  锁住当前类class对象
    public synchronized static void staticMethod(){

    }
    //普通同步方法  锁住当前实例对象
    public synchronized void memberMethod() {

    }
}
```
### java.util.concurrent  

java.util.concurrent 包是 java5 开始引入的并发类库，提供了多种在并发编程中的适用工具类。包括原子操作类，线程池，阻塞队列，Fork/Join 框架，并发集合，线程同步锁等。  

### Lock 与 Unlock  

JUC 中的 ReentrantLock 是多线程编程中常用的加锁方式，ReentrantLock 加锁比 synchronized 加锁更加的灵活，提供了更加丰富的功能。  

```
import java.util.concurrent.locks.ReentrantLock;

public class LockDemo {
    private static ReentrantLock lock = new ReentrantLock();

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            lock.lock();
            try {
                //需要同步的代码块
                System.out.println("线程1加锁");
            }finally {
//                一定要在finally中解锁，否则可能造成死锁
                lock.unlock();
                System.out.println("线程1解锁");
            }
        });
        thread1.start();
        Thread thread2 = new Thread(() -> {
            lock.lock();
            try {
                System.out.println("线程2加锁");
            }finally {
                lock.unlock();
                System.out.println("线程2解锁");
            }
        });
        thread2.start();
    }

}
```
在上面的编程实例中，线程 1 获取了 lockA 的锁后再去获取 lockB 的锁，而此时 lockB 已经被线程 2 获取，同时线程 2 也想获取 lockA，两个线程进这样僵持了下去，谁也不让，造成了死锁。在编程时，应该避免死锁的出现。  

### 饥饿

饥饿是指一个可运行的进程尽管能继续执行，但被调度器无限期地忽视，而不能被调度执行的情况  
比如当前线程处于一个低优先级的情况下，操作系统每次都调用高优先级的线程运行，就会导致当前线程虽然可以运行，但是一直不能被运行的情况。  

### 线程生命周期

线程的声明周期共有 6 种状态，分别是：新建New、运行（可运行）Runnable、阻塞Blocked、计时等待Timed Waiting、等待Waiting和终止Terminate。  

> 当你声明一个线程对象时，线程处于新建状态，系统不会为它分配资源，它只是一个空的线程对象  
> 调用start()方法时，线程就成为了可运行状态，至于是否是运行状态，则要看系统的调度了
> 调用了sleep()方法、调用wait()方法和 IO 阻塞时，线程处于等待、计时等待或阻塞状态。  
> 当run()方法执行结束后，线程也就终止了。  

示例  

```
public class ThreadState implements Runnable {

    public synchronized void waitForAMoment() throws InterruptedException {

        wait(500);
        //使用wait()方法使当前线程等待500毫秒
        //或者等待其他线程调用notify()或notifyAll()方法来唤醒
    }

    public synchronized void waitForever() throws InterruptedException {

        wait();
        //不填入时间就意味着使当前线程永久等待，
        //只能等到其他线程调用notify()或notifyAll()方法才能唤醒
    }

    public synchronized void notifyNow() throws InterruptedException {

        notify();
        //使用notify()方法来唤醒那些因为调用了wait()方法而进入等待状态的线程
    }

    @Override
    public void run() {

        //这里用异常处理是为了防止可能的中断异常
        //如果任何线程中断了当前线程，则抛出该异常

        try {
            waitForAMoment();
            // 在新线程中运行waitMoment()方法

            waitForever();
            // 在新线程中运行waitForever()方法

        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

实现代码示例：

```
public class ThreadTest {
    public static void main(String[] args) throws InterruptedException {
        ThreadState state = new ThreadState();
        //声明并实例化一个ThreadState对象

        Thread thread = new Thread(state);
        //利用这个名为state的ThreadState对象来创建Thread对象

        System.out.println("Create new thread: " + thread.getState());
        //使用getState()方法来获得线程的状态，并进行输出

        thread.start(); 
        //使用thread对象的start()方法来启动新的线程

        System.out.println("Start the thread: " + thread.getState());
        //输出线程的状态

        Thread.sleep(100); 
        //通过调用sleep()方法使当前这个线程休眠100毫秒，从而使新的线程运行waitForAMoment()方法

        System.out.println("Waiting for a moment (time): " + thread.getState());
        //输出线程的状态

        Thread.sleep(1000); 
        //使当前这个线程休眠1000毫秒，从而使新的线程运行waitForever()方法

        System.out.println("Waiting for a moment: " + thread.getState());
        //输出线程的状态

        state.notifyNow(); 
        // 调用state的notifyNow()方法

        System.out.println("Wake up the thread: " + thread.getState());
        //输出线程的状态

        Thread.sleep(1000); 
        //使当前线程休眠1000毫秒，使新线程结束

        System.out.println("Terminate the thread: " + thread.getState());
        //输出线程的状态
    }
}
```

### ArrayBlockingQueue

> ArrayBlockingQueue 是由数组支持的有界阻塞队列.位于java.util.concurrent包下

首先看看其构造方法:  

| 构造方法 | 描述 |
| ------- | ------- |
| public ArrayBlockingQueue(int capacity) | 构造大小为capacity的队列 |
| public ArrayBlockingQueue(int capacity, boolean fair) | 指定队列大小，以及内部实现是公平锁还是非公平锁 |  
| public ArrayBlockingQueue(int capacity, boolean fair, Collection<? extends E> c) | 指定队列大小, 以及锁实现, 并且在初始化是加入集合c |  

入队常用方法:  

| 入队方法 | 队列已满 | 队列未满 |  
| ------- | ------- | -------- |  
| add | 抛出异常 | 返回true |  
| offer | 返回false | 返回true |  
| put | 阻塞直到插入 | 没有返回值 |  

出队常用方法:  

| 出队方法 | 队列为空 | 队列不为空 |  
| -------  | ------- | ------ |
| remove | 抛出异常 | 移出并返回队首 |  
| poll | 返回null | 移出并返回队首 |  
| take | 阻塞直到返回 | 移出并返回队首 |  

示例源码:

```
import java.util.concurrent.ArrayBlockingQueue;

public class ABQDemo {
    //构建大小为10的阻塞队列
    private static ArrayBlockingQueue<Integer> arrayBlockingQueue = new ArrayBlockingQueue<>(10);

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            for (int i = 1; i <= 10; i++) {
                arrayBlockingQueue.add(i);
            }
        });
        thread1.start();
        try {
            //等待线程1执行完毕
            thread1.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(() -> {
            //如果插入失败
            if (!arrayBlockingQueue.offer(11)) {
                System.out.println("插入元素11失败");
            }
            try {
                //一直阻塞直到插入元素11，注意这里阻塞的不是主线程，main方法还是继续运行
                arrayBlockingQueue.put(11);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }).start();
        Thread thread2=new Thread(() -> {
            Integer element;
            System.out.println("开始出队:");
            //打印队列中的元素
            while ((element = arrayBlockingQueue.poll()) != null) {
                System.out.print("\t"+element);
            }
        });
        thread2.start();
    }
}
```


### 生产者消费者模式

> 生产者消费者模式是多线程编程中非常重要的设计模式，生产者负责生产数据，消费者负责消费数据。生产者消费者模式中间通常还有一个缓冲区，用于存放生产者生产的数据，而消费者则从缓冲区中获取，这样可以降低生产者和消费者之间的耦合度。举个例子来说吧，比如有厂家，代理商，顾客，厂家就是生产者，顾客就是消费者，代理商就是缓冲区，顾客从代理商这里买东西，代理商负责从厂家处拿货，并且销售给顾客，顾客不用直接和厂家打交道，并且通过代理商，就可以直接获取商品，或者从代理商处知道货物不足，需要等待。

示例代码：  

```
import java.util.Random;
import java.util.concurrent.LinkedBlockingQueue;

public class PCModel {
    private static LinkedBlockingQueue<Integer> queue = new LinkedBlockingQueue<>();

    public static void main(String[] args){
        // 生产者
        Thread provider = new Thread(()-> {
            // 执行代码块
            Random random = new Random();
            for (int j = 0; j < 5; j++){
                try{
                    int i = random.nextInt();
                    // 直到插入数据
                    queue.put(i);
                    System.out.println("生产数据："+ i);
                    Thread.sleep(1000);// 睡眠程序
                } catch(InterruptedException e){
                    e.printStackTrace();// 抛出异常
                }
            }
        });
        // 消费者
        Thread consumer = new Thread(()->{
            Integer data;
            for (int i = 0; i < 5; i++){
                try{
                    // 阻塞直到取出数据
                    data = queue.take();
                    System.out.println("消费数据:"+data);
                    Thread.sleep(1000);
                } catch(InterruptedException e){
                    e.printStackTrace();
                }
            }
        });

        // 启动线程
        provider.start();
        consumer.start();
    }
}
```

### 线程池

> 线程池（英语：thread pool）：一种线程使用模式。线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能够保证内核的充分利用，还能防止过分调度。

由于Java创建和销毁线程都会带来资源上的销毁,所以线程池可以帮助我们复用线程，减少资源消耗  

Java 线程池可以通过 Executors 工具类创建，Executors 常用方法：  

* newFixedThreeadPoo(int nThreads); 创建一个固定大小为n的线程池
* newSingleThreadExecutor(); 创建只有一个线程的线程池  
* newCachedThreadPool(); 创建一个根据需要创建新线程的线程池

示例代码：  

```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolDemo {
    //使用Executors 创建一个固定大小为5的线程池
    private static ExecutorService executorService = Executors.newFixedThreadPool(5);

    public static void main(String[] args) {
//        提交任务
        executorService.submit(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.print(i + " ");
            }
        });
        //停止线程池 并不会立即关闭 ，而是在线程池中的任务执行完毕后才关闭
        executorService.shutdown();
    }
}
```

除了使用***Executors***工具类帮助我们创建之外，也可以直接创建线程池  

示例代码：  

```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

public class ThreadPoolDemo2 {
    private static ExecutorService executorService = new ThreadPoolExecutor(
            5, //核心线程数为5
            10,//最大线程数为10
            0L, TimeUnit.MILLISECONDS,//非核心线程存活时间
            new LinkedBlockingQueue<>());//任务队列 

    public static void main(String[] args) {
        //提交任务
        executorService.submit(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.print(i + " ");
            }
        });
        //关闭线程池
        executorService.shutdown();
    }
}
```
