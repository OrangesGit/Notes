# innoDB介绍一下

innoDB是MySQL的数据库引擎之一，为MySQL的默认存储引擎，最大特色是支持ACID

## 什么是ACID

+ 原子性（Atomic）：事务中各项操作要么全做，要么不做，任何一项操作的失败都会导致整个事务的失败；
+ 一致性（Consistent）：事务结束后系统的状态是一致的；
+ 隔离性（Isolated）：并行执行的事务彼此是无法看到对方的中间状态；
+ 持久性（Durable）：事务完成后所做的改动都会被持久化，即使发生灾难性的失败，通过日志和同步备份可以在故障后重建数据；

# mysql索引介绍一下

索引是帮助MySQL**高效获取数据**的**数据结构**，类似于一本书前面的目录。

底层是B+树

## 使用B+树有什么好处？

- 数据量相同的情况下，B+树更矮胖，IO次数会减少
- B+树的查询必须最终找到叶子节点，而B树只需要找到匹配元素，B+树更稳定
- 范围查询中，B+树只需要在链表上做遍历即可，而B树只能靠中序遍历

[B+树资料](https://zhuanlan.zhihu.com/p/54102723)

## 最左匹配原则

如果有一个3列索引(col1,col2,col3)，实际上已经建立了三个联合索引(col1)、(col1,col2)、(col1,col2,col3)

如果不是按照这个规则来匹配那么就无法用到索引

# 主从复制

指定一个数据副本为主节点，当客户端写入数据时，必须将该请求发送至主节点，主节点会保存最新的数据，然后主角点再把对数据的更改发送到所有从节点

# 浏览器输入URL回车之后发生了什么

1. DNS（Domain Name System）解析（将域名转成IP地址）
2. 建立TCP连接（三次握手）
3. 发送HTTP请求
4. 服务器收到响应HTTP请求
5. 页面渲染（解析HTML、CSS和JSP文件）

# HTTP请求报文/响应报文

+ 请求行
+ 请求头部
+ 空行
+ 请求数据

---

+ 状态行
+ 响应头部
+ 空行
+ 响应数据

# TCP拥塞控制

[TCP拥塞控制资料](https://www.bilibili.com/video/BV1L4411a7RN?from=search&seid=17298520952506489388&spm_id_from=333.337.0.0)

假定条件：

1. 数据是单向传送由发送方发送TCP数据报文段到接收方，接收方只发送TCP确认报文段
2. 接收方总是由足够大的缓存空间，因此发送方发送窗口的大小由网络的拥塞程度来决定
3. 以TCP报文段的个数为讨论单位，而不是以字节为单位

发送方维护一个叫做**拥塞窗口cwnd**的状态变量，其值取决于网络的拥塞程度，并且动态变化

+ 拥塞窗口**cwnd的维护原则**：只要网络没有出现拥塞，拥塞窗口就再增大一些；只要网络一出现拥塞，拥塞窗口就减小一些
+ 判断出现**网络拥塞的依据**：没有按时收到应当到达的确认报文（即发生了重传）

维护一个**慢开始门限ssthresh**状态变量：

+ cwnd < ssthresh时，慢开始算法；
+ cwnd == ssthresh时，慢开始或者拥塞避免算法
+ cwnd > ssthresh时，拥塞避免

## 慢开始（slow-start）

最开始的时候cwnd为1，然后每次成功传输之后都翻倍，直到达到ssthresh的值



## 拥塞避免（congestion avoidance）

当cwnd == ssthresh时，cwnd每次都+1，直到发生拥塞（发生拥塞的判断机制是，发送方没有收到正常数量的确认报文段），之后将ssthresh设置为原来的一半，cwnd设置为1，重新进入到慢开始算法

> 有时，个别报文段会在**网络中丢失**，但实际未发生拥塞
>
> + 这将导致发送方超时重传，并误认为发生了拥塞
> + 此时发送方会错误启动慢开始算法，并发cwnd又设置为1，因此降低了传送效率

## 快重传（fast retransmit）

使发送方尽快进行重传，而不是等**超时计时器**超时再重传

+ 要求接收方不要等待自己发送数据时才捎带进行确认，而是要立即发送确认
+ 即使收到了失序的报文段也要立即发出对已收到报文段的**重复确认**
+ 发送方一旦收到**三个连续的重复确认**，就讲相应的报文段**立即重传**，而不是等超时计时器超时再重传

## 快恢复（fast recovery）

发送方一旦收到三个重复确认，就知道现在只是丢失了个别报文段，于是不启动慢开始算法，而是**执行快恢复算法**

+ **发送方将慢开始门限ssthresh值和拥塞窗口cwnd值调整为当前窗口的一半；开始执行拥塞避免算法**
+ 也有的快恢复算法是把快恢复开始时的cwnd值调整为ssthresh+3

# 什么是线程池？为什么要使用它？

[线程资料](https://mp.weixin.qq.com/s/HWoyHgZiUz7pqFhYxfLDNA)

创建线程要话费昂贵的资源和时间，如果任务来了才创建线程那么响应时间会变长，而且一个线程能创建的线程数有限，为了避免这些问题，在程序启动的时候就创建若干线程来相应处理，他们被称为线程池，里面的线程叫做工作线程。

好处如下：

1. 降低资源消耗，避免了反复创建线程
2. 提高响应速度，当任务到达时，任务不需要等待线程创建就可以直接执行
3. 提高线程的可管理性，通过线程池可以统一管理

## 为什么要控制线程数？

在JVM中每创建一个线程就需要调用操作系统提供的API创建线程，并赋予资源，并且销毁线程同样也需要系统调用，而系统调用就意味着上下文切换等开销，并且线程也是需要占用内存的，而内存是宝贵的资源。

# 线程池的排队策略

+ 如果运行的线程少于corePoolSize，则Executor始终首选添加新的线程，而不进行排队
+ 如果运行的线程等于或者多余corePoolSize，则Executor始终首选将请求加入队列，而不是添加新线程
+ 如果无法将请求加入队列，则队列已经满了，此时则创建新的线程，如果创建次线程超出maximumPoolSize，则拒绝此任务

# 设计模式

## 单例模式

定义一个类只能有一个实例存在

### 应用场景

比如在某个服务器中，该服务器的配置信息存放在一个文件中，这些配置数据由一个单例对象读取，服务进程中的其他对象可以直接通过这个单例对象获取这些配置信息。

### 步骤

+ 将构造方法定义为private方法，这样其他地方的代码就无法通过代码实例化该对象，只有通过该类提供的静态方法调用
+ 在类内部提供一个静态方法getInstance()，获取该对象

### 懒汉式（非线程安全）

```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
  
    public static Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
}
```

### 懒汉式（线程安全）

```java
/**
 * 多线程环境下的懒汉式单例模式(DCL，双检锁+volatile实现)
 * 加入了volatile变量来禁止指令重排序  
 * @author ywq
*/
class Single{ 
    private static volatile Single s = null; 
    private Single(){} 
 
    public static  Single getInstance(){
        if(null==s){
            synchronized(Single.class){
                if(null==s) 
                    s = new Single(); 
            }
        }
        return s; 
    } 
}
```

### 饿汉式

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}
```

## 工厂模式

[工厂模式资料](https://www.bilibili.com/video/BV1CF411h7ES?from=search&seid=4483846560152526447&spm_id_from=333.337.0.0)

### 简单工厂模式

缺点：

1. 使用简单工厂模式会增加系统中类的个数，一定程度上增加了系统的复杂程度和理解难度
2. 系统拓展困难，一旦添加产品就不得不修改工厂逻辑，在产品类型较多的时候有可能造成工厂逻辑复杂
3. 违背了开闭原则（对拓展开放，对修改关闭）

#### 一个虚拟接口

```java
public interface Car {
     public void createCar();
}
```

#### 一个工厂类

```java
public class FactoryCar {
    public void productCar(String type){
        if(type.equals("Benz")){
            Benz benz = new Benz();
            benz.createCar();;
        }
        else{
            BWM bwm = new BWM();
            bwm.createCar();
        }
    }
}
```

#### 几个具体实现类

```java
public class Benz implements Car{
    @Override
    public void createCar() {
        System.out.println("生产奔驰");
    }
}
// ------------------------------------------------------------
public class BWM implements Car {

    @Override
    public void createCar() {
        System.out.println("生产宝马");
    }
}
```

#### 主函数

```java
public class Main{

    public static void main(String[] args) throws Exception
    {
        FactoryCar factoryCar = new FactoryCar();
        factoryCar.productCar("Benz");
        factoryCar.productCar("BWM");
    }
}
```

### 工厂方法模式

好处在于，如果需要添加产品，只需要做如下操作：

1. 具体工厂类实现抽象工厂接口
2. 具体产品类继承具体工厂类，并实现抽象产品接口

#### 两个虚拟接口（虚拟对象和虚拟工厂）

```java
public interface Car {
    void prection();
}
// ------------------------------------------------------------
public interface CarFactory {
    void carFactory();
    void productCar();
}
```

#### 几个具体的工厂类

```java
public class FactoryBaoma implements CarFactory {
    @Override
    public void carFactory() {
        System.out.println("这是宝马汽车工厂");
    }

    @Override
    public void productCar() {
        System.out.println("宝马工厂正在生产宝马汽车");
    }
}
// ------------------------------------------------------------

public class FactoryBenchi implements CarFactory {
    @Override
    public void carFactory() {
        System.out.println("这是奔驰汽车工厂");
    }

    @Override
    public void productCar() {
        System.out.println("奔驰工厂正在生产奔驰汽车");
    }
}

```

#### 几个具体的被生产对象

```java
public class BaoMa extends FactoryBaoma implements Car{

    @Override
    public void prection() {
        System.out.println("这是奔驰汽车");
    }
}
// ------------------------------------------------------------
public class BenChi extends FactoryBaoma implements Car{
    @Override
    public void prection() {
        System.out.println("这是宝马汽车");
    }
}

```

#### 主函数

```java
public class Main{

    public static void main(String[] args) throws Exception
    {
        BenChi bc = new BenChi();
        bc.prection();
        bc.carFactory();
        bc.productCar();
    }
}
```

