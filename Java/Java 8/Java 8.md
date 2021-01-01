# 一.JMM

## 1.JMM概述

> 伪共享

![image-20201203105049304](Java 8.assets\image-20201203105049304.png)

CPU存在多级缓存，当CPU读取数据时会从一级缓存中向下查找。所有缓存中的数据都是从主存中加载的，而从主存中读取数据的最小单位是**缓存行**，一个缓存行的大小通常是64KB。如果A线程所需要的数据只有1KB，那么剩余的63KB数据很有可能被其他前程所操作。如果其他线程修改了A线程缓存行的某个数据，根据缓存一致性协议这个缓存行就会失效重新去主存中加载。这种频繁使缓存失效的资源竞争情况就称为伪共享

**避免伪共享：**

- 将我们创建的类进行数据填充，填充至64个字节
- JDK 8提供了`@Contended`注解，该注解使用在类上会自动增加目标实例的大小

> JMM

![image-20201203104414368](Java 8.assets\image-20201203104414368.png)

当多个线程访问资源时会先从主内存中加载到线程的工作内存中，在自己的工作内存中对数据完成处理后刷新回主内存中。这种模型会产生数据的可见性问题，即如果两个线程同时读取了一个数据，当一个线程对这个数据进行了修改并刷新回主内存中后，另一个线程无法得知此次修改，导致线程之间的数据不一致，这就是产生线程安全的主要原因

## 2.volatile

- 保证线程间数据可见性
- 禁止指令重排序
- 不保证原子性

```java
class MyNumber{
    volatile int number = 10;
    
    public void addTo1205(){
        this.number = 1025;
    }
}

public class Test{
    public static void main(String[] args){
        MyNumber myNumber = new MyNumber();
        
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "开始执行");
            try{
                Thread.sleep(3000);
            }catch(Exception e){
                e.printStackTrace();
            }
            myNumber.addTo1205();
            System.out.println(Thread.currentThread().getName()+"\t A update number,number value:"+myNumber.number);
        }, "A").start();
        
        while(true){
            if(myNumber.number == 1025){
            	System.out.println("number已更新");
                break;
        	}
        } 
    }
    //如果不加volatile关键字则main线程不会收到myNumber变量已经发生改变而进入死循环
}
```

## 3.原子变量和CAS算法

> i++原子问题

**i++JVM指令分为三步“读-改-写”：**

1. int temp = i;
2. i = i + 1;
3. i = temp;

```java
public class TestAtomicDemo{
    public static void main(String[] args){
    	AtomicDemo ad = new AtomicDemo();
        for(int i = 0; i < 10; i++){
            new Thread(ad).start();
        }
	}
    
    class AtomicDemo implements Runnable{
        volatile private int serialNumber = 0;
        @Override
        public void run(){
            try{
                Thread.sleep(200);
            }catch(Exception e){}
            System.out.println(Thread.currentThread().getName() + ":" + getSerialNumber());
        }
        public int getSerialNumber(){
            return serialNumber++;
        }
    }
}
```

添加`volatile`关键字依然会出现线程安全问题（出现相同的值），原因就是i++不是原子操作，而`volatile`也不能保证该操作的原子性。当A线程完成对数据的修改并刷新回主存中，`volatile`关键字虽然可以让B线程得到这个被修改的值，但是如果B线程对**serialNumber**的操作已经进入了第二步，那么即使获取到了A线程修改的值也没有用了

> 解决方法

java.util.concurrent.atomic包下的原子类，这些类底层的变量都使用`volatile`关键字保证内存可见性，使用CAS算法保证数据原子性。

> CAS算法

CAS包含三个操作数：

- 内存值：V
- 旧的预期值：A
- 更新值：B

**当且仅当V == A时，V = B，否则不做任何操作**，CAS可以保证在并发访问同一个数据时，只有一个线程可以修改成功，因为CAS算法在线程修改失败后不会进入阻塞状态，而是会继续尝试修改，所以比一般的锁效率高

```java
class AtomicDemo implements Runnable{
    private AtomicInteger serialNumber = new AtomicInteger(0);
    @Override
    public void run(){
        try{
            Thread.sleep(200);
        }catch(Exception e){}
        System.out.println(Thread.currentThread().getName() + ":" + getSerialNumber());
    }
    public int getSerialNumber(){
        return serialNumber.getAndIncrement();
    }
}
```



# 二.多线程

> 并发与并行

并发，指的是多个线程，在**同一时间段**同时进行

并行，指的是多个线程，在**同一时间点**同时进行

并发的多个任务之间是相互抢占资源的

并行的多个任务之间是不相互抢占资源的

只有在多个CPU或这一个CPU多核的情况中才会发生并行，否则，看似同时发生的事情，起始都是并发执行



##1.线程安全集合

> 产生**java.util.ConcurrentModificationException**

```java
pubilc static void main(String[] args){
    List<String> list = new ArrayList<String>();
    for(int i = 1; i <= 30; i++){
        new Thread(() -> {
            list.add(UUID.randomUUID().toString().substring(0.8));
            System.out.println(list);
        }).start();
    }
}
```

> 解决方法

- **new Vector()，底层使用synchronized实现同步**

- **Collections.synchronizedList(List<T> list)**

-  **Collections.sychronizedSet(Set<T> s)**

- **Collections.synchronizedMap(Map<k,v> m)**

-  **new CopyOnWriteArrayList()**，List接口的子类，底层为Object[]，其add()会获取这个数组将这个数组中的元素并使用Arrays.copyOf()复制到一个长度大1的新数组中，将新增元素设置到新数组中，再使用新数组替换原数组，add()使用ReentrantLock进行同步。该集合可以解决普通集合在遍历时不使用迭代器修改集合数据时会产生的**并发修改异常**

  ```java
  public boolean add(E e){
      final ReentrantLocl lock = this.lock;
      lock.lock();
      try{
          Object[] elements = getArray();
          int len = elements.length;
          Object[] newElements = Arrays.copyOf(elements,len+1);
          newElements[len] = e;
          setArray(newElements);
          return true;
      }finally{
          lock.unlock();
      }
  }
  ```

- **new CopyOnWriteArraySet<>()**

- **new ConcurrentHashMap<>()**

## 2.生产者消费者

```java
class Source{
    private int number = 0;
    public synchronized void increment() throws Exception{
        while(number != 0){
            this.wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName() + "\t" + number);
        this.notifyAll();
    }
    public synchronized void decrement() throws Exception{
        while(number != 1){
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName() + "\t" + number);
    }
}
pubilc class Main{
    public static void main(String[] args){
        Source source = new Source();
        new Thread(() -> {
            for(int i = 0; i < 10; i++){
                try{
                    source.increment();
                }catch(Exception e){
                    e.printStackTrace();
                }
            }
        }, "A").start();
        
        new Thread(() -> {
            for(int i = 0; i < 10; i++){
                try{
                    source.decrement();
                }catch(Exception e){
                    e.printStackTrace();
                }
            }
        }, "B").start();
    }
}
```

> 虚假唤醒

使用notify或notifyAll方法会产生虚假唤醒，即唤醒了不该唤醒的线程，而线程被唤醒后会执行wait()方法后的代码。为防止虚假唤醒，对于调用wait()方法的条件判断不能使用`if`来包裹，而是使用`while`来包裹，这样即使产生了虚假唤醒，在继续执行wait()方法后的代码时，依然会进入循环中再进行一次判断

## 3.锁

###3.1.锁类型

> 悲观锁和乐观锁

**悲观锁：**

悲观锁会为代码添加锁，当线程要访问被锁资源时需要先获取锁，没有获取到锁的线程需要等待锁的释放，然后由CPU唤醒等待线程。synchronized和Lock都是悲观锁

**乐观锁：**

乐观锁不会为资源添加锁，只是在更新数据的时候去判断之前有没有别的线程更新了这个数据。如果这个数据没有被更新，当前线程将自己修改的数据成功写入。如果数据已经被其他线程更新，则根据不同的实现方式执行不同的操作（例如报错或者自动重试）。

> 独占锁和共享锁

**独占锁：**写锁

也称排他锁，该锁一次只能被一个线程所持有。如果线程T对数据A加上排它锁后，则其他线程不能再对A加任何类型的锁。获得排它锁的线程即能读数据又能修改数据。

**共享锁：**读锁

共享锁是指该锁可被多个线程所持有。如果线程T对数据A加上共享锁后，则其他线程只能对A再加共享锁，不能加排它锁。获得共享锁的线程只能读数据，不能修改数据。

> 公平锁和非公平锁

**公平锁：**

根据线程先来后到的顺序获取CPU执行权。CPU唤醒阻塞线程的开销比非公平锁大。因为等待队列中除第一个线程以外的所有线程都会阻塞，都需要被唤醒

**非公平锁：**

多个线程加锁时直接尝试获取锁，获取不到才会到等待队列的队尾等待。但如果此时锁刚好可用，那么这个线程可以无需阻塞直接获取到锁，所以非公平锁有可能出现后申请锁的线程先获取锁的场景。CPU唤醒阻塞线程的开销低，因为线程有几率不阻塞直接获得锁，CPU不必唤醒所有线程。

> 可重入锁和不可重入锁

**可重入锁：**ReentrantLock

在一个线程中，如果这个线程已经获取到了方法中的锁，而在被锁代码中又有一段被同一个锁对象锁中的代码，那么线程会自动获取到这把锁，而不是因为这把锁已经占用而被阻塞，造成死锁

```java
public class LockTest{
    public static synchronized void A(){
        System.out.println("A");
    }
    public static synchronized void B(){
        A();
    }
    public static void main(String[] args){
        B();
    }
}
```

**不可重入锁：**NonReentrantLock

```markdown
ReentrantLock和NonReentrantLock都继承父类AQS，其父类AQS中维护了一个同步状态status来计数重入次数，status初始值为0。

当线程尝试获取锁时，可重入锁先尝试获取并更新status值，如果status == 0表示没有其他线程在执行同步代码，则把status置为1，当前线程开始执行。如果status != 0，则判断当前线程是否是获取到这个锁的线程，如果是的话执行status+1，且当前线程可以再次获取锁。而非可重入锁是直接去获取并尝试更新当前status的值，如果status != 0的话会导致其获取锁失败，当前线程阻塞。

释放锁时，可重入锁同样先获取当前status的值，在当前线程是持有锁的线程的前提下。如果status-1 == 0，则表示当前线程所有重复获取锁的操作都已经执行完毕，然后该线程才会真正释放锁。而非可重入锁则是在确定当前线程是持有锁的线程之后，直接将status置为0，将锁释放。
```



> 自旋锁和自适应锁

**自旋锁：**

阻塞或唤醒一个Java线程需要操作系统切换CPU状态来完成，这种状态转换需要耗费处理器时间。如果同步代码需要执行的时间很短，此时因为所被占用而将其他线程挂起，然后再由CPU对线程的状态进行转换消耗的时间有可能比用户代码执行的时间还要长。

自旋锁本身是有缺点的，它不能代替阻塞。自旋等待虽然避免了线程切换的开销，但它要占用处理器时间。如果锁被占用的时间很短，自旋等待的效果就会非常好。反之，如果锁被占用的时间很长，那么自旋的线程只会白浪费处理器资源。所以，自旋等待的时间必须要有一定的限度，如果自旋超过了限定次数（默认是10次，可以使用-XX:PreBlockSpin来更改）没有成功获得锁，就应当挂起线程。

自旋锁的实现原理同样也是CAS，AtomicInteger中调用unsafe进行自增操作的源码中的do-while循环就是一个自旋操作，如果修改数值失败则通过循环来执行自旋，直至修改成功。

**自适应锁：**

自适应意味着自旋的时间（次数）不再固定，而是由前一次在同一个锁上的自旋时间及锁的拥有者的状态来决定。如果在同一个锁对象上，自旋等待刚刚成功获得过锁，并且持有锁的线程正在运行中，那么虚拟机就会认为这次自旋也是很有可能再次成功，进而它将允许自旋等待持续相对更长的时间。如果对于某个锁，自旋很少成功获得过，那在以后尝试获取这个锁时将可能省略掉自旋过程，直接阻塞线程，避免浪费处理器资源。



### 3.2.synchronized锁升级

synchronized共有4中状态，从高到低依次是**无锁**、**偏向锁**、**轻量级锁**、**重量级锁**。锁可以升级但不能降级

> 无锁

无锁没有对资源进行锁定，所有的线程都能访问并修改同一个资源，但同时只有一个线程能修改成功。

无锁的特点就是修改操作在循环内进行，线程会不断的尝试修改共享资源。如果没有冲突就修改成功并退出，否则就会继续循环尝试。如果有多个线程修改同一个值，必定会有一个线程能修改成功，而其他修改失败的线程会不断重试直到修改成功。上面我们介绍的CAS原理及应用即是无锁的实现。无锁无法全面代替有锁，但无锁在某些场合下的性能是非常高的。

> 偏向锁

偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁，降低获取锁的代价。

当一个线程访问同步代码块并获取锁时，会在Mark Word里存储锁偏向的线程ID。在线程进入和退出同步块时不再通过CAS操作来加锁和解锁，而是检测Mark Word里是否存储着指向当前线程的偏向锁。引入偏向锁是为了在无多线程竞争的情况下尽量减少不必要的轻量级锁执行路径，因为轻量级锁的获取及释放依赖多次CAS原子指令，而偏向锁只需要在置换ThreadID的时候依赖一次CAS原子指令即可。

> 轻量级锁

当锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，从而提高性能。

若当前只有一个等待线程，则该线程通过自旋进行等待。但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。

> 重量级锁：将除了拥有锁的线程以外的线程都阻塞



### 3.3.ReentrantLock

**Lock接口的方法：**

- `void lock()`：获取锁
- `boolean tryLock()`：在锁为空闲状态时获取锁
- `boolean tryLock(long time, TimeUnit unit)`：如果锁在给定等待时间内空闲，则获取锁
- `void unlock()`：释放锁

**ReentrantLock：**Lock接口的实现类

> 可重入锁实现精准唤醒

```java
class Source{
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition con1 = lock.newCondition():
    private Condition con2 = lock.newCondition();
    private Condition con3 = lcok.newCondition();
    
    public void print5(){
        lock.lock();
        try{
            while(number != 1){
                con1.wait();
            }
            for(int i = 0; i < 5; i++){
                System.out.println(Thread.currentThread().getName() + "\t" +i);
            }
            number = 2;
            con2.signal();
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            lock.unlock();
        }
    }
    
    public void print10(){
        lock.lock();
        try{
            while(number != 2){
                con2.wait();
            }
            for(int i = 0; i < 10; i++){
                System.out.println(Thread.currentThread().getName() + "\t" +i);
            }
            number = 3;
            con3.signal();
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            lock.unlock();
        }
    }
    
    public void print15(){
        lock.lock();
        try{
            while(number != 3){
                con3.wait();
            }
            for(int i = 0; i < 15; i++){
                System.out.println(Thread.currentThread().getName() + "\t" +i);
            }
            number = 1;
            con1.signal()
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            lock.unlock();
        }
    }
}

public class Main{
    public static void main(String[] args){
        Source source = new Source();
        new Thread(() -> {
            source.print5();
        }, "A").start();
        
        new Thread(() -> {
            source.print10();
        }, "B").start();
        
        new Thread(() -> {
            source.print15();
        }, "C").start();
    }
}
```

### 3.4.ReentrantReadWriteLock

- 读锁和写锁之间互斥，即在完成某一个锁保护的代码前，另一个锁保护的代码不会介入执行。
- **写锁**为独占锁，会保证抢到**写锁的线程**其内部代码执行时的安全，即当一个线程抢到写锁时，其它线程只能等待抢到的线程释放锁后再去争夺锁
- **读锁**为共享锁，不会保证抢到**读锁的线程**其内部代码执行时的安全，即当一个线程抢到读锁时，其它线程不需要等待抢到的线程释放锁就可以直接争夺锁，并可能在一个读线程没有执行完毕时就开始执行另一个读线程

```java
calss Cache{
    private volatile Map<String,Object> map = new HashMap<>();
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
    
    public void put (String key, Object value){
        readWriteLock.writeLock().lock();
        try{
            System.out.println(Thread.currentThread().getName()+"\t写入数据"+key);
        	map.put(key, value);
            TimeUnit.SECONDS.sleep(3);
        	System.out.println(Thread.currentThread().getName()+"\t写入完成");
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            readWriteLock.writeLock().lock();
        }
    }
    
    public void get(String key){
        readWriteLock.readLock().lock();
        try{
            System.out.println(Thread.currentThread().getName()+"\t读取数据");
        	Object result = map.get(key);
            TimeUnit.SECONDS.sleep(3);
        	System.out.println(Thread.currentThread().getName()+"\t读取完成"+result);
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            readWriteLock.readLock().unlock();
        }
    }
}

public class ReadWriteLock{
    public static void main(String[] args){
        Cache cache = new Cache();
        for(int i = 1; i <= 5; i++){
            new Thread(() -> {
                cache.put(i+"", i+"");
            }, String.valueOf(i)).start();
        }
        
        for(int i = 1; i <= 5; i++){
            new Thread(() -> {
                cache.get(i+"");
            }, String.valueOf(i)).start();
        }
    }
}
```

## 4.线程工具类

> **CountDownLatch**

线程计数器，使用`await()`方法可以阻塞 线程，直到线程计数器的线程数变为0才会停止阻塞

```java
public class CountDownLatchDemo{
    public static void main(String[] args){
        CountDownLatch countDownLatch = new CountDownLatch(6);
        
        for(int i = 1; i <= 6; i++){
            new Thread(() -> {
               try{
                   System.out.println(Thread.currentThread().getName()+"\t离开教室"); 
               }catch(Exception e){}finally{
                   countDownLatch.countDown();
               }
            }).start();
        }
        
        countDownLatch.await();
        System.out.println(Thread.currentThread().getName()+"\t班长关门走人");
    }
}
```

> **CyclicBarrier**

`await()`可以把线程阻塞在**CyclicBarrier**中。创建**CyclicBarrier**时可以指定**该对象中等待的线程数**和**一个Runnable对象**，当**CyclicBarrier**对象上等待的线程达到数量后就会执行构造方法传入的Runnable对象的`run()`

```java
public class CyclicBarrierDemo{
    public static void main(String[] args){
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
            System.out.println("***召唤神龙");
        });
        
        for(int i = 1; i <= 7; i++){
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName()+"\t收集到第："+ i + "颗龙珠");
                try{
                    cyclicBarrier.await();
                }catch(Exception e){
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

> **Semaphore**

信号量，创建该对象的时候必须指定有多少个信号，每当使用`acquire()`方法时信号量就会减1，调用`release()`方法时信号量就会加1；如果信号量减为0，线程会阻塞在`acquire()`方法，知道信号量不为0时才会继续执行

```java
public class SemaphoreDemo{
    public static void main(String[] args){
        Semaphore semaphore = new Semaphore(3);
        for(int i = 1; i <= 6; i++){
            new Thread(() -> {
                try{
                    semaphore.acquire();
                	System.out.println(Thread.CurrentThread().getName()+"\t抢到了资源");
                    //模拟使用资源
                    try{
                        TimeUnit.SECONDS.sleep(3);
                    }catch(Exception e){
                        e.printStackTrace();
                    }
                }catch(Exception e){
                    e.printStackTrace();
                }finally{
                    semaphore.release();
                }
            }, String.valueOf(i)).start()
        }
    }
}
```

## 5.线程池



> **java.util.concurrent.Executor**
>
> > **ExecutorService**	子接口：线程池的主要接口
> >
> > > **ThreadPoolExecutor**	线程池的实现类
> > >
> > > **ScheduledExecutorService**	子接口：负责线程调度
> > >
> > > > **ScheduledThreadPoolExecutor**	继承ThreadPoolExecutor，实现ScheduledExecutorService

###5.1.Executors

线程池工具类创建线程池：

- `Executors.newCachedThreadPool()`：可伸缩线程池，线程数量根据传入线程任务的多少决定
- `Executors.newSingleThreadExecutor()`：创建单个线程的线程池
- `Executors.newFixedThreadPool(int nThreads)`：创建指定大小的线程池
- `Executors.newSecheduledThreadPool(int nThreads)`：创建可调度的指定大小的线程池

提交线程任务：

- `submit()`：主要用于提交Callable接口的线程任务，返回Future对象，通过该对象的get()获取任务返回值。但也有Runnable接口的重载
- `schedule(Callable callable, long delay, TimeUnit unit)`：**ScheduledExecutorService**中的方法，定时提交线程任务，也有Runnable的重载

使用步骤：

1. 使用线程池的工厂类Executors里提供的静态方法生产一个线程池
2. 创建一个类，实现Runnable接口，重写run方法，设置线程任务
3. 调用ExecutorService中的方法submit，传递线程任务(实现类),开启线程，执行run方法
4. 调用ExecutorService中的方法shutdown销毁线程池(不建议执行)

```java
class RunnableImp implements Runnable{
	public RunnableImp (){}
	@Override
	public void run(){
		for(int i = 0; i < 5; i++){
			System.out.println(Thread.currentThread().getName()+"-->"+i);
		}
	}
}
public class Main{
    public static void main(String[] args){
        //开启2个线程的线程池
        ExecutorService es = Executors.newFixedThreadPool(2);
		for(int i = 0; i < 10; i++){
            es.submit(new RunnableImp());
        }     
        //代码执行后不会结束，因为线程池没有关闭

        /*
        使用线程池方法二:	
            try {
                for(int i = 0;i<10;i++){
                    es.execute(()->{ System.out.println(Thread.currentThread().getName()+"ok");
                    });
            }catch(Exception e) {
                 e.printStackTrace();
            }finally {
                es.shutdown();
            } 
        */
    }
}
```

###5.2.ThreadPoolExecutor

```java
public ThreadPoolExecutor(int corePoolSize,
						 int maximumPoolSize,
                         long keepAliveTime,
                         TimeUnit unit,
                         BlockingQueue<Runnable> workQueue,
                         ThreadFactory threadFactory,
                         RejectedExecutionHandler handle)
```

1. 基本线程数
2. 最大线程数
   - **CPU密集型：**通常设为CPU数量+1，CPU数量使用Runtime.getRuntime().availableProcessors()获取
   - **IO密集型：**判断程序中非常消耗资源的大型操作数量，如IO操作。只需将线程数设置大于大型操作数量即可，通常设置为2倍
3. 某线程超时不调用自动关闭
4. 超时单位
5. 阻塞队列，等候区。
6. 线程工厂,一般默认即可
7. 拒绝策略
   - **AbortPolicy()（默认）：** 抛出异常
   - **CallerRunsPolicy()：**由哪条线程传入的任务就返回由哪条线程执行
   - **DiscardPolicy()：**丢弃任务，但不抛出异常
   - **DiscardOldestPolicy()：**队列满了，尝试和最早的一个竞争，若失败则被抛弃且不会抛出异常

> 示例

```java
public static void main(String[] args){
    //使用ThreadPoolExecutor创建线程池
    ExecutorService pool = new ThreadPoolExecutor(
        2,
        5,
        3,
        TimeUnit.SECONDS,
        new LinkedBlockingDeque<>(3),
        Executors.defaultThreadFactory(),
        new ThreadPoolExecutor.AbortPolicy()
    );
    //提交线程任务
    try {
        for(int i = 0;i<10;i++){
            pool.execute(() -> {
          System.out.println(Thread.currentThread().getName()+"ok");
            });
        }
    }catch(Exception e) {
        e.printStackTrace();
    }finally {
        pool.shutdown();
    }
}
```

> 线程池工作原理

当核心线程数都被使用，而还有线程进入，则新线程进入等候区（消息队列）等待，若等候区也满了则触发最大线程数，依照线程进入的先后顺序进行处理，若等候区也满了，则触发拒绝策略

### 5.3.ForkJoinPool

分支合并框架：将一个大任务拆分（Fork）成若干小任务，再将一个个小任务的执行结果进行合并（Join），主要用于计算较大的数字

工作窃取：当执行新的任务时它可以将其拆分为小的任务，由ForkJoinPool将这些小任务分配给各个线程，在各个线程中形成任务队列。在线程在执行自己的任务队列时，空闲的线程会从其它线程的任务队列尾部窃取一个小任务执行。再所有小任务执行完毕后将结果进行整合

使用步骤：

1. 编写任务拆分的类

   - RecursiveTask\<T t>：实现了Future接口，可以获取任务返回值
   - RecursiveAction：线程任务没有返回值

2. 使用**ForkJoinPool**的`invoke`方法，传递任务队列由分支合并框架进行线程对任务的分配

   ```java
   public class TestForkJoinPool{
       public static void mian(String[] args){
           ForkJoinPool pool = new ForkJoinPool();
           
           ForkJoinTask<Long> task = new ForkJoinSumCalculate(0L, 100000000L);
           
           Long sum = pool.invoke(task);
           System.out.println(sum);
       }
   }
   
   class ForkJoinSumCaculate extends RecursiveTask<Long>{
       //提供一个版本号
       private static final long serialVersionUID = -25919547999;
       
       private long start;
       private long end;
       //定义每一个子任务要累加的最大值
       private static final long THRESHOLD = 10000L;
       
       public ForkJoinSumCalculate(long start, long end){
           this.start = start;
           this.end = end;
       }
       
       @Override
       protected Long compute(){
           long length = end - start;
           if(length <= THRESHOLD){
               long sum = 0L;
               if(long i = start; i <= end; i++){
                   sum += i;
               }
               return sum;
           }else{
               long middle = (start + end) / 2;
               
               ForkJoinSumCalculate left = new ForkJoinSumCalculate(start, middle);
               left.fork();
               
               ForkJoinSumCalculate right = new ForkJoinSumCalculate(middle+1, end):
               right.fork();
               
               return left.join() + right.join();
           }
       }
   }
   ```

   **任务拆分流程：**

   **ForkJoinPool**的`invoke()`会调用**RecursiveTask**子类重写的`compute()`，进入`compute()`方法先进行任务规模的判断，任务规模不足则不进行拆分；反之进入`else`块，创建一个将当前任务一半的子任务，用该子任务执行第一个`fork()`方法时，该方法会将该子任务压入线程的任务队列中并再次调用`compute()`重复上述操作，直到任务规模足够小，能够进入`if`块时，左侧任务拆分完毕；开始第二个`fork()`进行右侧任务的拆分

   ```mermaid
   graph TD
   Z(开始)
   Z --> A[调用ForkJoinPool.invoke方法传入主任务]
   	A -->|调用| B[RecursiveTask子类重写的compute方法]
   	B -->|compute方法进行判断| C{任务规模是否需要拆分或拆分完毕?}
   		C -->|不需要拆分| E[执行任务,返回任务结果]
   		C -->|单侧拆分完毕| H{主任务是否完全拆分完毕}
   			H -->|是| I[子任务调用join方法获取合并结果]
   			H -->|否| F
           C -->|需要拆分| D[进入else代码块]
           	D --> F[创建当前任务一半大小的子任务]
           		F --> G[通过子任务实例调用fork压入线程的任务队列]
           		G -->|fork方法内部调用| B
           			I --> K(结束)
           			E --> K
   ```

# 三.队列

##1.ArrayBlockingQueue

- **add(Object e)：**添加元素到队列，若队列已满则会抛出异常，返回值为boolean
- **remove()：**从队列取出元素，若队列为空则会抛出异常，返回值为取出元素

```java
public static void test1() {
		ArrayBlockingQueue<Integer> blockingQueue = new ArrayBlockingQueue<>(3);
		//add(Object e)
		System.out.println(blockingQueue.add(1));
		System.out.println(blockingQueue.add(2));
		System.out.println(blockingQueue.add(3));
	//System.out.println(blockingQueue.add(4)); IllegalStateException: Queue full
		System.out.println(blockingQueue.element()); //查看队列首元素
		
		//remove()返回值为取出的元素，先进入队列的元素先被取出
		System.out.println(blockingQueue.remove());
		System.out.println(blockingQueue.remove());
		System.out.println(blockingQueue.remove());
	//System.out.println(blockingQueue.remove()); java.util.NoSuchElementException
}
```

- **offer(Object e)：**添加元素到队列，若队列已满则会返回false
- **poll()：**从队列取出元素，若队列为空则会返回null，返回值为取出元素

```java
public static void test2() {
    ArrayBlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);
    //offer(Object e)添加元素
    System.out.println(blockingQueue.offer("a"));
    System.out.println(blockingQueue.offer("b"));
    System.out.println(blockingQueue.offer("c"));
    System.out.println(blockingQueue.offer("d")); //false
    System.out.println(blockingQueue.peek()); //查看队列首元素

    //poll()取出元素
    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll()); //null
}
```

- **put(Object e)：**添加元素到队列，若队列已满，则会一直等待，直到有空位置才放入，无返回值
- **take()：**从队列取出元素，若队列为空，则会一直等待，直到有元素才会取出，返回值为取出元素

```java
public static void test3() throws InterruptedException{
    ArrayBlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);
    //put(Object e)添加元素
    blockingQueue.put("a");
    blockingQueue.put("b");
    blockingQueue.put("c");
    blockingQueue.put("d"); //程序没有停下，进入等待状态

    //take()取出元素
    System.out.println(blockingQueue.take());
    System.out.println(blockingQueue.take());
    System.out.println(blockingQueue.take());
    System.out.println(blockingQueue.take()); //程序没有停下，进入等待状态
}
```

- **offer(Object e, long timeout, TimeUnit unit)：**添加元素到队列，若队列已满，且指定时间内没有空位置则会自动退出等待，返回值为boolean
- **poll(long timeout, TimeUnit unit)：**从队列取出元素，若队列为空，且指定时间内没有元素则会自动退出等待，返回值为取出元素

```java
public static void test4() throws InterruptedException{
    ArrayBlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);
    //offer(Object e,long timeout,TimeUnit unit)添加元素
    System.out.println(blockingQueue.offer("a"));
    System.out.println(blockingQueue.offer("b"));
    System.out.println(blockingQueue.offer("c"));
    System.out.println(blockingQueue.offer("d",2,TimeUnit.SECONDS)); //程序等待2秒后，自动退出并打印false

    //poll()取出元素
    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll(2,TimeUnit.SECONDS)); //程序等待2秒后，自动退出并打印null
}
```



##2.SynchronousQueue

 **同步队列：**不储存元素，put一个元素必须先从里面取出，否则不能put第二个元素

```java
public class Test{
    public static void main(String[] args) {
        BlockingQueue<String> bq = new SynchronousQueue<String>();
        
        //放入元素线程
        new Thread(()-> {
            try {
                System.out.println(Thread.currentThread().getName()+"->put_a");
                bq.put("a");
                System.out.println(Thread.currentThread().getName()+"->put_b");
                bq.put("b");
                System.out.println(Thread.currentThread().getName()+"->put_c");
                bq.put("c");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"T1").start();
        
        //取出元素线程
        new Thread(()-> {
            try {
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName()+"->"+bq.take());
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName()+"->"+bq.take());
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName()+"->"+bq.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"T2").start();
    }
    //放入一个元素后，另一个线程延迟3秒后才会取出第一个元素，导致T1线程会停下等待此元素被取出后继续添加
}
```

# 四.JKD 8特性

## 1.四大函数式接口

### 1.1.Consumer\<T>

**抽象方法：**`void accept(T t)` ，消耗接口指定泛型类型的数据

**默认方法：**`Consumer<T> andThen(Consumer<? super T> after)`，连接两个Consumer接口返回一个新的Consumer接口，可用新的接口调用`accept()`进行数据消耗，哪个接口在前，哪个接口先消耗

```java
public class Demo_Consumer {
    //定义一个方法，传递一个字符串两个接口
    public static void method(String s, Consumer<String> con1, Consumer<String> con2){
        con1.accept(s);
        con2.accept(s);
        System.out.println("=====================");
        con2.andThen(con1).accept(s);
        //可连接多个接口，con1.andThen(con2).andThen(con3)
    }
    public static void main(String[] args){
        //调用method方法
        method("hello",(t) -> {
            //定义con1消费方式
            System.out.println(t.toUpperCase());
        },
        (t) -> {
            //定义con2消费方式
            System.out.println(t.toLowerCase());
        });
    }
}
```

### 1.2.Supplier\<T>

**抽象方法：**`T get() `，返回接口指定泛型类型的数据

```java
public class Demo_Supplier {
    public static int getMax(Supplier<Integer> sup) {
        return sup.get();
    }
    public static void main(String[] args) {
        int[] arr = new int[] {100,20,4,7,41,52};
        int maxValue = getMax(() -> {
            int max = arr[0];
            for(int i : arr) {
                if(i > max) {
                    max = i;
                }
            }
            return max;
        });
        System.out.println("数组中的最大值为: "+maxValue);
    }
}
```

### 1.3.Function<T,R>

**抽象方法：**`R apply(T t)` ，将T类型的数据转化为R类型

**默认方法：**`Function<T,R> andThen(Function<T,R>)`连接两个Function接口，返回一个新的Function接口，可通过新的Function接口调用`apply()`，哪个接口在前先执行哪个的`apply()`

```java
public class Demo_Function {
    //定义一个方法传入字符串和Function接口，将字符串转化为Integer
    public static Integer change(String s, Function<String,Integer> function){
        return function.apply(s);
    }
    public static void main(String[] args) {
        String s = "123";
        int i = change(s,(String s1) -> {
            return Integer.parseInt(s1);
        });
        System.out.println(i);
    }
}
```

### 1.4.Predicate\<T>

**抽象方法：**`boolean test(T t)`对某种数据类型进行判断，判断规则由调用者实现`test()`方法定义

**默认方法：**

- `default Predicate<T> and(Predicate<? super T> other)`，相当于&&
- `default Predicate<T> or(Predicate<? super T> other)`， 相当于||
- `efault Predicate<T> negate()` ，相当于!

```java
public class Demo_Predicate {
    //定义一个方法，参数传递String和Predicate接口
    public static boolean checkString(String st,Predicate<String> pre){
        return pre.test(st);
    }
    public static void main(String[] args){
        String s = "abcde";
        boolean b = checkString(s,s1 -> s1.length() > 5);
        System.out.println(b);
    }
}
```

```java
public class DemoPredicate_and {
    public static boolean checkString(String s, Predicate<String> pre1,Predicate<String> pre2){
        //return pre1.test(s) && pre2.test(s);
        return pre1.and(pre2).test(s);
    }
    /* negate方法
        public static boolean checkString(String s, Predicate<String> pre){
        //return !pre.test(s);
        return pre.negate().test(s);
    }*/

    public static void main(String[] args) {
        String s = "abcdef";
        boolean b = checkString(s,(String str) -> {
            return str.length() > 5;
        },(String str) -> {
            return str.contains("a");
        });
        System.out.println(b);
    }
}
```



## 2.方法引用

方法引用是对Lambda表达式的简化，当Lambda表达式体的操作已经有了实现的方法，就可以使用方法引用。使用方法引用的条件就是被实现的方法和引用的**方法参数列表**和**返回值**必须匹配。就是将别引用的方法的方法体作为抽象方法的方法体

### 2.1.成员方法引用

> **对象::成员方法名**

```java
@FunctionalInterface
public interface Method_CiteInterface {
    public abstract void print(String str);
}
```

```java
public class Method_Cite {
    //定义一个成员方法，传递字符串，把字符串按照大写输出
    public void printUpperCaseString(String str){
        System.out.println(str.toUpperCase());
    }
}
```

```java
public class DemoMethod_Cite {
    public static void printString(Method_CiteInterface m){
        m.print("Hello");
    }

    public static void main(String[] args) {
        //调用printString方法，方法的参数是函数式接口，可使用Lambda表达式
        printString((String s) -> {
            //创建对象
            Method_Cite mc = new Method_Cite();
            //调用对象中的成员方法printUpperCaseString，把字符串按照大写输出
            mc.printUpperCaseString(s);
        });
        
        //使用方法引用优化
        Method_Cite mc = new Method_Cite();
        //使用的前提是对象和方法必须都存在
        printString(mc::printUpperCaseString);
    }
}
```

> **类名::成员方法名**

**使用条件：**Lambda表达式中第一个参数是第实例方法的调用者。如果Lambda有多个参数，则第一个参数作为实例方法的调用者，其余参数作为实例方法的参数。即将第一个参数作为成员方法的调用者，剩余参数作为成员方法的参数

```java
public class Test{
    @Test
    public void test(){
        //Lambda
        BiPredicate<String, String> bp = (x, y) -> x.equals(y);
        Function<Employee, Integer> fun = emp -> emp.getAge();
        print(emp -> emp.getAge(), new Employee);
        //方法引用
        BiPredicate<String, String> bp2 = String::equals;
        Function<Employee, Integer> fun2 = Employee::getAge;
        print(Employee::getAge, new Employee());
    }
    
    public static void print(Function<Employee,Integer> fun, Employee emp){
        System.out.println(fun.apply(emp));
    }
}
```



###2.2.静态方法引用

```java
@FunctionalInterface
public interface StaticMethod_CiteInterface {
    public abstract int calAbs(int num);
}
```

```java
public class DemoStaticMethod_Cite {
    public static int method(int num,StaticMethod_CiteInterface s){
        return s.calAbs(num);
    }

    public static void main(String[] args) {
        //调用method方法
        int absolute = method(-10,(n) -> {
            return Math.abs(n);
        });
        System.out.println(absolute);
        /*方法引用写法:
            1.Math类存在
            2.abs静态方法也存在*/
        int absolute2 = method(-10,Math::abs);
        System.out.println(absolute2);
    }
}
```

###2.3.this关键字与super关键字的方法引用

```java
@FunctionalInterface
public interface SuperThis_CiteInterface {
    public abstract void greet();
}

```

```java
public class SuperThis_Cite {
    //定义一个说你好的方法
    public void sayHello(){
        System.out.println("Hello,我是父类");
    }
}
```

```java
public class SuperThis_Extend_Cite extends SuperThis_Cite {
    //重写父类SayHello方法
    @Override
    public void sayHello(){
        System.out.println("Hello,我是子类");
    }
    //定义一个方法传递Super_CiteInterface接口
    public void method(SuperThis_CiteInterface sci){
        sci.greet();
    }
    public void show(){
        //super关键字
        method(() -> {
            //通过Lambda表达式实现抽象方法
            SuperThis_Cite fu = new SuperThis_Cite();
            fu.sayHello();  //结果: Hello,我是父类
            //以上代码可替换为 super.sayHello();
        });
        /*
            使用super关键字的方法引用
            super已存在
            父类成员方法sayHello已存在
         */
        method(super::sayHello); //结果: Hello,我是父类
        
        //this关键字
        method(() -> this.sayHello());  //Hello,我是子类
        /*
            使用this关键字的方法引用
            this已存在
            本类成员方法sayHello已存在
         */
        method(this::sayHello);  //Hello,我是子类
    }

    public static void main(String[] args) {
        new SuperThis_Extend_Cite().show();
    }
}
```

###2.4.构造器引用

```java
public class Person {
    private String name;

    public Person() {}
    public Person(String name) {
        this.name = name;
    }

    public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

```java
@FunctionalInterface
public interface PersonBuilder {
    public abstract Person builderPerson(String name);
}
```

```java
public class PersonDemo {
    public static void printName(String name,PersonBuilder pb){
        Person person = pb.builderPerson(name);
        System.out.println(person);
    }

    public static void main(String[] args) {
        //调用printName方法使用Lambda表达式
        printName("武建琦",(name) -> {
            return new Person(name);
        });
        /*
            使用构造器引用
            Person类已存在
            有参构造方法已存在，可以使用new
         */
        printName("武建洋",Person::new);
    }
}
```

###2.5.数组构造器引用

```java
public interface ArrayBuilder {
    public abstract int[] buildArray(int length);
}
```

```java
public class ArrayBuilderDemo {
    public static int[] creatArray(int length,ArrayBuilder ab){
        return ab.buildArray(length);
    }

    public static void main(String[] args) {
        int[] arr1 = creatArray(4,(length) -> {
            return new int[length];
        });
        System.out.println(arr1.length);
        //使用方法引用优化
        int[] arr2 = creatArray(8,int[] :: new);
        System.out.println(arr2.length);
        System.out.println(Arrays.toString(arr2));
    }
}
```

## 3.Stream流

```markdown
流是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列
集合讲的是数据，流讲的是计算。流不存储元素
流不会改变源对象。而是返回一个持有结果的新Stream
Stream操作是延迟执行的。这意味着它们会等到需要结果的时候才会执行，即调用执行终止操作的方法
因为MySQL的语句可以在数据库中执行信息过滤，而有些数据库则不行，所以可以通过流来对此类型数据进行过滤
Stream的操作是惰性的
```

###3.1.流的获取方式

1. 所有Collection集合都可以通过默认方法stream转换为流，Map集合只能间接转换

   - `default Stream<E> stream()`
   - `default Stream<E> parallelStream()`：对**ForkJoinPool**的简化，使用方法和串行流相同，但执行大任务时效率更高

   ```java
   List:
   	List<String> list = new ArrayList<>();
   	Stream<String> stream = list.stream();
   
   Set:
   	Set<String> set = new HashSet<>();
   	Stream<String> stream = set.stream();
   
   Map: Map<String,String> map = new Map<>();
   	1.Set<String> keySet = map.keySet();
   	  Stream<String> stream = keySet.stream();
   	2.Collection<Sting> values = map.values();
   	  Stream<String> stream = values.stream();
   	3.Set<Map.Entry<String,String>> entry = map.entrySet();
   	  Stream<Map.Entry<String,String>> stream = entry.stream();
   ```

2. java.util.stream中的Stream接口的静态方法

   - `static <T> Stream<T> of(T...values)`，参数是可变参数,可传递数组

   ```java
   1.Stream<Integer> stream = Stream.of(1,2,3,4,5);
   2.String[] arr = {"a","b","ab"};
     Stream<String> stream = Stream.of(arr);
     Stream<String> stream = Arrays.stream(arr);
   ```

### 3.2.中间操作

> 筛选与切片

`Stream<T> filter(Predicate<? super T> predicate)`：该方法会调用**Predicate**接口的`test()`方法，将Stream流中的元素都传入，如果该方法返回**true**就添加到一个新的Stream流中，否则丢弃该元素。最终返回一个新的Stream流，而上一个流已经关闭。

```java
public class Stream_filter {
    public static void main(String[] args) {
        Stream<String> stream = Stream.of("张三丰","张翠山","张敏","周芷若","张无忌");
        Stream<String> newStream = stream.filter(name -> name.startsWith("张"));
        newStream.forEach(System.out::println);
    }
}
```

`Stream<T> limit(long maxSize)`：截取流中的元素，若集合当前长度大于参数，则进行截取

```java
public class Stream_limit {
    public static void main(String[] args) {
        String[] arr = {"喜羊羊","美羊羊","懒羊羊","慢羊羊","沸羊羊"};
        Stream<String> stream = Stream.of(arr);
        Stream<String> newStream = stream.limit(2);
        newStream.forEach(name -> System.out.println(name));
    }
}
```

`Stream<T> skip(long n)`：跳过前几个元素进行截取，如果流中元素不足n个，则返回一个空流

```java
public class Stream_skip {
    public static void main(String[] args) {
        String[] arr = {"喜羊羊","美羊羊","懒羊羊","慢羊羊","沸羊羊"};
        Stream<String> stream = Stream.of(arr);
        Stream<String> newStream = stream.skip(3);
        newStream.forEach((name) -> System.out.println(name));
    }
}
```

`Stream<T> distince()`：通过`hashCode()`和`equals()`方法对流中元素去重

```java
public class Stream_distinct{
    public static void main(String[] args) {
        String[] arr = {"喜羊羊","沸羊羊","沸羊羊","沸羊羊"};
        Stream<String> stream = Stream.of(arr);
        Stream<String> newStream = stream.distinct();
        newStream.forEach(System.out::println);
    }
}
```

> 映射

`<R> Stream<R> map<Function<? super T, ? extends R> mapper)`：将一个流中的数据经过`apply()`方法处理后的返回值映射到另一个流中

```java
public class Stream_map {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add(new Employee("张三"));
        list.add(new Employee("李四"));
        list.add(new Employee("王五"));
        
        list.stream()
            .map(Employee::getName)
            .forEach(System.out::println);
    }
}
```

`<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> var1)`：将流中的元素传入`apply()`方法进行处理，该`apply()`方法要求返回**Stream**，流中有几个元素经过`apply()`方法处理后就会返回多少个子流，`flatMap()`会将`apply()`返回的流中的每一个元素提取出来映射到新流中。

```java
public class Stream_flatMap {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("aa","bb","world");
        //使用map方法进行转换
        Stream<Stream<Character>> streamStream = list.stream().map(Stream_flatMap::fromStringToStream);
        streamStream.forEach(s -> {
           s.forEach(System.out::println);
        });
        System.out.println("==========================");
        //使用flatMap进行转换
        Stream<Character> characterStream = list.stream().flatMap(Stream_flatMap::fromStringToStream);
        characterStream.forEach(System.out::println);
    }

    //将字符串转化为Stream
    public static Stream<Character> fromStringToStream(String str){
        ArrayList<Character> list = new ArrayList<>();
        for(Character c : str.toCharArray()){
            list.add(c);
        }
        return list.stream();
    }
}
```

> 排序

` Stream<T> sorted(Comparator<? super T> comparator)`：实现**Comparator**接口自定义排序规则，不传递参数为自然排序

```java
public class Stream_Sorted {
    public static void main(String[] args) {
        //sorted自然排序
        List<Integer> list = Arrays.asList(45,56,12,72,95,51);
        list.stream().sorted().forEach(System.out::println);
    }
}
```



### 3.3.终止操作

> 匹配

`boolean allMatch(Predicate<? super T> predicate)`：将流中的每一个元素传入`test()`方法，如果所有元素经过`test()`处理后都返回**true**，则`allMatch()`返回**true**

`boolean anyMatch(Predicate<? super T> predicate)`：将流中的元素传入`test()`方法，只要出现某个元素经过`test()`处理后返回**true**，则`anyMatch()`返回**true**

> 查找

`Optional<T> findFirst()`：返回流中第一个元素

`Optional<T> finaAny()`：返回流中的任意一个元素

`long count()`：统计流中元素个数

`Optional<T> max(Comparator<? super T> comparator)`：根据**Comparator**指定的比较策略返回流中最小值，不传递**Comparator**参数则按照自然排序策略返回

`Optional<T> min(Comparator<? super T> comparator)`

> 归约

`reduce(T identity, BinaryOperator<T> )`：传递一个起始值，和BinaryOperator的接口，该接口中继承**BiFunction**接口并将该接口的所有泛型改为**T**，`apply()`方法会接收两个T类型的值，再返回一个T类型的值。`reduce()`会将流中的数据分别`apply()`处理

`Optional<T> reduce(BinaryOperator<T>)`：因为没有起始值，`apply()`可能返回空，所以`reduce()`返回Optional

```java
public void test(){
    List<Integer> list = Arrays.asList(1,2,3,4,5,5);
    Integer sum = list.stream().reduce(0,(x,y) -> x+y);
    System.out.println(sum);
    /*
    先将起始值作为x，流中第一个元素作为y进行运算，再将结果作为x，流中第二个元素作为y运算；如果没有起始值，则先将流的第一个元素作为x进行运算
    */
}
```

> 收集

`collect(Collector<? super T> collector)`：将流中的数据收集到收集器所指定的容器中

**Collector**：可通过**Collectors**工具类创建

```java
public void test(){
    List<Integer> list = Arrays.asList(1,2,3,4,5,5);
	Set<Integer> set = list.stream().collect(Collectors.toSet());
    HashSet<Integer> hashSet = list.stream().collect(Collectors.toCollection(HashSet::new));
}
```

# 五.NIO

##1.缓冲区

NIO为面向缓冲区，缓冲区底层就是数组，负责数据存取

根据数据类型的不同（boolean）除外，提供了相应类型的缓冲区：

- ByteBuffer
- CharBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer
- MappedByteBuffer

上述缓冲区管理方式几乎一致，通过`allocate()`获取缓冲区

缓冲区中的核心方法：

- `put()`：存入数据到缓冲区
- `get()`：获取缓冲区中的数据

缓冲区的核心属性：

- **capacity**：缓冲区中最大存储数据的容量，一旦声明不能改变
- **limit**：表示缓冲区中可以操作数据的大小，即**limit**后的数据不能进行读写
- **position**：表示缓冲区中下一个数据要进行读写的位置

```java
public class TestBuffer{
	@Test
    public void test(){
        String str = "abced";
        ByteBuffer buf = ByteBuffer.allocate(1024);
        System.out.println("=====allocate()=====");
        System.out.println(buf.position()); //0
        System.out.println(buf.limit()); //1024
        System.out.println(buf.capacity()); //1024
        
        //向缓冲区存入数据
        buf.put(str.getBytes());
        System.out.println("=====put()=====");
        System.out.println(buf.position()); //5
        System.out.println(buf.limit()); //1024
        System.out.println(buf.capacity()); //1024
        
        //切换到读模式
        buf.flip();
        System.out.println("=====flip()=====");
        System.out.println(buf.position()); //0
        System.out.println(buf.limit()); //5
        System.out.println(buf.capacity()); //1024
        
        //读取数据
        byte[] bytes = new byte[buf.limit()];
        buf.get(bytes);
        System.out.println("=====get()=====");
        System.out.println(new String(bytes, 0, bytes.length));
        System.out.println(buf.position()); //5
        System.out.println(buf.limit()); //5
        System.out.println(buf.capacity()); //1024
    }
}
```

**直接缓冲区与非直接缓冲区**

- 非直接缓冲区：通过`allocate()`方法分配的缓冲区，将缓冲区建立在JVM的内存中
- 直接缓冲区：
  通过`allocateDirect()`方法分配直接缓冲区，将缓冲区建立在物理内存中。还可以通过**FileChannel**的`map()`将文件区域直接映射到内存中来创建。该方法返回**MappedByteBuffer**

## 2.通道

**通道（Channel）：**由java.nio.channels包定义，Channel表示IO源与目标打开的连接，不同于传统的流，通道本身不能直接访问数据，配合缓冲区进行数据传输

`read(Buffer buf)`：读通道的数据到缓冲区中

`write(Buffer buf)`：写缓冲区的数据到通道中

**通道的主要实现类：**

- FileChannel：从文件中读写数据
- SocketChannel：能通过TCP读写网络中的数据。
- ServerSocketChannel：可以监听新进来的TCP连接，像Web服务器那样。对每一个新进来的连接都会创建一个SocketChannel
- DatagramChannel：能通过UDP读写网络中的数据。

> **获取通道**

- `getChannel()`：
  - FileInputStream/FileOutputStream
  - RandomAccessFile
  - Socket
  - ServerSocket
  - DatagramSocket
- 各个通道的静态方法`open()`
- Files工具类的`newByteChannel()`

```java
//复制1.jpg为2.jpg
public class Test{
    //非直接缓冲区完成文件复制
    @Test
    public void test1() throws Exception{
        FileInputStream fis = new FileInputStream("1.jpg");
        FileOutputStream fos = new FileOutputStream("2.jpg");
        
        //获取通道
        FileChannel inChannel = fis.getChannel();
        FileChannel outChannel = fos.getChannel();
        
        //创建缓冲区
        ByteBuffer buf = ByteBuffer.allocate(1024);
        
        //从通道读取数据到缓冲区中
        while(inChannel.read(buf) != -1){
            buf.flip();
            //将缓冲区的数据写入通道
            outChannel.write(buf);
            buf.clear();
        }
        outChannel.close();
        inChannel.close();
        fos.close();
        fis.close();
    }
    
    //通过直接缓冲区完成文件复制（内存映射文件）
    @Test
    public void test2() throws Exception{
        //获取通道
        FileChannel inChannel = FileChannel.open(Paths.get("1.jpg"), StandardOpenOption.READ);
        FileChannel outChannel = FileChannel.open(Paths.get("2.jpg"), StandardOpenOption.WRITE, StandardOpenOption.WRITE, StandardOpenOption.CREATE);
        
        //内存映射文件
        MappedByteBuffer inMappedBuf = inChannel.map(MapMode.READ_ONLY, 0, inChannel.size());
        MappedByteBuffer outMappedBuf = inChannel.map(MapMode.READ_WRITE, 0, inChannel.size());
        
        byte[] bytes = new byte[inMappedBuf.limit()];
        inMappedBuf.get(bytes);
        outMappedBuf.get(bytes);
        
        inChannel.close();
        outChannel.close();
    }
    
    @Test
    public void test3() throws Exception{
        
    }
}
```

## 3.分散读取与聚集写入

**分散读取：**

将Channel中的数据分散到各个缓冲区中。按照缓冲区的顺序，用从Channel中读取到的数据依次填满Buffer

首先将buffer插入到数组，然后再将数组作为`channel.read()` 的输入参数。`read()`方法按照buffer在数组中的顺序将从channel中读取的数据写入到buffer，当一个buffer被写满后，channel紧接着向另一个buffer中写。Scattering Reads在移动下一个buffer前，必须填满当前的buffer

**聚集写入：**

将多个缓冲区中的数据聚集到通道中。按照缓冲区顺序写入position和limit之间的数据到Channel

buffer的数组是`write()`方法的入参，`write()`方法会按照buffer在数组中的顺序，将数据写入到channel

```java
@Test
public void test() throws Exception{
    RandomAccessFile raf1 = new RandomAccessFile("1.txt","rw");
    //获取通道
    FileChannel channel1 = raf1.getChannel();
    //分配指定大小的缓冲区
    ByteBuffer buf1 = ByteBuffer.allocate(100);
    ByteBuffer buf2 = ByteBuffer.allocate(1024);
    
    //分散读取
    ByteBuffer[] bufs = {buf1,buf2};
    channel1.read(bufs);
    
    for(ByteBuffer byteBuffer : bufs){
        byteBuffer.flip();
    }
    System.out.println(new String(bufs[0].array(), 0, bufs[0].limit()));
    System.out.println("-----------");
    System.out.println(new String(bufs[1].array(), 0, bufs[1].limit()));
    
    //聚集写入
    RandomAcessFile raf2 = new RandomAccessFile("2.txt", "rw");
    FileChannel channel2 = raf2.getChannel();
    channle2.write(bufs);
    
    channel1.close();
    channel2.close();
    raf1.close();
    raf2.close();
}
```

## 4.Charset

**编码：**字符串--->字节数组

**解码：**字节数组--->字符串

```java
@Test
public void test()  throws CharacterCodingException{
    //创建Charset
    Charset cs = Charset.forName("GBK");
    //获取编码器
    CharsetEncoder ce = cs.newEncoder();
    //获取解码器
    CharsetDecoder cd = cs.newDecoder();
    
    CharBuffer cBuf = CharBuffer.allocate(1024);
    cBuf.put("你好，世界");
    cBuf.flip();
    
    //编码
    ByteBuffer bBuf = ce.encode(cBuf);
    for(int i = 0; i < bBuf.limit(); i++){
        System.out.println(bBuf.get());
    }
    bBuf.flip();
    //解码
    CharBuffer cBuf2 = cd.decode(bBuf);
    for(int i = 0; i< cBuf.limit(); i++){
        System.out.println(cBuf2.get());
    }
}
```

## 5.阻塞与非阻塞

阻塞与非阻塞是针对于网络IO

**阻塞模式：**当客户端发送连接请求时，会占用服务器端的线程，因为无法得知客户端是否上传完毕，该线程会一直被占用，导致服务器端线程资源浪费

**非阻塞式：**通过选择器对客户端的请求状态进行持续监控，直到客户端的状态满足监听状态后为客户端请求分配线程

###5.1.阻塞式

```java
public class Test{
    @Test
    public void client() throws Exception{
        //获得通道
        SocketChannel socket = SocketChannel.open(new InetSocketAddress("127.0.0.1",9999));
        
        FileChannel inChannel = FileChannel.open(Paths.get("1.jpg"), StandardOpenOption.READ);
        //分配缓冲区
        ByteBuffer buf = ByteBuffer.allocate(1024);
        //发送数据
        while(inChannel.read(buf) != -1){
            buf.flip();
            socket.write(buf);
            buf.clear();
        }
        //数据发送完毕要关闭缓冲区，否则服务器线程会因为不知道客户端数据是否发送完毕而一直被占用
        soucket.shutdownOutput();
        
        //接受服务器端的响应
        int len = 0;
        while((len = socket.read(buf)) != -1){
            buf.flip();
            System.out.println(new String(buf.array(), 0, len));
            buf.clear();
        }
        inChannel.close();
        socket.close();
    }
    @Test
    public void Server() throws Exception{
		ServerSocketChannel server = ServerSocketChannel.open();
        FileChannel outChannel = FileChannel.open(Paths.get("2.jpg"), StandardOpenOption.WRITE, StandardOpenOption.CREATE);
        
        ByteBuffer buf = ByteBuffer.allocate(1024);
        //绑定端口号
        server.bind(new InetSocketAddress(9999));
        //获取客户端连接
        SocketChannel socket = server.accept();
        //接受客户端数据并保存在本地
        while(socket.read(buf) != -1){
            buf.flip();
            outChannel.write(buf);
            buf.clear();
        }
        //响应数据到客户端
        buf.put("数据上传完毕".getBytes());
        buf.flip();
        socket.write(buf);
        socket.close();
        server.close();
        outChannel.close();
    }
}
```

### 5.2.非阻塞式

```java
public class Test{
	@Test
    public void client() throws Exception{
        SocketChannel socket = SocketChannel.open(new InetSocketAddress("127.0.0.1",9999));
        //切换为非阻塞模式
        socket.configureBlocking(false);
        ByteBuffer buf = ByteBuffer.allocate(1024);
        Scanner scan = new Scanner(System.in);
        while(scan.hasNext()){
            buf.put(scan.next().getBytes());
            buf.flip();
            socket.write(buf);
            buf.clear();
        }
        scan.close();
        socket.close();
    }
    
    @Test
    public void server() throws Exception{
        ServerSocketChannel server = ServerSocketChannel.open();
        //切换为非阻塞模式
        server.configureBlocking(false);
        
        server.bind(new InetSocketAddress(9999));
        //获取选择器
        Selector selector = Selector.open();
        //注册选择器并设置线程监听状态
        server.register(selector, SelectionKey.OP_ACCEPT);
        
        while(selector.select() > 0){
            //获取选择器上的事件
            Iterator<SelectionKey> it = selector.selectedKeys().iterator();
            //遍历选择器上的事件
            while(it.hasNext()){
                SelectionKey key = it.next();
                if(key.isAcceptable()){
                    //若为“接受就绪”状态，获取客户端链接
                    SocketChannel socket = server.accept();
                    //切换为非阻塞状态
                    socket.configureBlocking(false);
                    //将该通道注册到选择器上
                    socket.register(selector,SelectionKey.OP_READ);
                }else if(key.isReadable()){
                    SocketChannel socket = (SocketChannel) key.channel();
                    ByteBuffer buf = ByteBuffer.allocate(1024);
                    int len = 0;
                    while((len = socket.read(buf)) > 0){
                        buf.flip();
                        System.out.println(new String(buf.array(), 0, len));
                        buf.clear();
                    }
                }
                //删除该事件
                it.remove();
            }
        }
        SocketChannel socket = server.accept();
        ByteBuffer buf = ByteBuffer.allocate(1024);
        System.out.println(socket.read(buf));
    }
}
```

