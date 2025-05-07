# 说一下Spring的事务传播行为

<font style="color:rgb(51, 51, 51);">事务的传播特性指的是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行？ </font>

<font style="color:rgb(36, 41, 47);"> Spring框架提供了多种事务传播行为：</font>

1. **<font style="color:rgb(36, 41, 47);">REQUIRED</font>**<font style="color:rgb(36, 41, 47);">：如果当前存在事务，则加入该事务，如果当前没有事务，则创建一个新的事务。这是最常用的传播行为，也是默认的，适用于大多数情况。</font>
2. **<font style="color:rgb(36, 41, 47);">REQUIRES_NEW</font>**<font style="color:rgb(36, 41, 47);">：无论当前是否存在事务，都创建一个新的事务。如果当前存在事务，则将当前事务挂起。适用于需要独立事务执行的场景，不受外部事务的影响。</font>
3. **<font style="color:rgb(36, 41, 47);">SUPPORTS</font>**<font style="color:rgb(36, 41, 47);">：如果当前存在事务，则加入该事务，如果当前没有事务，则以非事务方式执行。适用于不需要强制事务的场景，可以与其他事务方法共享事务。</font>
4. **<font style="color:rgb(36, 41, 47);">NOT_SUPPORTED</font>**<font style="color:rgb(36, 41, 47);">：以非事务方式执行，如果当前存在事务，则将当前事务挂起。适用于不需要事务支持的场景，可以在方法执行期间暂时禁用事务。</font>
5. **<font style="color:rgb(36, 41, 47);">MANDATORY</font>**<font style="color:rgb(36, 41, 47);">：如果当前存在事务，则加入该事务，如果当前没有事务，则抛出异常。适用于必须在事务中执行的场景，如果没有事务则会抛出异常。</font>
6. **<font style="color:rgb(36, 41, 47);">NESTED</font>**<font style="color:rgb(36, 41, 47);">：如果当前存在事务，则在嵌套事务中执行，如果当前没有事务，则创建一个新的事务。嵌套事务是外部事务的一部分，可以独立提交或回滚。适用于需要在嵌套事务中执行的场景。</font>
7. **<font style="color:rgb(36, 41, 47);">NEVER</font>**<font style="color:rgb(36, 41, 47);">：以非事务方式执行，如果当前存在事务，则抛出异常。适用于不允许在事务中执行的场景，如果存在事务则会抛出异常。</font>

<font style="color:rgb(36, 41, 47);">通过@Transactional注解的propagation属性来指定事务传播行为 。</font>



> 更新: 2023-09-06 16:52:06  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ccreslu5lwn4tgrx>