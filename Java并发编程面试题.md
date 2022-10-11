# 参考
[面渣逆袭（Java并发编程面试题八股文）必看👍 | Java 程序员进阶之路 (tobebetterjavaer.com)](https://tobebetterjavaer.com/sidebar/sanfene/javathread.html)
# 问题
### 并行跟并发有什么区别？
### 说说什么是进程和线程？
### 线程的几种创建方式？
Java中创建线程主要有三种方式，分别为继承Thread类、实现Runnable接口、实现Callable接口。
![[Pasted image 20221010163100.png]]
使用Thread类和Runable接口都没有没返回值实现Callable接口的方式就可以实现带有返回方法的线程。
``` Java
public class CallerTask implements Callable<String> {
    public String call() throws Exception {
        return "Hello,i am running!";
    }

    public static void main(String[] args) {
        //创建异步任务
        FutureTask<String> task=new FutureTask<String>(new CallerTask());
        //启动线程
        new Thread(task).start();
        try {
            //等待执行完成，并获取返回结果
            String result=task.get();
            System.out.println(result);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```
线程常用的调度方法
![[Pasted image 20221010163530.png]]
**线程等待与通知**

在Object类中有一些函数可以用于线程的等待与通知。

-   wait()：当一个线程A调用一个共享变量的 wait()方法时， 线程A会被阻塞挂起， 发生下面几种情况才会返回 ：
    
    -   （1） 线程A调用了共享对象 notify()或者 notifyAll()方法；
        
    -   （2）其他线程调用了线程A的 interrupt() 方法，线程A抛出InterruptedException异常返回。
        
-   wait(long timeout) ：这个方法相比 wait() 方法多了一个超时参数，它的不同之处在于，如果线程A调用共享对象的wait(long timeout)方法后，没有在指定的 timeout ms时间内被其它线程唤醒，那么这个方法还是会因为超时而返回。
    
-   wait(long timeout, int nanos)，其内部调用的是 wait(long timout）函数。
    

上面是线程等待的方法，而唤醒线程主要是下面两个方法：

-   notify() : 一个线程A调用共享对象的 notify() 方法后，会唤醒一个在这个共享变量上调用 wait 系列方法后被挂起的线程。 一个共享变量上可能会有多个线程在等待，具体唤醒哪个等待的线程是随机的。
-   notifyAll() ：不同于在共享变量上调用 notify() 函数会唤醒被阻塞到该共享变量上的一个线程，notifyAll()方法则会唤醒所有在该共享变量上由于调用 wait 系列方法而被挂起的线程。

Thread类也提供了一个方法用于等待的方法：

-   join()：如果一个线程A执行了thread.join()语句，其含义是：当前线程A等待thread线程终止之后才
    
    从thread.join()返回。
    

**线程休眠**

-   sleep(long millis) :Thread类中的静态方法，当一个执行中的线程A调用了Thread 的sleep方法后，线程A会暂时让出指定时间的执行权，但是线程A所拥有的监视器资源，比如锁还是持有不让出的。指定的睡眠时间到了后该函数会正常返回，接着参与 CPU 的调度，获取到 CPU 资源后就可以继续运行。

**让出优先权**

-   yield() ：Thread类中的静态方法，当一个线程调用 yield 方法时，实际就是在暗示线程调度器当前线程请求让出自己的CPU ，但是线程调度器可以无条件忽略这个暗示。
yield()应该做的是让当前运行线程回到可运行状态，以允许具有相同优先级的其他线程获得运行机会。因此，使用yield()的目的是让相同优先级的线程之间能适当的轮转执行。但是，实际中无法保证yield()达到让步目的，因为让步的线程还有可能被线程调度程序再次选中。

**线程中断**

Java 中的线程中断是一种线程间的协作模式，通过设置线程的中断标志并不能直接终止该线程的执行，而是被中断的线程根据中断状态自行处理。

-   void interrupt() ：中断线程，例如，当线程A运行时，线程B可以调用线程interrupt() 方法来设置线程的中断标志为true 并立即返回。设置标志仅仅是设置标志, 线程A实际并没有被中断， 会继续往下执行。
-   boolean isInterrupted() 方法： 检测当前线程是否被中断。
-   boolean interrupted() 方法： 检测当前线程是否被中断，与 isInterrupted 不同的是，该方法如果发现当前线程被中断，则会清除中断标志。
### 线程有几种状态？
![[Pasted image 20221010171139.png]]
### 线程间的通信方式
![[Pasted image 20221010173422.png]]
-   **volatile和synchronized关键字**

关键字volatile可以用来修饰字段（成员变量），就是告知程序任何对该变量的访问均需要从共享内存中获取，而对它的改变必须同步刷新回共享内存，它能保证所有线程对变量访问的可见性。

关键字synchronized可以修饰方法或者以同步块的形式来进行使用，它主要确保多个线程在同一个时刻，只能有一个线程处于方法或者同步块中，它保证了线程对变量访问的可见性和排他性。

-   **等待/通知机制**

可以通过Java内置的等待/通知机制（wait()/notify()）实现一个线程修改一个对象的值，而另一个线程感知到了变化，然后进行相应的操作。

-   **管道输入/输出流**

管道输入/输出流和普通的文件输入/输出流或者网络输入/输出流不同之处在于，它主要用于线程之间的数据传输，而传输的媒介为内存。

管道输入/输出流主要包括了如下4种具体实现：PipedOutputStream、PipedInputStream、 PipedReader和PipedWriter，前两种面向字节，而后两种面向字符。

-   **使用Thread.join()**

如果一个线程A执行了thread.join()语句，其含义是：当前线程A等待thread线程终止之后才从thread.join()返回。。线程Thread除了提供join()方法之外，还提供了join(long millis)和join(long millis,int nanos)两个具备超时特性的方法。

-   **使用ThreadLocal**

ThreadLocal，即线程变量，是一个以ThreadLocal对象为键、任意对象为值的存储结构。这个结构被附带在线程上，也就是说一个线程可以根据一个ThreadLocal对象查询到绑定在这个线程上的一个值。

### 你在工作中用到过ThreadLocal吗？

有用到过的，用来做用户信息上下文的存储。

我们的系统应用是一个典型的MVC架构，登录后的用户每次访问接口，都会在请求头中携带一个token，在控制层可以根据这个token，解析出用户的基本信息。那么问题来了，假如在服务层和持久层都要用到用户信息，比如rpc调用、更新用户获取等等，那应该怎么办呢？

一种办法是显式定义用户相关的参数，比如账号、用户名……这样一来，我们可能需要大面积地修改代码，多少有点瓜皮，那该怎么办呢？

这时候我们就可以用到ThreadLocal，在控制层拦截请求把用户信息存入ThreadLocal，这样我们在任何一个地方，都可以取出ThreadLocal中存的用户数据。

![[Pasted image 20221010173713.png]]
  
很多其它场景的cookie、session等等数据隔离也都可以通过ThreadLocal去实现。

我们常用的数据库连接池也用到了ThreadLocal：

-   数据库连接池的连接交给ThreadLoca进行管理，保证当前线程的操作都是同一个Connnection。
### 父子线程怎么共享数据
父线程能用ThreadLocal来给子线程传值吗？毫无疑问，不能。那该怎么办？

这时候可以用到另外一个类——`InheritableThreadLocal` 。

使用起来很简单，在主线程的InheritableThreadLocal实例设置值，在子线程中就可以拿到了。
### 指令重排有限制吗？happens-before了解吗？
指令重排也是有一些限制的，有两个规则`happens-before`和`as-if-serial`来约束。

happens-before的定义：

-   如果一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。
-   两个操作之间存在happens-before关系，并不意味着Java平台的具体实现必须要按照 happens-before关系指定的顺序来执行。如果重排序之后的执行结果，与按happens-before关系来执行的结果一致，那么这种重排序并不非法

happens-before和我们息息相关的有六大规则：
![[Pasted image 20221010175045.png]]
-   **程序顺序规则**：一个线程中的每个操作，happens-before于该线程中的任意后续操作。
-   **监视器锁规则**：对一个锁的解锁，happens-before于随后对这个锁的加锁。
-   **volatile变量规则**：对一个volatile域的写，happens-before于任意后续对这个volatile域的读。
-   **传递性**：如果A happens-before B，且B happens-before C，那么A happens-before C。
-   **start()规则**：如果线程A执行操作ThreadB.start()（启动线程B），那么A线程的 ThreadB.start()操作happens-before于线程B中的任意操作。
-   **join()规则**：如果线程A执行操作ThreadB.join()并成功返回，那么线程B中的任意操作 happens-before于线程A从ThreadB.join()操作成功返回
### as-if-serial又是什么？单线程的程序一定是顺序的吗？
as-if-serial语义的意思是：不管怎么重排序（编译器和处理器为了提高并行度），**单线程程序的执行结果不能被改变**。编译器、runtime和处理器都必须遵守as-if-serial语义。

为了遵守as-if-serial语义，编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果。但是，如果操作之间不存在数据依赖关系，这些操作就可能被编译器和处理器重排序。为了具体说明，请看下面计算圆面积的代码示例。

```
double pi = 3.14;   // A
double r = 1.0;   // B 
double area = pi * r * r;   // C
```

上面3个操作的数据依赖关系：
![[Pasted image 20221010175121.png]]
A和C之间存在数据依赖关系，同时B和C之间也存在数据依赖关系。因此在最终执行的指令序列中，C不能被重排序到A和B的前面（C排到A和B的前面，程序的结果将会被改变）。但A和B之间没有数据依赖关系，编译器和处理器可以重排序A和B之间的执行顺序。

所以最终，程序可能会有两种执行顺序：
![[Pasted image 20221010175153.png]]
as-if-serial语义把单线程程序保护了起来，遵守as-if-serial语义的编译器、runtime和处理器共同编织了这么一个“楚门的世界”：单线程程序是按程序的“顺序”来执行的。as- if-serial语义使单线程情况下，我们不需要担心重排序的问题，可见性的问题。
### volatile实现原理了解吗？
volatile有两个作用，保证**可见性**和**有序性**。

> volatile怎么保证可见性的呢？

相比synchronized的加锁方式来解决共享变量的内存可见性问题，volatile就是更轻量的选择，它没有上下文切换的额外开销成本。

volatile可以确保对某个变量的更新对其他线程马上可见，一个变量被声明为volatile 时，线程在写入变量时不会把值缓存在寄存器或者其他地方，而是会把值刷新回主内存 当其它线程读取该共享变量 ，会从主内存重新获取最新值，而不是使用当前线程的本地内存中的值。

例如，我们声明一个 volatile 变量 volatile int x = 0，线程A修改x=1，修改完之后就会把新的值刷新回主内存，线程B读取x的时候，就会清空本地内存变量，然后再从主内存获取最新值。
![[Pasted image 20221010175225.png]]

> volatile怎么保证有序性的呢？

重排序可以分为编译器重排序和处理器重排序，valatile保证有序性，就是通过分别限制这两种类型的重排序。
![[Pasted image 20221010175318.png]]
为了实现volatile的内存语义，编译器在生成字节码时，会在指令序列中插入内存屏障来禁止特定类型的处理器重排序。

1.  在每个volatile写操作的前面插入一个`StoreStore`屏障
2.  在每个volatile写操作的后面插入一个`StoreLoad`屏障
3.  在每个volatile读操作的后面插入一个`LoadLoad`屏障
4.  在每个volatile读操作的后面插入一个`LoadStore`屏障
![[Pasted image 20221010175333.png]]

![[Pasted image 20221010175339.png]]
由于多线程下的读写如果顺序不一致会出现竞态（例如[[单例模式]]中使用volatile关键字修饰单例变量）
### synchronized的实现原理？
我们使用synchronized的时候，发现不用自己去lock和unlock，是因为JVM帮我们把这个事情做了。
	synchronized是怎么加锁的呢？
1.  synchronized修饰代码块时，JVM采用`monitorenter`、`monitorexit`两个指令来实现同步，`monitorenter` 指令指向同步代码块的开始位置， `monitorexit` 指令则指向同步代码块的结束位置。

  反编译一段synchronized修饰代码块代码，`javap -c -s -v -l SynchronizedDemo.class`，可以看到相应的字节码指令。
    ![[Pasted image 20221010223244.png]]
 2.  synchronized修饰同步方法时，JVM采用`ACC_SYNCHRONIZED`标记符来实现同步，这个标识指明了该方法是一个同步方法。

同样可以写段代码反编译看一下。
![[Pasted image 20221010223257.png]]
	synchronized锁住的是什么呢？

monitorenter、monitorexit或者ACC_SYNCHRONIZED都是**基于Monitor实现**的。

实例对象结构里有对象头，对象头里面有一块结构叫Mark Word，Mark Word指针指向了**monitor**。

所谓的Monitor其实是一种**同步工具**，也可以说是一种**同步机制**。在Java虚拟机（HotSpot）中，Monitor是由**ObjectMonitor实现**的，可以叫做内部锁，或者Monitor锁。

ObjectMonitor的工作原理：

-   ObjectMonitor有两个队列：_WaitSet、_EntryList，用来保存ObjectWaiter 对象列表。
-   _owner，获取 Monitor 对象的线程进入 _owner 区时， _count + 1。如果线程调用了 wait() 方法，此时会释放 Monitor 对象， _owner 恢复为空， _count - 1。同时该等待线程进入 _WaitSet 中，等待被唤醒。
### 除了原子性，synchronized可见性，有序性，可重入性怎么实现？
> synchronized怎么保证可见性？

-   线程加锁前，将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新读取最新的值。
-   线程加锁后，其它线程无法获取主内存中的共享变量。
-   线程解锁前，必须把共享变量的最新值刷新到主内存中。

> synchronized怎么保证有序性？

synchronized同步的代码块，具有排他性，一次只能被一个线程拥有，所以synchronized保证同一时刻，代码是单线程执行的。

因为as-if-serial语义的存在，单线程的程序能保证最终结果是有序的，但是不保证不会指令重排。

所以synchronized保证的有序是执行结果的有序性，而不是防止指令重排的有序性。

> synchronized怎么实现可重入的呢？

synchronized 是可重入锁，也就是说，允许一个线程二次请求自己持有对象锁的临界资源，这种情况称为可重入锁。

synchronized 锁对象的时候有个计数器，他会记录下线程获取锁的次数，在执行完对应的代码块之后，计数器就会-1，直到计数器清零，就释放锁了。

之所以，是可重入的。是因为 synchronized 锁对象有个计数器，会随着线程获取锁后 +1 计数，当线程执行完毕后 -1，直到清零释放锁。
### 锁升级？synchronized优化了解吗？
  
了解锁升级，得先知道，不同锁的状态是什么样的。这个状态指的是什么呢？

Java对象头里，有一块结构，叫`Mark Word`标记字段，这块结构会随着锁的状态变化而变化。

64 位虚拟机 Mark Word 是 64bit，我们来看看它的状态变化：
![[Pasted image 20221010223821.png]]
Mark Word存储对象自身的运行数据，如**哈希码、GC分代年龄、锁状态标志、偏向时间戳（Epoch）** 等。
	synchronized做了哪些优化？
在JDK1.6之前，synchronized的实现直接调用ObjectMonitor的enter和exit，这种锁被称之为**重量级锁**。从JDK6开始，HotSpot虚拟机开发团队对Java中的锁进行优化，如增加了适应性自旋、锁消除、锁粗化、轻量级锁和偏向锁等优化策略，提升了synchronized的性能。

-   偏向锁：在无竞争的情况下，只是在Mark Word里存储当前线程指针，CAS操作都不做。
    
-   轻量级锁：在没有多线程竞争时，相对重量级锁，减少操作系统互斥量带来的性能消耗。但是，如果存在锁竞争，除了互斥量本身开销，还额外有CAS操作的开销。
    
-   自旋锁：减少不必要的CPU上下文切换。在轻量级锁升级为重量级锁时，就使用了自旋加锁的方式
    
-   锁粗化：将多个连续的加锁、解锁操作连接在一起，扩展成一个范围更大的锁。
    
-   锁消除：虚拟机即时编译器在运行时，对一些代码上要求同步，但是被检测到不可能存在共享数据竞争的锁进行消除。
    

> 锁升级的过程是什么样的？

锁升级方向：无锁-->偏向锁---> 轻量级锁---->重量级锁，这个方向基本上是不可逆的。

![[Pasted image 20221010223847.png]]


我们看一下升级的过程：

#### 偏向锁：

**偏向锁的获取：**

1.  判断是否为可偏向状态--MarkWord中锁标志是否为‘01’，是否偏向锁是否为‘1’
2.  如果是可偏向状态，则查看线程ID是否为当前线程，如果是，则进入步骤'5'，否则进入步骤‘3’
3.  通过CAS操作竞争锁，如果竞争成功，则将MarkWord中线程ID设置为当前线程ID，然后执行‘5’；竞争失败，则执行‘4’
4.  CAS获取偏向锁失败表示有竞争。当达到safepoint时获得偏向锁的线程被挂起，**偏向锁升级为轻量级锁**，然后被阻塞在安全点的线程继续往下执行同步代码块
5.  执行同步代码

**偏向锁的撤销：**

1.  偏向锁不会主动释放(撤销)，只有遇到其他线程竞争时才会执行撤销，由于撤销需要知道当前持有该偏向锁的线程栈状态，因此要等到safepoint时执行，此时持有该偏向锁的线程（T）有‘2’，‘3’两种情况；
2.  撤销----T线程已经退出同步代码块，或者已经不再存活，则直接撤销偏向锁，变成无锁状态----该状态达到阈值20则执行批量重偏向
3.  升级----T线程还在同步代码块中，则将T线程的偏向锁**升级为轻量级锁**，当前线程执行轻量级锁状态下的锁获取步骤----该状态达到阈值40则执行批量撤销

#### 轻量级锁：

**轻量级锁的获取：**

1.  进行加锁操作时，jvm会判断是否已经时重量级锁，如果不是，则会在当前线程栈帧中划出一块空间，作为该锁的锁记录，并且将锁对象MarkWord复制到该锁记录中
2.  复制成功之后，jvm使用CAS操作将对象头MarkWord更新为指向锁记录的指针，并将锁记录里的owner指针指向对象头的MarkWord。如果成功，则执行‘3’，否则执行‘4’
3.  更新成功，则当前线程持有该对象锁，并且对象MarkWord锁标志设置为‘00’，即表示此对象处于轻量级锁状态
4.  更新失败，jvm先检查对象MarkWord是否指向当前线程栈帧中的锁记录，如果是则执行‘5’，否则执行‘4’
5.  表示锁重入；然后当前线程栈帧中增加一个锁记录第一部分（Displaced Mark Word）为null，并指向Mark Word的锁对象，起到一个重入计数器的作用。
6.  表示该锁对象已经被其他线程抢占，则进行**自旋等待**（默认10次），等待次数达到阈值仍未获取到锁，则**升级为重量级锁**

大体上省简的升级过程：
![[Pasted image 20221010223923.png]]

完整的升级过程：![[Pasted image 20221010223931.png]]

### 说说synchronized和ReentrantLock的区别？
### AQS了解多少？
### ReentrantLock怎么实现公平锁的？
### CAS呢？CAS了解多少？
### CAS 有什么问题？如何解决？
CAS的经典三大问题：
![[Pasted image 20221010182848.png]]
#### ABA 问题

并发环境下，假设初始条件是A，去修改数据时，发现是A就会执行修改。但是看到的虽然是A，中间可能发生了A变B，B又变回A的情况。此时A已经非彼A，数据即使成功修改，也可能有问题。

> 怎么解决ABA问题？

-   加版本号

每次修改变量，都在这个变量的版本号上加1，这样，刚刚A->B->A，虽然A的值没变，但是它的版本号已经变了，再判断版本号就会发现此时的A已经被改过了。参考乐观锁的版本号，这种做法可以给数据带上了一种实效性的检验。

Java提供了AtomicStampReference类，它的compareAndSet方法首先检查当前的对象引用值是否等于预期引用，并且当前印戳（Stamp）标志是否等于预期标志，如果全部相等，则以原子方式将引用值和印戳标志的值更新为给定的更新值。

#### 循环性能开销

自旋CAS，如果一直循环执行，一直不成功，会给CPU带来非常大的执行开销。

> 怎么解决循环性能开销问题？

在Java中，很多使用自旋CAS的地方，会有一个自旋次数的限制，超过一定次数，就停止自旋。

#### 只能保证一个变量的原子操作

CAS 保证的是对一个变量执行操作的原子性，如果对多个变量操作时，CAS 目前无法直接保证操作的原子性的。

> 怎么解决只能保证一个变量的原子操作问题？

-   可以考虑改用锁来保证操作的原子性
-   可以考虑合并多个变量，将多个变量封装成一个对象，通过AtomicReference来保证原子性。
### Java有哪些保证原子性的方法？如何保证多线程下i++ 结果正确？
![[Pasted image 20221010183110.png]]
### 原子操作类了解多少？
当程序更新一个变量时，如果多线程同时更新这个变量，可能得到期望之外的值，比如变量i=1，A线程更新i+1，B线程也更新i+1，经过两个线程操作之后可能i不等于3，而是等于2。因为A和B线程在更新变量i的时候拿到的i都是1，这就是线程不安全的更新操作，一般我们会使用synchronized来解决这个问题，synchronized会保证多线程不会同时更新变量i。

其实除此之外，还有更轻量级的选择，Java从JDK 1.5开始提供了java.util.concurrent.atomic包，这个包中的原子操作类提供了一种用法简单、性能高效、线程安全地更新一个变量的方式。

因为变量的类型有很多种，所以在Atomic包里一共提供了13个类，属于4种类型的原子更新方式，分别是原子更新基本类型、原子更新数组、原子更新引用和原子更新属性（字段）。

![[Pasted image 20221010183140.png]]
Atomic包里的类基本都是使用Unsafe实现的包装类。

使用原子的方式更新基本类型，Atomic包提供了以下3个类：

-   AtomicBoolean：原子更新布尔类型。
    
-   AtomicInteger：原子更新整型。
    
-   AtomicLong：原子更新长整型。
    

通过原子的方式更新数组里的某个元素，Atomic包提供了以下4个类：

-   AtomicIntegerArray：原子更新整型数组里的元素。
    
-   AtomicLongArray：原子更新长整型数组里的元素。
    
-   AtomicReferenceArray：原子更新引用类型数组里的元素。
    
-   AtomicIntegerArray类主要是提供原子的方式更新数组里的整型
    

原子更新基本类型的AtomicInteger，只能更新一个变量，如果要原子更新多个变量，就需要使用这个原子更新引用类型提供的类。Atomic包提供了以下3个类：

-   AtomicReference：原子更新引用类型。
    
-   AtomicReferenceFieldUpdater：原子更新引用类型里的字段。
    
-   AtomicMarkableReference：原子更新带有标记位的引用类型。可以原子更新一个布尔类型的标记位和引用类型。构造方法是AtomicMarkableReference（V initialRef，boolean initialMark）。
    

如果需原子地更新某个类里的某个字段时，就需要使用原子更新字段类，Atomic包提供了以下3个类进行原子字段更新：

-   AtomicIntegerFieldUpdater：原子更新整型的字段的更新器。
-   AtomicLongFieldUpdater：原子更新长整型字段的更新器。
-   AtomicStampedReference：原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于原子的更新数据和数据的版本号，可以解决使用CAS进行原子更新时可能出现的 ABA问题。
### AtomicInteger 的原理？
一句话概括：**使用CAS实现**。

以AtomicInteger的添加方法为例：

``` java 
    public final int getAndIncrement() {
        return unsafe.getAndAddInt(this, valueOffset, 1);
    }
```

通过`Unsafe`类的实例来进行添加操作，来看看具体的CAS操作：

``` java 
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}
```

compareAndSwapInt 是一个native方法，基于CAS来操作int类型变量。其它的原子操作类基本都是大同小异
### 线程死锁了解吗？该如何避免？
死锁是指两个或两个以上的线程在执行过程中，因争夺资源而造成的互相等待的现象，在无外力作用的情况下，这些线程会一直相互等待而无法继续运行下去。
![[Pasted image 20221010183352.png]]
那么为什么会产生死锁呢？ 死锁的产生必须具备以下四个条件：
![[Pasted image 20221010183402.png]]
-   互斥条件：指线程对己经获取到的资源进行它性使用，即该资源同时只由一个线程占用。如果此时还有其它线程请求获取获取该资源，则请求者只能等待，直至占有资源的线程释放该资源。
-   请求并持有条件：指一个 线程己经持有了至少一个资源，但又提出了新的资源请求，而新资源己被其它线程占有，所以当前线程会被阻塞，但阻塞 的同时并不释放自己已经获取的资源。
-   不可剥夺条件：指线程获取到的资源在自己使用完之前不能被其它线程抢占，只有在自己使用完毕后才由自己释放该资源。
-   环路等待条件：指在发生死锁时，必然存在一个线程——资源的环形链，即线程集合 {T0，T1，T2,…… ，Tn} 中 T0 正在等待一 T1 占用的资源，Tl1正在等待 T2用的资源，…… Tn 在等待己被 T0占用的资源。

该如何避免死锁呢？答案是**至少破坏死锁发生的一个条件**。

-   其中，互斥这个条件我们没有办法破坏，因为用锁为的就是互斥。不过其他三个条件都是有办法破坏掉的，到底如何做呢？
    
-   对于“请求并持有”这个条件，可以一次性请求所有的资源。
    
-   对于“不可剥夺”这个条件，占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源，这样不可抢占这个条件就破坏掉了。
    
-   对于“环路等待”这个条件，可以靠按序申请资源来预防。所谓按序申请，是指资源是有线性顺序的，申请的时候可以先申请资源序号小的，再申请资源序号大的，这样线性化后就不存在环路了。
### 那死锁问题怎么排查呢？
可以使用jdk自带的命令行工具排查：

1.  使用jps查找运行的Java进程：jps -l
2.  使用jstack查看线程堆栈信息：jstack -l 进程id

基本就可以看到死锁的信息。

还可以利用图形化工具，比如JConsole。出现线程死锁以后，点击JConsole线程面板的`检测到死锁`按钮，将会看到线程的死锁信息。
### CountDownLatch（倒计数器）了解吗？
CountDownLatch，倒计数器，有两个常见的应用场景[18]：

**场景1：协调子线程结束动作：等待所有子线程运行结束**

CountDownLatch允许一个或多个线程等待其他线程完成操作。

例如，我们很多人喜欢玩的王者荣耀，开黑的时候，得等所有人都上线之后，才能开打。

![[Pasted image 20221010183720.png]]
CountDownLatch模仿这个场景(参考[18])：

创建大乔、兰陵王、安其拉、哪吒和铠等五个玩家，主线程必须在他们都完成确认后，才可以继续运行。

在这段代码中，`new CountDownLatch(5)`用户创建初始的latch数量，各玩家通过`countDownLatch.countDown()`完成状态确认，主线程通过`countDownLatch.await()`等待。

``` java 
public static void main(String[] args) throws InterruptedException {
    CountDownLatch countDownLatch = new CountDownLatch(5);

    Thread 大乔 = new Thread(countDownLatch::countDown);
    Thread 兰陵王 = new Thread(countDownLatch::countDown);
    Thread 安其拉 = new Thread(countDownLatch::countDown);
    Thread 哪吒 = new Thread(countDownLatch::countDown);
    Thread 铠 = new Thread(() -> {
        try {
            // 稍等，上个卫生间，马上到...
            Thread.sleep(1500);
            countDownLatch.countDown();
        } catch (InterruptedException ignored) {}
    });

    大乔.start();
    兰陵王.start();
    安其拉.start();
    哪吒.start();
    铠.start();
    countDownLatch.await();
    System.out.println("所有玩家已经就位！");
}
```

**场景2. 协调子线程开始动作：统一各线程动作开始的时机**

王者游戏中也有类似的场景，游戏开始时，各玩家的初始状态必须一致。不能有的玩家都出完装了，有的才降生。

所以大家得一块出生，在这个场景中，仍然用五个线程代表大乔、兰陵王、安其拉、哪吒和铠等五个玩家。需要注意的是，各玩家虽然都调用了`start()`线程，但是它们在运行时都在等待`countDownLatch`的信号，在信号未收到前，它们不会往下执行。
``` java 
public static void main(String[] args) throws InterruptedException {
    CountDownLatch countDownLatch = new CountDownLatch(1);

    Thread 大乔 = new Thread(() -> waitToFight(countDownLatch));
    Thread 兰陵王 = new Thread(() -> waitToFight(countDownLatch));
    Thread 安其拉 = new Thread(() -> waitToFight(countDownLatch));
    Thread 哪吒 = new Thread(() -> waitToFight(countDownLatch));
    Thread 铠 = new Thread(() -> waitToFight(countDownLatch));

    大乔.start();
    兰陵王.start();
    安其拉.start();
    哪吒.start();
    铠.start();
    Thread.sleep(1000);
    countDownLatch.countDown();
    System.out.println("敌方还有5秒达到战场，全军出击！");
}

private static void waitToFight(CountDownLatch countDownLatch) {
    try {
        countDownLatch.await(); // 在此等待信号再继续
        System.out.println("收到，发起进攻！");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```
CountDownLatch的**核心方法**也不多：

-   `await()`：等待latch降为0；
-   `boolean await(long timeout, TimeUnit unit)`：等待latch降为0，但是可以设置超时时间。比如有玩家超时未确认，那就重新匹配，总不能为了某个玩家等到天荒地老。
-   `countDown()`：latch数量减1；
-   `getCount()`：获取当前的latch数量。
### CyclicBarrier（同步屏障）了解吗？
CyclicBarrier的字面意思是可循环使用（Cyclic）的屏障（Barrier）。它要做的事情是，让一 组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续运行。

它和CountDownLatch类似，都可以协调多线程的结束动作，在它们结束后都可以执行特定动作，但是为什么要有CyclicBarrier，自然是它有和CountDownLatch不同的地方。

不知道你听没听过一个新人UP主小约翰可汗，小约翰生平有两大恨——“想结衣结衣不依,迷爱理爱理不理。”我们来还原一下事情的经过：小约翰在亲政后认识了新垣结衣，于是决定第一次选妃，向结衣表白，等待回应。然而新垣结衣回应嫁给了星野源，小约翰伤心欲绝，发誓生平不娶，突然发现了铃木爱理，于是小约翰决定第二次选妃，求爱理搭理，等待回应。

![[Pasted image 20221010183850.png]]
我们拿代码模拟这一场景，发现CountDownLatch无能为力了，因为CountDownLatch的使用是一次性的，无法重复利用，而这里等待了两次。此时，我们用CyclicBarrier就可以实现，因为它可以重复利用。
![[Pasted image 20221010183904.png]]
### Semaphore（信号量）了解吗？
Semaphore（信号量）是用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源。

听起来似乎很抽象，现在汽车多了，开车出门在外的一个老大难问题就是停车 。停车场的车位是有限的，只能允许若干车辆停泊，如果停车场还有空位，那么显示牌显示的就是绿灯和剩余的车位，车辆就可以驶入；如果停车场没位了，那么显示牌显示的就是绿灯和数字0，车辆就得等待。如果满了的停车场有车离开，那么显示牌就又变绿，显示空车位数量，等待的车辆就能进停车场。
![[Pasted image 20221010183949.png]]
我们把这个例子类比一下，车辆就是线程，进入停车场就是线程在执行，离开停车场就是线程执行完毕，看见红灯就表示线程被阻塞，不能执行，Semaphore的本质就是**协调多个线程对共享资源的获取**。
![[Pasted image 20221010183958.png]]
我们再来看一个Semaphore的用途：它可以用于做流量控制，特别是公用资源有限的应用场景，比如数据库连接。

假如有一个需求，要读取几万个文件的数据，因为都是IO密集型任务，我们可以启动几十个线程并发地读取，但是如果读到内存后，还需要存储到数据库中，而数据库的连接数只有10个，这时我们必须控制只有10个线程同时获取数据库连接保存数据，否则会报错无法获取数据库连接。这个时候，就可以使用Semaphore来做流量控制，如下：

``` java 
public class SemaphoreTest {
    private static final int THREAD_COUNT = 30;
    private static ExecutorService threadPool = Executors.newFixedThreadPool(THREAD_COUNT);
    private static Semaphore s = new Semaphore(10);

    public static void main(String[] args) {
        for (int i = 0; i < THREAD_COUNT; i++) {
            threadPool.execute(new Runnable() {
                @Override
                public void run() {
                    try {
                        s.acquire();
                        System.out.println("save data");
                        s.release();
                    } catch (InterruptedException e) {
                    }
                }
            });
        }
        threadPool.shutdown();
    }
}
```
在代码中，虽然有30个线程在执行，但是只允许10个并发执行。Semaphore的构造方法 `Semaphore（int permits`）接受一个整型的数字，表示可用的许可证数量。`Semaphore（10）`表示允许10个线程获取许可证，也就是最大并发数是10。Semaphore的用法也很简单，首先线程使用 Semaphore的acquire()方法获取一个许可证，使用完之后调用release()方法归还许可证。还可以用tryAcquire()方法尝试获取许可证。

### Exchanger 了解吗？
Exchanger（交换者）是一个用于线程间协作的工具类。Exchanger用于进行线程间的数据交换。它提供一个同步点，在这个同步点，两个线程可以交换彼此的数据。
![[Pasted image 20221010184042.png]]
这两个线程通过 exchange方法交换数据，如果第一个线程先执行exchange()方法，它会一直等待第二个线程也执行exchange方法，当两个线程都到达同步点时，这两个线程就可以交换数据，将本线程生产出来的数据传递给对方。

Exchanger可以用于遗传算法，遗传算法里需要选出两个人作为交配对象，这时候会交换两人的数据，并使用交叉规则得出2个交配结果。Exchanger也可以用于校对工作，比如我们需要将纸制银行流水通过人工的方式录入成电子银行流水，为了避免错误，采用AB岗两人进行录入，录入到Excel之后，系统需要加载这两个Excel，并对两个Excel数据进行校对，看看是否录入一致。

``` java 
public class ExchangerTest {
    private static final Exchanger<String> exgr = new Exchanger<String>();
    private static ExecutorService threadPool = Executors.newFixedThreadPool(2);

    public static void main(String[] args) {
        threadPool.execute(new Runnable() {
            @Override
            public void run() {
                try {
                    String A = "银行流水A"; // A录入银行流水数据 
                    exgr.exchange(A);
                } catch (InterruptedException e) {
                }
            }
        });
        threadPool.execute(new Runnable() {
            @Override
            public void run() {
                try {
                    String B = "银行流水B"; // B录入银行流水数据 
                    String A = exgr.exchange("B");
                    System.out.println("A和B数据是否一致：" + A.equals(B) + "，A录入的是："
                            + A + "，B录入是：" + B);
                } catch (InterruptedException e) {
                }
            }
        });
        threadPool.shutdown();
    }
}
```

假如两个线程有一个没有执行exchange()方法，则会一直等待，如果担心有特殊情况发生，避免一直等待，可以使用`exchange(V x, long timeOut, TimeUnit unit)` 设置最大等待时长。

### 什么是线程池？
**线程池：** 简单理解，它就是一个管理线程的池子。
![[Pasted image 20221010184209.png]]
-   **它帮我们管理线程，避免增加创建线程和销毁线程的资源损耗**。因为线程其实也是一个对象，创建一个对象，需要经过类加载过程，销毁一个对象，需要走GC垃圾回收流程，都是需要资源开销的。
-   **提高响应速度。** 如果任务到达了，相对于从线程池拿线程，重新去创建一条线程执行，速度肯定慢很多。
-   **重复利用。** 线程用完，再放回池子，可以达到重复利用的效果，节省资源。
### 能说说工作中线程池的应用吗？
之前我们有一个和第三方对接的需求，需要向第三方推送数据，引入了多线程来提升数据推送的效率，其中用到了线程池来管理线程。
![[Pasted image 20221010184314.png]]
主要代码如下：
![[Pasted image 20221010184514.png]]
完整可运行代码地址：https://gitee.com/fighter3/thread-demo.git

线程池的参数如下：

-   corePoolSize：线程核心参数选择了CPU数×2
    
-   maximumPoolSize：最大线程数选择了和核心线程数相同
    
-   keepAliveTime：非核心闲置线程存活时间直接置为0
    
-   unit：非核心线程保持存活的时间选择了 TimeUnit.SECONDS 秒
    
-   workQueue：线程池等待队列，使用 LinkedBlockingQueue阻塞队列
    

同时还用了synchronized 来加锁，保证数据不会被重复推送：

```
  synchronized (PushProcessServiceImpl.class) {}
```

ps:这个例子只是简单地进行了数据推送，实际上还可以结合其他的业务，像什么数据清洗啊、数据统计啊，都可以套用。

### 线程池主要参数有哪些？
![[Pasted image 20221010184906.png]]
线程池有七大参数，需要重点关注`corePoolSize`、`maximumPoolSize`、`workQueue`、`handler`这四个。

1.  corePoolSize

此值是用来初始化线程池中核心线程数，当线程池中线程池数< `corePoolSize`时，系统默认是添加一个任务才创建一个线程池。当线程数 = corePoolSize时，新任务会追加到workQueue中。

2.  maximumPoolSize

`maximumPoolSize`表示允许的最大线程数 = (非核心线程数+核心线程数)，当`BlockingQueue`也满了，但线程池中总线程数 < `maximumPoolSize`时候就会再次创建新的线程。

3.  keepAliveTime

非核心线程 =(maximumPoolSize - corePoolSize ) ,非核心线程闲置下来不干活最多存活时间。

4.  unit

线程池中非核心线程保持存活的时间的单位

-   TimeUnit.DAYS; 天
-   TimeUnit.HOURS; 小时
-   TimeUnit.MINUTES; 分钟
-   TimeUnit.SECONDS; 秒
-   TimeUnit.MILLISECONDS; 毫秒
-   TimeUnit.MICROSECONDS; 微秒
-   TimeUnit.NANOSECONDS; 纳秒

5.  workQueue

线程池等待队列，维护着等待执行的`Runnable`对象。当运行当线程数= corePoolSize时，新的任务会被添加到`workQueue`中，如果`workQueue`也满了则尝试用非核心线程执行任务，等待队列应该尽量用有界的。

6.  threadFactory

创建一个新线程时使用的工厂，可以用来设定线程名、是否为daemon线程等等。

7.  handler

`corePoolSize`、`workQueue`、`maximumPoolSize`都不可用的时候执行的饱和策略。

### 线程池的拒绝策略有哪些？
![[Pasted image 20221010185012.png]]
-   AbortPolicy ：直接抛出异常，默认使用此策略
-   CallerRunsPolicy：用调用者所在的线程来执行任务
-   DiscardOldestPolicy：丢弃阻塞队列里最老的任务，也就是队列里靠前的任务
-   DiscardPolicy ：当前任务直接丢弃
想实现自己的拒绝策略，实现RejectedExecutionHandler接口即可。

### 线程池有哪几种工作队列？
常用的阻塞队列主要有以下几种：
![[Pasted image 20221010191418.png]]
-   ArrayBlockingQueue：ArrayBlockingQueue（有界队列）是一个用数组实现的有界阻塞队列，按FIFO排序量。
-   LinkedBlockingQueue：LinkedBlockingQueue（可设置容量队列）是基于链表结构的阻塞队列，按FIFO排序任务，容量可以选择进行设置，不设置的话，将是一个无边界的阻塞队列，最大长度为Integer.MAX_VALUE，吞吐量通常要高于ArrayBlockingQuene；newFixedThreadPool线程池使用了这个队列
-   DelayQueue：DelayQueue（延迟队列）是一个任务定时周期的延迟执行的队列。根据指定的执行时间从小到大排序，否则根据插入到队列的先后排序。newScheduledThreadPool线程池使用了这个队列。
-   PriorityBlockingQueue：PriorityBlockingQueue（优先级队列）是具有优先级的无界阻塞队列
-   SynchronousQueue：SynchronousQueue（同步队列）是一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQuene，newCachedThreadPool线程池使用了这个队列。
### 线程池提交execute和submit有什么区别？
1.  execute 用于提交不需要返回值的任务

``` java
threadsPool.execute(new Runnable() { 
    @Override public void run() { 
        // TODO Auto-generated method stub } 
    });
```

2.  submit()方法用于提交需要返回值的任务。线程池会返回一个future类型的对象，通过这个 future对象可以判断任务是否执行成功，并且可以通过future的get()方法来获取返回值

``` java
Future<Object> future = executor.submit(harReturnValuetask); 
try { Object s = future.get(); } catch (InterruptedException e) { 
    // 处理中断异常 
} catch (ExecutionException e) { 
    // 处理无法执行任务异常 
} finally { 
    // 关闭线程池 executor.shutdown();
}
```
### 线程池怎么关闭知道吗？
可以通过调用线程池的`shutdown`或`shutdownNow`方法来关闭线程池。它们的原理是遍历线程池中的工作线程，然后逐个调用线程的interrupt方法来中断线程，所以无法响应中断的任务可能永远无法终止。

**shutdown() 将线程池状态置为shutdown,并不会立即停止**：

1.  停止接收外部submit的任务
2.  内部正在跑的任务和队列里等待的任务，会执行完
3.  等到第二步完成后，才真正停止

**shutdownNow() 将线程池状态置为stop。一般会立即停止，事实上不一定**：

1.  和shutdown()一样，先停止接收外部提交的任务
2.  忽略队列里等待的任务
3.  尝试将正在跑的任务interrupt中断
4.  返回未执行的任务列表

shutdown 和shutdownnow简单来说区别如下：

-   shutdownNow()能立即停止线程池，正在跑的和正在等待的任务都停下了。这样做立即生效，但是风险也比较大。
-   shutdown()只是关闭了提交通道，用submit()是无效的；而内部的任务该怎么跑还是怎么跑，跑完再彻底停止线程池。
### 线程池的线程数应该怎么配置？
线程在Java中属于稀缺资源，线程池不是越大越好也不是越小越好。任务分为计算密集型、IO密集型、混合型。

1.  计算密集型：大部分都在用CPU跟内存，加密，逻辑操作业务处理等。
2.  IO密集型：数据库链接，网络通讯传输等。
![[Pasted image 20221010191752.png]]
一般的经验，不同类型线程池的参数配置：

1.  计算密集型一般推荐线程池不要过大，一般是CPU数 + 1，+1是因为可能存在**页缺失**(就是可能存在有些数据在硬盘中需要多来一个线程将数据读入内存)。如果线程池数太大，可能会频繁的 进行线程上下文切换跟任务调度。获得当前CPU核心数代码如下：

```
Runtime.getRuntime().availableProcessors();
```

2.  IO密集型：线程数适当大一点，机器的Cpu核心数*2。
3.  混合型：可以考虑根绝情况将它拆分成CPU密集型和IO密集型任务，如果执行时间相差不大，拆分可以提升吞吐量，反之没有必要。

当然，实际应用中没有固定的公式，需要结合测试和监控来进行调整。
### 有哪几种常见的线程池？
面试常问，主要有四种，都是通过工具类Excutors创建出来的，需要注意，阿里巴巴《Java开发手册》里禁止使用这种方式来创建线程池。
![[Pasted image 20221010191856.png]]
### 能说一下四种常见线程池的原理吗？
前三种线程池的构造直接调用ThreadPoolExecutor的构造方法。

#### newSingleThreadExecutor

```
public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>(),
                                threadFactory));
}
```

**线程池特点**

-   核心线程数为1
-   最大线程数也为1
-   阻塞队列是无界队列LinkedBlockingQueue，可能会导致OOM
-   keepAliveTime为0

![SingleThreadExecutor运行流程](http://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javathread-72.png)

工作流程：

-   提交任务
-   线程池是否有一条线程在，如果没有，新建线程执行任务
-   如果有，将任务加到阻塞队列
-   当前的唯一线程，从队列取任务，执行完一个，再继续取，一个线程执行任务。

**适用场景**

适用于串行执行任务的场景，一个任务一个任务地执行。

#### newFixedThreadPool

```
public static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>(),
                                  threadFactory);
}
```

**线程池特点：**

-   核心线程数和最大线程数大小一样
-   没有所谓的非空闲时间，即keepAliveTime为0
-   阻塞队列为无界队列LinkedBlockingQueue，可能会导致OOM

![FixedThreadPool](http://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javathread-73.png)

工作流程：

-   提交任务
-   如果线程数少于核心线程，创建核心线程执行任务
-   如果线程数等于核心线程，把任务添加到LinkedBlockingQueue阻塞队列
-   如果线程执行完任务，去阻塞队列取任务，继续执行。

**使用场景**

FixedThreadPool 适用于处理CPU密集型的任务，确保CPU在长期被工作线程使用的情况下，尽可能的少的分配线程，即适用执行长期的任务。

#### newCachedThreadPool

```
public static ExecutorService newCachedThreadPool(ThreadFactory threadFactory) {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>(),
                                  threadFactory);
}
```

**线程池特点：**

-   核心线程数为0
-   最大线程数为Integer.MAX_VALUE，即无限大，可能会因为无限创建线程，导致OOM
-   阻塞队列是SynchronousQueue
-   非核心线程空闲存活时间为60秒

当提交任务的速度大于处理任务的速度时，每次提交一个任务，就必然会创建一个线程。极端情况下会创建过多的线程，耗尽 CPU 和内存资源。由于空闲 60 秒的线程会被终止，长时间保持空闲的 CachedThreadPool 不会占用任何资源。

![CachedThreadPool执行流程](http://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javathread-74.png)

工作流程：

-   提交任务
-   因为没有核心线程，所以任务直接加到SynchronousQueue队列。
-   判断是否有空闲线程，如果有，就去取出任务执行。
-   如果没有空闲线程，就新建一个线程执行。
-   执行完任务的线程，还可以存活60秒，如果在这期间，接到任务，可以继续活下去；否则，被销毁。

**适用场景**

用于并发执行大量短期的小任务。

#### newScheduledThreadPool

``` java
public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
          new DelayedWorkQueue());
}
```

**线程池特点**

-   最大线程数为Integer.MAX_VALUE，也有OOM的风险
-   阻塞队列是DelayedWorkQueue
-   keepAliveTime为0
-   scheduleAtFixedRate() ：按某种速率周期执行
-   scheduleWithFixedDelay()：在某个延迟后执行

![ScheduledThreadPool执行流程](http://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javathread-75.png)

**工作机制**

-   线程从DelayQueue中获取已到期的ScheduledFutureTask（DelayQueue.take()）。到期任务是指ScheduledFutureTask的time大于等于当前时间。
-   线程执行这个ScheduledFutureTask。
-   线程修改ScheduledFutureTask的time变量为下次将要被执行的时间。
-   线程把这个修改time之后的ScheduledFutureTask放回DelayQueue中（DelayQueue.add()）。

![ScheduledThreadPoolExecutor执行流程](http://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javathread-76.png)

**使用场景**

周期性执行任务的场景，需要限制线程数量的场景

> 使用无界队列的线程池会导致什么问题吗？

例如newFixedThreadPool使用了无界的阻塞队列LinkedBlockingQueue，如果线程获取一个任务后，任务的执行时间比较长，会导致队列的任务越积越多，导致机器内存使用不停飙升，最终导致OOM。

### 线程池异常怎么处理知道吗？

在使用线程池处理任务的时候，任务代码可能抛出RuntimeException，抛出异常后，线程池可能捕获它，也可能创建一个新的线程来代替异常的线程，我们可能无法感知任务出现了异常，因此我们需要考虑线程池异常情况。

常见的异常处理方式：
![[Pasted image 20221010192044.png]]
线程池有这几个状态：RUNNING,SHUTDOWN,STOP,TIDYING,TERMINATED。

```
//线程池状态
private static final int RUNNING    = -1 << COUNT_BITS;
private static final int SHUTDOWN   =  0 << COUNT_BITS;
private static final int STOP       =  1 << COUNT_BITS;
private static final int TIDYING    =  2 << COUNT_BITS;
private static final int TERMINATED =  3 << COUNT_BITS;
```

线程池各个状态切换图：
![[Pasted image 20221010192107.png]]
**RUNNING**

-   该状态的线程池会接收新任务，并处理阻塞队列中的任务;
-   调用线程池的shutdown()方法，可以切换到SHUTDOWN状态;
-   调用线程池的shutdownNow()方法，可以切换到STOP状态;

**SHUTDOWN**

-   该状态的线程池不会接收新任务，但会处理阻塞队列中的任务；
-   队列为空，并且线程池中执行的任务也为空,进入TIDYING状态;

**STOP**

-   该状态的线程不会接收新任务，也不会处理阻塞队列中的任务，而且会中断正在运行的任务；
-   线程池中执行的任务为空,进入TIDYING状态;

**TIDYING**

-   该状态表明所有的任务已经运行终止，记录的任务数量为0。
-   terminated()执行完毕，进入TERMINATED状态

**TERMINATED**

-   该状态表示线程池彻底终止
### 线程池如何实现参数的动态修改？
线程池提供了几个 setter方法来设置线程池的参数。
![[Pasted image 20221010192143.png]]
这里主要有两个思路：
![[Pasted image 20221010192153.png]]
-   在我们微服务的架构下，可以利用配置中心如Nacos、Apollo等等，也可以自己开发配置中心。业务服务读取线程池配置，获取相应的线程池实例来修改线程池的参数。
    
-   如果限制了配置中心的使用，也可以自己去扩展**ThreadPoolExecutor**，重写方法，监听线程池参数变化，来动态修改线程池参数。
### 线程池调优了解吗？

线程池配置没有固定的公式，通常事前会对线程池进行一定评估，常见的评估方案如下：
![[Pasted image 20221010192217.png]]
上线之前也要进行充分的测试，上线之后要建立完善的线程池监控机制。

事中结合监控告警机制，分析线程池的问题，或者可优化点，结合线程池动态参数配置机制来调整配置。

事后要注意仔细观察，随时调整。
![[Pasted image 20221010192227.png]]
具体的调优案例可以查看参考[7]美团技术博客

### 你能设计实现一个线程池吗？
线程池实现原理可以查看 [要是以前有人这么讲线程池，我早就该明白了！open in new window](https://mp.weixin.qq.com/s/Exy7pRGND9TCjRd9TZK4jg) ，当然，我们自己实现， 只需要抓住线程池的核心流程-参考[6]：
![[Pasted image 20221010192337.png]]
我们自己的实现就是完成这个核心流程：

-   线程池中有N个工作线程
-   把任务提交给线程池运行
-   如果线程池已满，把任务放入队列
-   最后当有空闲时，获取队列中任务来执行

实现代码[6]：
![[Pasted image 20221010192354.png]]
这样，一个实现了线程池主要流程的类就完成了。
### 单机线程池执行断电了应该怎么处理？
  
我们可以对正在处理和阻塞队列的任务做事务管理或者对阻塞队列中的任务持久化处理，并且当断电或者系统崩溃，操作无法继续下去的时候，可以通过回溯日志的方式来撤销`正在处理`的已经执行成功的操作。然后重新执行整个阻塞队列。

也就是说，对阻塞队列持久化；正在处理任务事务控制；断电之后正在处理任务的回滚，通过日志恢复该次操作；服务器重启后阻塞队列中的数据再加载。
### Fork/Join框架了解吗？
Fork/Join框架是Java7提供的一个用于并行执行任务的框架，是一个把大任务分割成若干个小任务，最终汇总每个小任务结果后得到大任务结果的框架。

要想掌握Fork/Join框架，首先需要理解两个点，**分而治之**和**工作窃取算法**。

**分而治之**

Fork/Join框架的定义，其实就体现了分治思想：将一个规模为N的问题分解为K个规模较小的子问题，这些子问题相互独立且与原问题性质相同。求出子问题的解，就可得到原问题的解。
![[Pasted image 20221010192730.png]]
**工作窃取算法**

大任务拆成了若干个小任务，把这些小任务放到不同的队列里，各自创建单独线程来执行队列里的任务。

那么问题来了，有的线程干活块，有的线程干活慢。干完活的线程不能让它空下来，得让它去帮没干完活的线程干活。它去其它线程的队列里窃取一个任务来执行，这就是所谓的**工作窃取**。

工作窃取发生的时候，它们会访问同一个队列，为了减少窃取任务线程和被窃取任务线程之间的竞争，通常任务会使用双端队列，被窃取任务线程永远从双端队列的头部拿，而窃取任务的线程永远从双端队列的尾部拿任务执行。
![[Pasted image 20221010192741.png]]
看一个Fork/Join框架应用的例子，计算1~n之间的和：1+2+3+…+n

-   设置一个分割阈值，任务大于阈值就拆分任务
-   任务有结果，所以需要继承RecursiveTask
``` java
public class CountTask extends RecursiveTask<Integer> {
    private static final int THRESHOLD = 16; // 阈值
    private int start;
    private int end;

    public CountTask(int start, int end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        int sum = 0;
        // 如果任务足够小就计算任务
        boolean canCompute = (end - start) <= THRESHOLD;
        if (canCompute) {
            for (int i = start; i <= end; i++) {
                sum += i;
            }
        } else {
            // 如果任务大于阈值，就分裂成两个子任务计算
            int middle = (start + end) / 2;
            CountTask leftTask = new CountTask(start, middle);
            CountTask rightTask = new CountTask(middle + 1, end);
            // 执行子任务
            leftTask.fork();
            rightTask.fork(); // 等待子任务执行完，并得到其结果
            int leftResult = leftTask.join();
            int rightResult = rightTask.join(); // 合并子任务
            sum = leftResult + rightResult;
        }
        return sum;
    }

    public static void main(String[] args) {
        ForkJoinPool forkJoinPool = new ForkJoinPool(); // 生成一个计算任务，负责计算1+2+3+4
        CountTask task = new CountTask(1, 100); // 执行一个任务
        Future<Integer> result = forkJoinPool.submit(task);
        try {
            System.out.println(result.get());
        } catch (InterruptedException e) {
        } catch (ExecutionException e) {
        }
    }
    
}
```
ForkJoinTask与一般Task的主要区别在于它需要实现compute方法，在这个方法里，首先需要判断任务是否足够小，如果足够小就直接执行任务。如果比较大，就必须分割成两个子任务，每个子任务在调用fork方法时，又会进compute方法，看看当前子任务是否需要继续分割成子任务，如果不需要继续分割，则执行当前子任务并返回结果。使用join方法会等待子任务执行完并得到其结果。