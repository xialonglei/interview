#面试题
<h3 id='1'>进程间的通信方式</h3>

* 管道 

管道是由内核管理的一个缓冲区，相当于我们放入内存中的一个纸条。  
管道的一端连接一个进程的输出。这个进程会向管道中放入信息。  
管道的另一端连接一个进程的输入，这个进程取出被放入管道的信息。  
一个缓冲区不需要很大，它被设计成为环形的数据结构，以便管道可以被循环利用。  
当管道中没有信息的话，从管道中读取的进程会等待，直到另一端的进程放入信息。  
当管道被放满信息的时候，尝试放入信息的进程会等待，直到另一端的进程取出信息。  
当两个进程都终结的时候，管道也自动消失。  
1. 无名管道--用于父子进程间的通信   
2. 有名管道--用于在一台机器中任意两个进程间的通信    

* 消息队列

* 共享内存

共享内存是运行在同一台机器上的进程间通信最快的方式，因为数据不需要在不同的进程间复制。  
通常由一个进程创建一块共享内存区，其余进程对这块内存区进行 读写。  

* 信号量

信号量又称为信号灯，它是用来协调不同进程间的数据对象的，而最主要的应用是前一节的共享内存方式的进程间通信。  
本质上，信号量是一个计数器，它用来记录对某个资源（如共享内存）的存取状况。一般说来，为了获得共享资源，进程需要执行下列操作：   
1. 测试控制该资源的信号量。   
2. 若此信号量的值为正，则允许进行使用该资源。进程将信号量减1。   
3. 若此信号量为0，则该资源目前不可用，进程进入睡眠状态，直至信号量值大于0，进程被唤醒，转入步骤（1）。   
4. 当进程不再使用一个信号量控制的资源时，信号量值加1。如果此时有进程正在睡眠等待此信号量，则唤醒此进程。  

* 套接字

套接口（socket）编程是实现Linux系统和其他大多数操作系统中进程间通信的主要方式之一。  
我们熟知的WWW服务、FTP服务、TELNET服务 等都是基于套接口编程来实现的。

<h3 id='2'>java线程池</h3>  
对线程的重复利用
* newCachedThreadPool  无限大小的线程池(无则创建，多则回收)  
* newFixedThreadPool  创建一个固定大小的线程池(超出的任务则等待)  
* newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行  
* newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序执行

1.newCachedThreadPool
```
public static ExecutorService newCachedThreadPool() {  
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>());  
} 
参数说明:1.线程池维护线程的最小数量；2.能够创建线程的最大数量；3.线程无任务时的包活时间60s；4.包活时间单位；5.所用的阻塞队列(SynchronousQueue同步队列，来一个处理一个，容量为空；要移除必须要等待插入，一旦又插入马上进行处理，也就是移除)
* 终止并从缓存中移除那些已有 60 秒钟未被使用的线程
```
2.newFixedThreadPool
```
public static ExecutorService newFixedThreadPool(int nThreads) {
      return new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS,new LinkedBlockingQueue<Runnable>());  
} 
```
3.newScheduledThreadPool
```
ScheduledExecutorService  threadPools = Executors.newScheduledThreadPool(2);  
for(int i = 0; i < 2;i++){  
    threadPools.schedule(new Runnable() {  
        @Override  
        public void run() {  
                System.out.println(Thread.currentThread().getName() + "定时器执行");  
        }  
    }, 2, TimeUnit.SECONDS);  
}
//2秒后开始执行
//scheduleAtFixedRate 这个方法是不管你有没有执行完，反正我每隔4秒来执行一次，以相同的频率来执行
//scheduleWithFixedDelay 这个是等你方法执行完后，我再隔4秒来执行，也就是相对延迟后，以固定的频率去执行
```
4.newSingleThreadExecutor
```
public static ExecutorService newSingleThreadExecutor() {  
        return new FinalizableDelegatedExecutorService  
            (new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>()));  
} 
```
<h3 id='3'>数据库索引失效</h3>
1. or语句
2. like 语句
3. 如果字段类型为字符串，但是查询是没有加''，索引也无效

<h3 id='4'>springmvc工作原理</h3>
![imag](https://github.com/xialonglei/interview/blob/master/springmvc%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.jpg)  

1. http发送的请求交给DispatcherServlet，然后通过HandlerMapping找到与之对应的Controller
2. 之后通过Controller调用业务逻辑
3. 之后Controller返回ModelAndView逻辑视图给DispatcherServlet
4. 通过ViewResovler对ModelAndView进行解析,也就是找到视图真正的地址
5. 之后视图负责将结果显示给客户端

<h3 id='5'>mysql数据引擎对count(*)的影响</h3>

MyISAM:MyISAM引擎很容易获得总行数的统计,查询速度变得更快,因为myisam存储引擎已经存储了表的总行数。注意一点：MyISAM存储引擎的表，count(\*)速度快的也仅仅是不带where条件的count  

InnoDB:针对InnoDB表,尽量不执行 SELECT COUNT(\*) 语句,因为Innodb表没有类似MyISAM那样的内部计数器来记录表记录总量,执行这个操作将会全表扫描,速度很慢。所以呢，表的行数越多，扫描的时间就越多。当你表行数还是小数量的时候体会不出速度差距。比如百万也感觉不出明显。上千万就会很明显速度差别了。  
对策：对InnoDB存储引擎的大表进行select count(\*)统计总数操作,业界都会尽量避免。

<h3 id='6'>回答RabbitMQ与MongoDB</h3>
着重：  
RabbitMQ解耦  
MongoDB读写性能高，Schema Free不用设计表结构





