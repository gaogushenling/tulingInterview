# 什么是可重入锁ReentrantLock

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">可重入锁（ReentrantLock）是一种支持重入的锁机制，也被称为递归锁。重入锁是指同一个线程可以多次获得同一个锁而不会发生死锁。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">具体来说，当一个线程获取了可重入锁后，可以多次对该锁进行加锁操作，每次加锁都要对应一次解锁操作。而对于其他线程来说，只有当获取到该锁后，才能进行加锁操作。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">在Java中，ReentrantLock是可重入锁的一个具体实现。与Synchronized关键字相比，ReentrantLock提供了更多的功能和灵活性，比如可设置公平性、可定时、可中断等。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">可重入锁的一个重要特性是，同一个线程可以对同一个锁多次调用lock()方法，而每次调用必须要对应一次unlock()方法，否则其他线程无法获取到锁。这种机制保证了线程在获取锁后，能够多次进入同步代码块，从而避免了死锁的发生。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">可重入锁对于递归调用非常有用，当一个线程在执行方法A的过程中，需要调用方法B，而方法B又需要获取同一个锁时，可重入锁能够将这个操作合理地进行处理，使得方法B可以成功获取锁而不产生死锁。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">总结：可重入锁是一种支持重入的锁机制，同一个线程可以多次获取同一个锁而不会发生死锁。在Java中，ReentrantLock是可重入锁的一个具体实现，通过它可以实现更灵活和精细的锁控制。</font>



> 更新: 2023-08-24 21:14:04  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/rmhg9t2n32852v4a>