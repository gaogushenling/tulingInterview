# JVM对象分配内存如何保证线程安全

<font style="color:rgba(0, 0, 0, 0.82);">在JVM中，为对象分配内存的过程需要确保线程安全，因为在多线程环境下，多个线程可能会同时尝试创建对象。为了保证内存分配的线程安全性，JVM采用了以下几种机制和技术：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">TLAB（Thread Local Allocation Buffer）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">概念</font>**<font style="color:rgba(0, 0, 0, 0.82);">：TLAB是一种每个线程私有的内存区域，用于加速对象内存的分配。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">作用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过为每个线程分配一个独立的小内存块，线程可以在这个块中直接分配对象，而不需要对共享堆进行同步。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">原理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：当一个线程需要分配对象时，首先会尝试在TLAB中进行分配。如果TLAB有足够的空间，分配过程就是线程安全的，因为没有其他线程访问这个内存块。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">不足</font>**<font style="color:rgba(0, 0, 0, 0.82);">：当TLAB空间不足时，线程需要请求一个新的TLAB或者直接从共享堆中分配，这个过程需要一定的同步机制。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">CAS（Compare-And-Swap）机制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">概念</font>**<font style="color:rgba(0, 0, 0, 0.82);">：CAS是一种硬件提供的原子操作，用于无锁（Lock-free）并发编程。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">作用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：JVM在需要直接操作共享堆时，可以使用CAS来实现原子性地更新内存分配指针。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">执行过程</font>**<font style="color:rgba(0, 0, 0, 0.82);">：CAS操作检查一个变量是否等于预期值，如果是，则更新为新值。这种操作是原子的，避免了需要锁机制带来的额外开销。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">应用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：当TLAB耗尽或在涉及到跨线程的堆内存分配时，CAS有效避免了竞争条件。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">分代收集</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">背景</font>**<font style="color:rgba(0, 0, 0, 0.82);">：虽然不是直接用于线程安全，但分代收集（年轻代、老年代、永久代/元空间）使得内存管理更高效，减少了直接竞争的机会。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">结合</font>**<font style="color:rgba(0, 0, 0, 0.82);">：TLAB一般对年轻代的内存分配进行优化，更加局部化的内存管理有助于线程安全。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过运用这些机制，JVM能够在多线程环境下高效而安全地进行内存分配，并最大限度地减少同步操作带来的性能损耗。这样设计不仅提升了性能，也保证了对象内存分配的安全性和一致性。</font>



> 更新: 2024-08-11 15:16:52  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xi757wrpuahlnrgg>