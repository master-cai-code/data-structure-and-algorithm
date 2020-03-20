2020.3.20    22:12

## 1. hashCode和equals

       1.hashCode用来计算对象物理地址在hash表上的“下标地址”，分组管理，可以减少比较次数
       2.在Object类中，equals方法是和“==”一样用来判断地址是否相等，有时需要根据内容相等则对象相等（重写equals方法）
       3.判断一个对象是否相等，先比较hashCode是否相等，再看equals是否相等，两个都相等则两对象相等.
       4.若重写equals方法，而不重写hashCode，也会认为对象不相等，所以也要重写hashCode
       5.重写了两个方法后，equals方法相等，hashCode一定相等；  hashCode相等不一定equals相等

       尚未解决： hashCode如何重写???
   
   
## 2. hashMap
       hash用来找存储地址；map是存储的元素类型
       
       数组查找O(1)；会产生hash冲突，链地址法（拉链法）用到链表；链表查找O(n),效率低--->红黑树
       
       key可以为null，此时hash值为0，但只允许一个为null，值可以不唯一可以多个null
       当总的元素个数size>=capacity*loadfactor,两倍扩容

       按位与(&)、按位或(|)、按位异或(^)、取反(~)
       &:都是1才为1
       |：都是0才是0
       ^:不同为1，相同为0 （不进位加法）
       取模运算代价高于位运算，但hashCode%length == hashCode&（length-1） 所以要为2的幂次方 （✔）

   
  ### 3. jdk 1.7 ： 数组+链表
    1.头插法（不带头结点的单链表）会造成死循环
       原因：两个线程先后倒序元素，形成环
    2.扩容后新hash是和新数组长度按位与
       
  ### 4. jdk 1.8 ：数组+链表+红黑树   链表长度达到8变为红黑树，应为按照泊松分布，达到8的概率是一亿分之六
    1.尾插法   不会出现逆序
    2.扩容后   可能仍是原位置或者原位置后移一个老数组的长度
    
  
## 5. 多线程的调度是随机的，抢占CPU是随机的，时间片完了进行下一轮的抢占（导致每次运行顺序不一致，重新占用CPU后，接着上次的地方运行）
    与start()先后位置无关
    
   ### 5.1 hashMap是线程不安全的，方法都没加锁（例如put（）方法，size++问题等）
   
   ###  5.2 ConcurrentHashMap是线程安全的，Collections.synchronizedMap也行
         ★ConcurrentHashMap使用Unsafe类中的native本地方法CAS（CompareAndSwapxxx）：内存地址的值和期望值是否相等，相等就用新值更新
           
           CAS让拿值和写入修改值同时成功或同时失败，失败自旋（重新到内存中拿新的值，再来操作，再来CAS比较）不断重复...直到成功交换
           
           volatile解决了内存可见性和禁止指令重排，CAS解决了原子性
           
           CAS会有   1. ABA问题，解决：带版本号的原子引用AtomicStampedRefence<V>
                    2. 长时间自旋，CPU开销很大
                    
           put操作时锁的是数组中的一个节点???(说法不确定）
       
     
     
     
     
       
 ## synchronized 和 reentrantLock
  ### 1.synchronized 原理       jvm层面      非公平锁
       1.1 对象的对象头当中有个MarkWord部分，该部分记录了对象和锁的有关信息
       1.2 同一时刻只有一个线程可以获取监视器（monitor）
       1.3 编译后同步代码块会形成monitorenter和monitorexit
       
       1.4.锁
        普通同步方法的synchronized，此对象代表当前的实例对象（this）
        静态同步方法.此对象代表当前的类.即                 （xx.class）
        同步方法块此对象代表你指定的对象。                 （obj）
   
 ### 2. AbstractQueuedSynchronizer，这就是传说中的AQS。                       
      
