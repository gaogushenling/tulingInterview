# synchronized的实现原理

**synchronized是Java语言中最基本的线程同步机制，它通过互斥锁来控制线程对共享变量的访问。**

具体实现原理如下：

1. synchronized的实现基础是对象内部的锁（也称为监视器锁或管程），每个锁关联着一个对象实例。
2. 当synchronized作用于某个对象时，它就会尝试获取这个对象的锁，如果锁没有被其他线程占用，则当前线程获取到锁，并可以执行同步代码块；如果锁已经被其他线程占用，那么当前线程就会阻塞在同步块之外，直到获取到锁才能进入同步块。
3. synchronized还支持作用于类上，此时它锁住的是整个类，而不是类的某个实例。在这种情况下，由于只有一个锁存在，所以所有使用该类的线程都需要等待锁的释放。
4. 在JVM内部，每个Java对象都有头信息，其中包含了对象的一些元信息和状态标志。synchronized通过修改头信息的状态标志来实现锁的获取和释放。
5. synchronized还支持可重入性，即在同一个线程中可以多次获取同一个锁，这样可以避免死锁问题。
6. Java虚拟机会通过锁升级的方式来提升synchronized的效率，比如偏向锁、轻量级锁和重量级锁等机制，使得在竞争不激烈的情况下，synchronized的性能可以达到与非同步代码相当的水平。



> 更新: 2023-09-05 21:26:33  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/uou4g1qccpxzmzow>