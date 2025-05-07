# MySQL为什么默认使用RR隔离级别

<font style="color:rgba(0, 0, 0, 0.82);">MySQL 默认使用 Repeatable Read (RR) 隔离级别主要是为了在性能与一致性之间取得一个较好的平衡。让我们详细探讨一下使用 RR 隔离级别的理由：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">一致性保证</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">RR 提供了较高的一致性保障。它保证在同一个事务中, 相同的 SELECT 查询结果是一致的，因为它通过使用快照确保查询结果的一致性。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在 MySQL 的 InnoDB 存储引擎中，RR 隔离级别还能防止幻读，这得益于使用间隙锁机制。这比 REPEATABLE READ 传统定义的行为有所提升。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">防止“幻读”</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">幻读是指当在一个事务中读取多行记录时，另一事务可能在这期间插入新的记录，从而导致前后两次读取结果不一致。在 MySQL 的 RR 隔离级别下，会使用间隙锁（next-key locking）来防止这种情况。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">性能权衡</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">虽然 SERIALIZABLE 提供了更强的一致性保障，但它会锁住读操作，极大地影响并发性能。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在大多数应用场景下，RR 提供的隔离性足够好，而相比于 SERIALIZABLE 来说，它允许更高的并发度。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">可重复读取</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在金融系统等需要强一致性的场景中，确保在同一事务中读取结果一致是非常关键的。但又要避免由于串行执行带来的性能问题，因此 RR 是一个不错的选择。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">锁机制的优化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">InnoDB 在实现 RR 隔离级别时进行了优化，竞争条件相对少，在多数情况下表现优异。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">通过多版本并发控制（MVCC），读操作不会被写操作阻塞，从而提升了读写性能。</font>
6. **<font style="color:rgba(0, 0, 0, 0.82);">广泛应用和标准</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">RR 是 SQL 标准之一，大多数数据库系统都实现了它，并且 MySQL 的实现经过多年的应用，已被证明是稳固、可靠和性能良好的。</font>
7. **<font style="color:rgba(0, 0, 0, 0.82);">综合考量</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">默认使用 RR 隔离级别可以简化开发过程，开发人员不必为达到更高的读一致性需求而在代码中显式更改事务隔离级别。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在 MySQL 环境中，对于大多数应用程序（尤其是 Web 应用），RR 已经能够满足常见的数据一致性需求，而不必承担使用 SERIALIZABLE 级别带来的额外性能损耗。</font>

<font style="color:rgba(0, 0, 0, 0.82);">综上所述，MySQL 选择 default 为 Repeatable Read 是为了在一致性和性能上找到一个合理的平衡点。RR 足以应对绝大部分场景，而同时支持较高的并发操作。</font>



> 更新: 2024-08-08 14:48:12  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/lug14uq7bkzgh858>