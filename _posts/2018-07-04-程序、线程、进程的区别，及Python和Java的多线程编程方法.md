---

title: 程序、线程、进程的区别，及Python和Java的多线程编程方法

layout: post

author: GYC

tags: Java Python 

---

前段时间，在做一个Python项目的时候，需要将窗口的线程和数据处理的线程分开处理，因此使用了Python的多线程管理；最近复习Java的时候再次接触到了Java的线程管理。温故知新，写下这篇关于线程的博客。


# 一、综述

### 什么是程序
程序是一段静态的代码，它是应用软件执行的蓝本。

### 什么是进程
进程是程序的一次动态执行过程，它对应了从代码加载、执行到执行完毕的一个完整过程，这个过程也是进程本身从产生、发展到消亡的过程。

### 什么是线程
线程是必进程更小的执行单位。一个进程在其执行过程中可以产生多个线程，形成多条执行线索，每条线索（即线程）都有它自身的产生，存在和消亡过程，也是一个动态的概念。

**总的来说，静态的程序运行得到动态的进程；进程包含了多个独立运行的线程。**

`参考《Java面向对象程序设计（第2版）》`

# 二、Python中的多线程 `threading`
假如我们想要实现一个功能，如下：

> 主程序while死循环
> 另一个子函数subFun(n)从0到n不断计数并输出

先写出来试试

``` Python
# coding:utf-8
def subFun(n):
    while True:
        for i in range(n):
            print("子线程："+str(i))

subFun(6)          #子程序从0到n循环并输出
while True:        #主程序whlie死循环
    print("主线程")
```

以上程序是否可行呢？
很明显，不可行！因为在subFun中有一个死循环，因此无法运行到主程序的while循环。

运行结果如图
![错误示例](https://img-blog.csdn.net/201807022139367?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5Nzk5NzY1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
由上可见，这两段造成了相互的干扰而无法同时运行。

&nbsp;
因此，要**使用多线程的管理**避免这种干扰的发生。

在python中，`threading`是管理多进程的包，通过`import threading`语句来使用这个包。

首先要为子函数构建一个线程`t1 = threading.Thread(target=subFun,args=(6,))`,其中参数target是要维护的线程函数名(注意没有圆括号)，args是函数的参数表。返回对该线程的引用。
然后要设置守护线程：`t1.setDaemon(True)`    
当你需要启动该线程时，使用start方法：`t1.start()`

完整的程序如下

```Python
# coding:utf-8

#导入线程管理包
import threading
#子线程
def subFun(n):
    while True:
        for i in range(n):
            print("子线程："+str(i))
#为subFun构建线程
t1 = threading.Thread(target=subFun,args=(6,))  
t1.setDaemon(True) #守护线程
t1.start() #启动线程
#主线程
while True:
    print("主线程")
```
运行结果如图：
![正确示例](https://img-blog.csdn.net/20180702215600173?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5Nzk5NzY1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 三、Java中的多线程`Thread`类
在Java中，每个Java程序都有一个默认的主线程，当程序进入到main方法后，会启动这个默认的主线程，该线程负责执行main方法。而如果需要创建子线程，就需要创建一个继承于Thread的子类。

```Java
public class SubThread extends Thread{
  ...
}
```
当Java虚拟机执行到该线程时，会调用`Thread`类中的`run()`方法，因此，我们要在子类中重写`run()`方法以规定子线程的具体使命。

```Java
public class SubThread extends Thread{
  public void run(){
    ... //该线程要做的事
  }
}
```
同样于上面的功能

> 主程序while死循环

> 另一个子函数subThread(n)从0到n不断计数并输出

用Java的实现如下：

```Java
public class main {
  public static void main(String[] args) {	
    
    SubThread  a  = new SubThread(6);  //新建子线程对象
    a.start();  //启动线程	
    //主线程
    while(true) {
      System.out.println("主线程");
    }
  }
}
class SubThread extends Thread{
  int n;
  public SubThread(int n) {  //构造方法
    this.n = n;
  }
  //重写Thread类中的run()方法
  public void run(){
    while(true) {
      for(int i=0;i<n;++i)
      System.out.println("子线程："+i);	
    }
  }
}
```


运行结果如下：
![Java多线程运行结果](https://img-blog.csdn.net/20180704112501431?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5Nzk5NzY1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

