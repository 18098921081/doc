####阿里面试场景 
#### 1、线程池
* 线程池的实现原理，这个知识点真的很重要，几乎每次面试都会被问到，一般的提问方式有如下几种：

1、“讲讲线程池的实现原理”

2、“线程池中的coreNum和maxNum有什么不同”

3、“在不同的业务场景中，线程池参数如何设置”
面试官：平时线程池用的多么？

我：嗯，我的*项目中就用到了。

面试官：那好，你讲讲线程池的实现原理  
|首先要对线程池的参数详细了解:  
|ThreadPoolExecutor(int corePoolSize,  //核心线程数,当线程池新建线程时，如果线程数<corePoolSize,则新建核心线程,否则新建非核心线程；该核心线程即时空闲也不会kill掉;     
|int maximumPoolSize,  //线程池中最大线程数   非核心线程数+核心线程数    
|long keepAliveTime,   //非核心线程的闲置超时时间  
|TimeUnit unit,      //时间单位  
|BlockingQueue<Runnable> workQueue,  //线程池的任务队列 当所有核心线程都在工作时，新建的任务就进入该任务队列中，如果任务队列满了，则新建非核心线程去执行任务  
|ThreadFactory threadFactory,      // 创建线程的方式，这是一个接口   
|RejectedExecutionHandler handler)//线程池的拒绝策略  

我：能给我笔和纸么，我画图分析给你看看，假设初始化一个线程池，核心线程数是5，最大线程数是10 ,在纸上画了正方形，这个代表一个线程池，初始化的时候，里面是没有线程的;  
我：又画了一个细长的长方形，这个代表阻塞队列，一开始里面也是没有任务的。当来了一个任务时，在正方形中画了一个小圆圈，代表初始化了一个线程，如果再来一个任务，就再画一个圆圈，表示再初始化了一个线程，连续画了5个圆圈之后，如果第6个任务过来了…

面试官：嗯，好的，你继续…

我：这时会把第6个任务放到阻塞队列中..
|阻塞队列:  
|1,SynchronousQueue,该队列在接收任务时直接将任务提交给线程处理，而不保留任务，如果所有线程都在工作，那么久新建线程来处理。为了不出现<线程数达到了maximumPoolSize而不能新建线程>的错误；使用这个类型队列的时候，maximumPoolSize一般指定成Integer.MAX_VALUE，即无限大；  
|2, LinkedBlockingQueue：这个队列接收到任务的时候，如果当前线程数小于核心线程数，则新建线程(核心线程)处理任务；如果当前线程数等于核心线程数，则进入队列等待。由于这个队列没有最大值限制，即所有超过核心线程数的任务都将被添加到队列中，这也就导致了maximumPoolSize的设定失效，因为总线程数永远不会超过corePoolSize;  
|3,ArrayBlockingQueue:可以限定队列的长度，接收到任务的时候，如果没有达到corePoolSize的值，则新建线程(核心线程)执行任务，如果达到了，则入队等候，如果队列已满，则新建线程(非核心线程)执行任务，又如果总线程数到了maximumPoolSize，并且队列也满了，则发生错误
|4,DelayQueue：队列内元素必须实现Delayed接口，这就意味着你传进去的任务必须先实现Delayed接口。这个队列接收到任务时，首先先入队，只有达到了指定的延时时间，才会执行任务;  

面试官：嗯，然后呢？

我：现在线程池中不是有5个线程了么，如果其中一个线程空闲了，就会从阻塞队列中获取第6个任务，进行执行..

面试官：嗯，对的，那如果任务产生的速度比消费的速度快呢？

我：如果线程池的5个线程都在running状态，那么任务就先保存在阻塞队列中

面试官：如果队列满了，怎么办？

我：如果队列满了，我们不是设置了最大线程数是10么，而线程池中只有5个线程，这时会新建一个线程去执行不能保存到阻塞队列的任务，然后我又在正方形中画了5个圆圈。

面试官：那如果线程池中的线程数达到10个了，阻塞队列也满了，怎么办？

我：这种情况通过自定义reject函数(拒绝策略)去处理这里任务了，舒了一口去，以为问完了,  
|线程池的拒绝策略:  1.ThreadPoolExecutor.AbortPolicy 丢弃任务并抛出RejectedExecutionException异常  
|ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
|ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
|ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务

面试官：好的，那如果运行一段时间之后，阻塞队列中的任务也执行完了，线程池中的线程会怎么样？

我：这个时候如果超过了设定的空闲超时时间，非核心线程会被回收掉，而核心线程不会被回收，而是出于空闲状态  

面试官：好的，那这种情况在什么场景下会发生?

我：这个…那个…我好像没有遇到过这样的情况……

面试官：嗯，好的，你回去之后再好好想想。

我：……..

PS：面试真的会紧张，导致很多明明知道的东西却全忘记了。所以一定要放松放松。而且有蛮多面试官其实会很耐心，会引导你，但也会沿着你的思路一直细问下去，所以一定要确保自己思维逻辑清晰。 



#### 2,锁
* 在关于锁的面试过程中，一般主要问Synchronized和ReentrantLock的实现原理，更有甚者会问读写锁。

面试官：都了解Java中的什么锁？

我：比如Synchronized和ReentrantLock…读写锁用的不多，就没研究了。

PS:我就怕被问读写锁，因为一直没去看。所以，对一些自己不了解的话题，尽量少说一点，也坦白承认自己不会。

面试官：那好，你先说说Synchronized的实现原理吧。

我：嗯，Synchronized是JVM实现的一种锁，其中锁的获取和释放分别是monitorenter和monitorexit指令，该锁在实现上分为了偏向锁、轻量级锁和重量级锁，其中偏向锁在1.6是默认开启的，轻量级锁在多线程竞争的情况下会膨胀成重量级锁，有关锁的数据都保存在对象头中……

面试官：哦，嗯，理解的还挺透彻，那你说说ReentrantLock的实现吧…

我：ReentrantLock是基于AQS实现的

面试官：什么是AQS？

我：在AQS内部会保存一个状态变量state，通过CAS修改该变量的值，修改成功的线程表示获取到该锁，没有修改成功，或者发现状态state已经是加锁状态，则通过一个Waiter对象封装线程，添加到等待队列中，并挂起等待被唤醒……

面试官：能说说CAS的实现原理么？

我：CAS是通过unsafe类的compareAndSwap方法实现的（心里得意的一笑）

面试官：哦，好的，那你知道这个方法的参数的含义的么？

我：这个方法看的时间有点久远了，第一个参数是要修改的对象，第二个参数是对象中要修改变量的偏移量，第三个参数是修改之前的值，第四个参数是预想修改后的值….

面试官：嗯，对的，那你知道操作系统级别是如何实现的么？

我：（我去你大爷…）我只记得X86中有一个cmp开头的指令，具体的我忘记了…

面试官：嗯，好，你知道CAS指令有什么缺点么

我：哦，CAS的缺点是存在ABA问题。

面试官：怎么讲？

我：就是一个变量V，如果变量V初次读取的时候是A，并且在准备赋值的时候检查到它仍然是A，那能说明它的值没有被其他线程修改过了吗？如果在这段期间它的值曾经被改成了B，然后又改回A，那CAS操作就会误认为它从来没有被修改过。

面试官：那怎么解决？

我：（有完没完了啊…我的心里是崩溃的）针对这种情况，java并发包中提供了一个带有标记的原子引用类"AtomicStampedReference"，它可以通过控制变量值的版本来保证CAS的正确性。

面试官：嗯，好的，这个问题到此为止，我们再看看别的。 我：….我能喝口水么


#### 3，数据结构hashMap,ConcurrentHashMap

* 面试官：谈谈ConcurrentHashMap实现原理

我：……基于分段锁的……，但是1.8之后改变实现方式了。

面试官：1.8啥方式？

我：……（把1.8的实现原理说了一通，其中提到了红黑树）…

面试官：能讲下红黑树的概念吗？

我：红黑树是一种二叉树，并且是平衡……％……¥……，

面试官：能讲下红黑树的……

我：打住，别问了，红黑树我只知道他是二叉树，比其他树多一个属性，其他的我都不知道。 面试官：好的，那换个，你知道它的size方法是如何实现的么？

我：size方法？是想要得到Map中的元素个数么？

面试官：对的….

我：我记得好像size方法返回是不准确的，平时也不会用到这个方法…

面试官：如果你觉得size方法返回值不准确，那如果让你自己实现，你觉得应该怎么实现呢？

我：（两眼一黑）等等，让我想想…..应该可以用AtomicInteger变量进行记录…嗯，对的，每次插入或删除的时候，操作这个变量，我得意的一笑…

面试官：哦，是么，那如果我觉得这个AtomicInteger这个变量性能不好，还能再优化么？

我：懵逼脸…（当时居然把volitile变量给忘记了）…好像没有了，我想不出来了…

面试官：哦，那回头你再看看源码吧，jdk中已经实现了…

我：哦，是么….

面试官：那今天的面试到此结束，我们后面会通知你。

我：……（结束专业用语）
