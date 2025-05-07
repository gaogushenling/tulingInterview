# Mysql默认RR，大厂为啥要改成RC

<font style="color:rgba(0, 0, 0, 0.82);">在许多大规模应用程序和互联网公司中，选择将 MySQL 的默认隔离级别从 Repeatable Read (RR) 调整为 Read Committed (RC) 有几个重要原因，主要是为了在特定业务场景下优化性能和提升系统的并发处理能力。以下是一些关键点：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">更高的并发性能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在 RC 隔离级别下，读操作不需要持有锁，从而减少了锁争用，特别是在高并发环境下可以显著提升系统的吞吐量。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">RC 只需要更少的锁定和版本控制开销，因为它允许“不可重复读”，无需像 RR 一样为保证可重复读而加持多版本控制。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">降低锁等待与死锁概率</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">RC 级别避免了由于间隙锁而产生的锁等待和死锁问题，尤其是在频繁进行插入、更新操作时，这能降低锁竞争带来的风险。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">由于减少了锁的范围和锁的保留时间，系统线程间等待时间缩短，死锁概率相对更低。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">较弱的隔离级别适应性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">网络应用通常更倾向于性能优化，对强一致性的要求不如事务型应用高。RC 已经能够满足大多数Web应用所需的读一致性。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">这与互联网公司快速响应和高并发需求更加契合。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">简化应用架构</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用 RC 可以简化应用程序设计，因为应用程序开发人员不需要处理因RR导致的复杂锁定问题。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">适用于需要快速开发、快速迭代的应用场景。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">数据库和缓存系统的协同</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">许多大厂在架构设计中结合使用缓存系统，缓存系统缓解了数据库的一些一致性压力，让选择更高效处理的数据读取变得可行。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">RC 降低了数据库负担，而在外层使用缓存保证某种程度的最终一致性。</font>
6. **<font style="color:rgba(0, 0, 0, 0.82);">减少事务开销</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在 RC 模式下，数据库系统处理事务的开销相对较低，事务可以更快速的提交和回滚。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">提高响应速度对电商网站等对响应时间敏感的应用尤其重要。</font>

<font style="color:rgba(0, 0, 0, 0.82);">选择在大厂中将隔离级别调整为 RC 是经过性能测试和业务场景验证的结果。在这样的决策中，公司重点考虑了性能优化的需求以及可以接受的隔离性降低所造成的数据一致性影响。最终的决定往往取决于具体的应用特性和业务需求。在某些事务的关键部分，仍然可以通过在代码中明确指定隔离级别来使用更高级别的隔离性。</font>



> 更新: 2024-08-08 14:48:57  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/oo0qdoe3i554vygr>