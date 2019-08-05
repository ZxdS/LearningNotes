

	#推荐书籍：《java高并发程序设计》倾力推荐，书中介绍了关于并包和并包的使用场景
	#介绍：Java 5 添加了一个新的包到 Java 平台，java.util.concurrent 包。这个包包含有一系列能够让 Java 的并发编程变得更加简单轻松的类。在这个包被添加以前，你需要自己去动手实现自己的相关工具类。
	#阻塞队列 BlockingQueue：
	#数组阻塞队列 ArrayBlockingQueue：
	#延迟队列 DelayQueue：
	
	#链阻塞队列 LinkedBlockingQueue
	#同步队列 SynchronousQueue：它的内部同时只能够容纳单个元素。如果该队列已有一元素的话，试图向队列中插入一个新元素的线程将会阻塞，直到另一个线程将该元素从队列中抽走
	#阻塞双端队列 BlockingDeque：
	
	#并发Map ConcurrentMap：ConcurrentHashMap 和 java.util.HashTable 类很相似，但 ConcurrentHashMap 能够提供比 HashTable 更好的并发性能。在你从中读取对象的时候 ConcurrentHashMap 并不会把整个 Map 锁住。此外，在你向其中写入对象的时候，ConcurrentHashMap 也不会锁住整个 Map。它的内部只是把 Map 中正在被写入的部分进行锁定。
	#栅栏 CyclicBarrier：是一种同步机制，它就是一个所有线程必须等待的一个栅栏，直到所有线程都到达这里，然后所有线程才可以继续做其他事情
	#交换机 Exchanger：表示一种两个线程可以进行互相交换对象
	#执行器服务 ExecutorService：一个可以实现线程池的接口
	#线程池执行者 ThreadPoolExecutor：底层是实现ExecutorService
	#定时执行者服务 ScheduledExecutorService：它能够将任务延后执行，或者间隔固定时间多次执行。 任务由一个工作者线程异步执行，而不是由提交任务给 ScheduledExecutorService 的那个线程执行。
	# ForkJoinPool 进行分叉和合并：它和 ExecutorService 很相似，除了一点不同。ForkJoinPool 让我们可以很方便地把任务分裂成几个更小的任务，也可以合并任务
	#锁 Lock：是一个类似于 synchronized 块的线程同步机制。但是 Lock 比 synchronized 块更加灵活、精细。
	#读写锁 ReadWriteLock：读写锁是一种先进的线程锁机制。它能够允许多个线程在同一时间对某特定资源进行读取，但同一时间内只能有一个线程对其进行写入。
	#原子性布尔 AtomicBoolean：提供了一个可以用原子方式进行读和写的布尔值
	#原子性整型 AtomicInteger 
	#原子性长整型 AtomicLong
	#原子性引用型 AtomicReference