# 多线程
## 1.多线程概念
是进程中单个顺序控制流（执行路径），是一条单独执行的路径。

JVM java虚拟机 本质上是一个 进程。该进程可以叫做虚拟机进程。

虚拟机可以有多个。一个java程序就需要一个java虚拟机实例。（例如：在eclipse中运行一个程序，在myeclipse中也运行一个程序）

迅雷下载是一个很好的例子。
>  注：在eclipse中，主方法运行的是主线程，然后一个垃圾回收器线程。  

## 2.声明方式
**写一个线程类，继承线程，在类方法中重写run方法(线程运行时将自动调用run方法里的程序），在主函数中构建对象，启用start（）方法。**
> 注：start方法有开启后有时间间隔，并不是立即执行。
>  >  当run方法结束后，将自动销毁线程，若main函数主线程结束后，还有其他线程，并不会结束这些线程。

```
public class ThreadDemo2 {

public static void main(String[] args) {
	MyThread myThread = new MyThread();
	myThread.setName("周杰伦");
	MyThread myThread1 = new MyThread();
	myThread1.setName("张学友");
	//如果调用run方法，仅仅是之前学的方法调用。
	//myThread.run(); 
	//启动一个线程。是不是？不是run方法而是 p.start();
	//start启动一个线程之后，该线程上的代码是不是立即执行了？
	//并不是。start仅仅表示我启动一个线程，表示这个线程已经准备好了，可以被cpu调度了。
	//但是cpu会不会立即调度到该线程，是需要cpu来决定。
	myThread.start();
	myThread1.start();
	//String name = myThread.getName();

	//在Main方法内，如何获取main方法所在的主线程的名字
       //先获取main方法所在的主线程
	
	//获取当前的线程的引用。在哪里执行就是得到那个线程。
	
	//主线程内执行打印100个数
	/*Thread currentThread = Thread.currentThread();
	String name2 = currentThread.getName();
	 
    for(int i=0;i<100;i++){

		System.out.println(name2+"  :"+i);
	}
    
	System.out.println( "over" );*/
	
	//线程结束：
	//线程什么时候结束？
	//很多人觉得线程启动之后，就一直在那。
	//实际情况是线程start之后，会等待cpu调度，等到线程的run方法执行完毕，说明线程已经把需要完成的事情做完。
	//线程的run方法执行完毕之后，线程就退出了。
	//主线程的结束：是main方法执行结束
	
	//当主线程结束之后，子线程是否还会继续运行？
	//主线程退出之后，如果还有除垃圾回收之外的子线程在执行，则jvm不会退出。会继续执行。
	//主线程跟子线程没有从属关系。
}
//1.一种方法是将类声明为 Thread 的子类。
//2.该子类应重写 Thread 类的 run 方法。
class MyThread extends Thread{
String name;
@Override
public void run() {  //run方法里写什么代码？这里就写你希望放在一个单独的线程内执行的代码。
	//获取一个线程名字。在该线程内调用Thread的方法  getName()
	name = getName();
	//Thread  thread= Thread.currentThread();
   for(int i=0;i<100;i++){
		System.out.println(name+":"+i);
}
 	}
/* public void run() {
 }*/
/*@Override
public void run() {
 	}*/
}
```
> 注：若for循环中有i<100000,这个buffer里会满乘不了，导致输出结果只是部分

## 3.线程的主要方法
 
### 1.Thread currentThread =Thread.currentThread();
**这个可以直接获取到当前的线程实体**
> 值得一提的是，有个getname方法，这个方法是Thread里的，可以获取到thread的名字，也就是说thread类中有一个name属性  

### 2. static void sleep(long millis) 
由于是静态的，可以直接类名调用
           
```
Thread1 thread1 = new Thread1();

thread1.start();

try {
	thread1.join(1000);
} catch (InterruptedException e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
}
```
### 3.public static void yield()
> 线程礼让
yield() 礼让：表示让出CPU的执行权
只要yield执行，都会让出。cpu会重新选择线程调度。
cpu会随机再选一个线程，有机会再次选择到当前的线程。  

```
public void run() {
// TODO Auto-generated method stub
   for(int i=0;i<100;i++){
	   System.out.println(getName()+"吃第"+i+"个梨");  
	   Thread.yield();
   }
}		

```  

写在run中，由于两个线程是并发的，故没啥鸟用，鸡肋;yield可以使线程从运行转为就绪

**值得注意的是，这也是个static方法，故可以直接调用**  


### 4.public final void setDaemon(boolean on)
守护线程
> 守护线程需要在start（）之前写，需要将某个线程类实例化之后调用setDaemon（true）方法，在某个线程体内就是这个线程守护某个线程体
> 值得注意的是，当被守护者生命周期结束后，其守护者也将结束

*GC线程就是一个典型的守护线程，守护主线程*
```
Thread1 thread1 = new Thread1();
thread1.setDaemon(true);
thread1.start();
```
### 5.public void interrupt()
中断线程
好比某一个线程睡了五s钟，在2s钟被叫醒
```
Girl girl = new Girl();
girl.start();	    
try {
		Thread.sleep(2000);
	 } catch (InterruptedException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	 }
girl.interrupt();
public void run() {	
System.out.println("Girt start");    
try {
			sleep(5000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		    System.out.println("被叫醒了！");
		}	    
System.out.println("Girt end");

```
如果当前线程没有中断它自己（这在任何情况下都是允许的），则该线程的 checkAccess 方法就会被调用，这可能抛出 SecurityException

### 6.public final void join()
线程实例的方法join()方法可以使得一个线程在另一个线程结束后再执行。如果join()方法在一个线程实例上调用，当前运行着的线程将阻塞直到这个线程实例完成了执行。

## 3.线程的接口实现
一个类实现Runable接口，重写run方法
在主方法中实例化出一个对象，将此对象扔到Thread中
```
MyRunnable myRunnable = new MyRunnable();
	
Thread thread = new Thread(myRunnable);

```
#####  为什么仅仅一个类实现了Runnable接口就能实现线程功能？
答：
```
public interface Runnable {
    public abstract void run();
}
//在runnable接口中有一个run的抽象方法，在实现类中必须重写这个run方法
class Mask implements Runnable{
	public void run() {
		//代码实现
	}
}
//在主方法中实例化这个类，并将其扔到Thread中
Mask mask = new Mask();
Thread thread = new Thread(mask);
thread.start();
//进入Thread中观察到又这么两个方法
public Thread(Runnable target) {
    init(null, target, "Thread-" + nextThreadNum(), 0);
}

public void run() {
    if (target != null) {
        target.run();
    }
}

```
这就说明了mask这个实参代替形参target（其类型为Runnable），将mask这个对象（其父类**Runnable作为一个Thread的属性值**）扔进了Thread中，这样就可以实现线程并调用其重写的run方法

#### 总之，在用继承类写线程类时，可以直接用sleep（）和getname（）等其父类Thread中的方法，而在用接口实现线程类时，需要通过获取到当前线程（Thread.currentThread，静态的），再调用放法

## 4.线程安全
**核心概念就是多线程，有共享的资源，共享数据的访问跟修改之间 有可能被cpu打断**
***用同步代码块解决***

### 1.方式一：synchronized（对象）  
线程安全有三个   
1.多线程  
2.多个线程共享数据  
3.共享数据的访问跟修改之间有可能被cpu打断。
**这个syn的作用就是保证其代码块内部为原子操作，不可被打断**
>注：这个对象要为同一把锁  

**还可以这样做：**
```
public synchronized void sell(){
    //方法内容
}
```
注意此种默认的锁为（this），如果是三个对象引用这用方法，则会导致错误。  

### 2.方式二：Lock
lock是一个接口，要实现他的子类ReentrantLock，通过子类的lock（）和unlock（）方法来进行同步
```
lock.lock();
	if (tickets>0) {
		System.out.println(Thread.currentThread(). getName()+"正在出售第"+tickets+"张票");
	    tickets--;
	}
lock.unlock();
```
**此种方式简洁明了，值得推荐**

——————————————————————————————  
*对于类调用的一些感悟：*  
看手写笔记。

> 注：线程可以无限循环，不会导致cup崩溃，
原始是cpu可以随时挂起线程（若为进程，结果另说）

### 3.线程通信  
引入一个Object中的wait（）与nitify（）方法
**wait：当方法内有wait时，将时其隶线程进入阻塞状态，并放弃它的锁，知道另一个线程的notify唤起**  
**notify：不会放弃它的执行权，也不会放弃它的锁，直到运行完锁里的内容**  
> 注：1.当一个wait线程被唤醒时，并不会继续执行wait之后的内容，而是开始在锁处的起点运行

> 2.一个线程notify之后，将会继续执行它的锁内内容，执行完后，下一次cpu仍可能会调度其线程，只不过会限制在其wait中

问：为什么wait和notify是Object中的方法而不是Thread中？

**答：因为两个Thread要通过媒介进行通信，即一个object对象在两个线程中共有**  

```
Object o = new Object();
Thread h1 = new Mythread();
Thread h2 = new Mythread();
```
**若定义在Thread中，则两个Thread如何用同一把锁实现通信呢？**  

注：**进程通信一定是在进程同步的基础上，也就是在原子操作的基础上！因为好比wait要释放锁，若没有锁，如何释放？**

### 4.线程池 ExecutorService
声明一个线程池对象，将实现Runnable的一个类扔进线程池  
值得注意的是，当线程事务执行完之后，不会被销毁，也就是说后台Console不会结束  

### 5.定时器 Timer
**声明一个Timer类型的对象，将继承于TimerTask的类的实体扔进Timer对象中，调用schedule方法来实现功能，cancle结束Timer**


















