# MyBatis如何处理懒加载和预加载？

当谈到MyBatis中的懒加载和预加载时，我们实际上在讨论在获取数据库数据时如何处理关联对象的加载方式。



**懒加载**是一种延迟加载技术，它在需要访问关联对象的时候才会加载相关数据。这意味着，当你从数据库中获取一个主对象时，它的关联对象并不会立即加载到内存中，只有当你实际调用访问关联对象的方法时，MyBatis才会去数据库中加载并填充这些关联对象的数据。懒加载适用于关联对象较多或者关联对象数据较大的情况，这样可以减少不必要的数据库查询，提升性能。



**预加载**则是一种在获取主对象时同时加载其关联对象的技术。这样一来，当你获取主对象时，它的所有关联对象也会被一并加载到内存中，避免了多次数据库查询。预加载适用于你确定在后续使用中肯定会访问关联对象，这样可以减少每次访问关联对象时的延迟。



选择懒加载还是预加载取决于你的具体需求和场景。如果你希望在尽量少的数据库查询次数下获取数据，懒加载是个不错的选择。如果你在获取主对象后会频繁地访问其关联对象，预加载可能更适合，因为它可以减少多次查询带来的性能开销。



两者都是优化数据库访问性能的手段，根据具体的使用场景选择合适的加载方式非常重要。



> 更新: 2023-08-29 16:36:35  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/gd0ut67yyz0k60ni>