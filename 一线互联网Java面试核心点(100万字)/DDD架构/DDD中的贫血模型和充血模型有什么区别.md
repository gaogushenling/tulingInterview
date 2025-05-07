# DDD 中的贫血模型和充血模型有什么区别

<font style="color:rgb(5, 7, 59);">DDD中的贫血模型和充血模型都是领域模型的表现形式，但是它们在设计和实现上有着显著的区别。</font>**<font style="color:rgb(6, 6, 7);">主要区别在于领域对象是否包含业务逻辑</font>**<font style="color:rgb(6, 6, 7);">。</font>

#### <font style="color:rgb(5, 7, 59);">贫血模型（Anemic Domain Model）</font>
<font style="color:rgb(5, 7, 59);">是</font>**<font style="color:rgb(5, 7, 59);">面向过程</font>**<font style="color:rgb(5, 7, 59);">编程的一种表现形式。贫血模型的实体只包含数据属性和对应的 getter 以及 setter，而具体的业务逻辑交由服务层或其他外部组件负责。贫血模型将数据与操作分离，其好处是模型足够简单，开发上手比较快。团队内多人协作时，设计不容易变形。比较适合轻量级应用。但坏处是贫血模型的实体无法直接体现对应的业务能力，在复杂业务中，梳理业务逻辑将变得非常困难，不利于项目后期的业务演进。</font>

#### <font style="color:rgb(5, 7, 59);">充血模型（Rich Domain Model）</font>
<font style="color:rgb(5, 7, 59);">则是</font>**<font style="color:rgb(5, 7, 59);">面向对象</font>**<font style="color:rgb(5, 7, 59);">编程的一种表现形式。充血模型的实体通常包含了数据属性以及引起属性发生变化的核心业务动作。充血模型将数据与业务能力绑定在一起，非常符合业务人员的思考方式，因此其好处是对业务非常友好，有助于更好的封装和保护领域内部的业务规则，尤其适合业务复杂的应用场景。充血模型的坏处是对业务的熟悉程度要求很高，需要在上手之初就设计好针对不同的业务场景，设计好具体的模型实体，并且规划好需要暴露哪些操作，定义哪些业务逻辑。而这些在贫血模型中则不需要，可以边实现边修改。</font>



<font style="color:rgb(5, 7, 59);">如果是面试比较，或者区别可从优缺点出发：</font>

### <font style="color:rgb(6, 6, 7);">1. 贫血模型（Anemic Domain Model）</font>
<font style="color:rgb(6, 6, 7);">贫血模型是一种反模式，其特点如下：</font>

+ **<font style="color:rgb(6, 6, 7);">数据与逻辑分离</font>**<font style="color:rgb(6, 6, 7);">：领域对象仅包含数据和属性的访问方法（如getter和setter），而所有业务逻辑都集中于服务层</font><font style="color:rgb(6, 6, 7);">。</font>
+ **<font style="color:rgb(6, 6, 7);">对象职责单一</font>**<font style="color:rgb(6, 6, 7);">：领域对象只作为数据容器，不包含任何业务行为</font><font style="color:rgb(6, 6, 7);">。</font>
+ **<font style="color:rgb(6, 6, 7);">优点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">符合单一职责原则，领域对象只负责存储数据</font><font style="color:rgb(6, 6, 7);">。</font>
    - <font style="color:rgb(6, 6, 7);">逻辑与数据分离，便于建立清晰的分层架构</font><font style="color:rgb(6, 6, 7);">。</font>
+ **<font style="color:rgb(6, 6, 7);">缺点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">违背面向对象设计的核心思想，导致领域对象缺乏行为</font><font style="color:rgb(6, 6, 7);">。</font>
    - <font style="color:rgb(6, 6, 7);">业务逻辑分散在服务层，可能导致代码难以维护和测试</font><font style="color:rgb(6, 6, 7);">。</font>

### <font style="color:rgb(6, 6, 7);">2. 充血模型（Rich Domain Model）</font>
<font style="color:rgb(6, 6, 7);">充血模型是一种更符合面向对象设计原则的模型，其特点如下：</font>

+ **<font style="color:rgb(6, 6, 7);">数据与逻辑结合</font>**<font style="color:rgb(6, 6, 7);">：领域对象不仅包含数据，还封装了与数据相关的业务逻辑</font><font style="color:rgb(6, 6, 7);">。</font>
+ **<font style="color:rgb(6, 6, 7);">对象自治</font>**<font style="color:rgb(6, 6, 7);">：领域对象能够自主管理自己的状态和行为，业务逻辑与数据紧密结合</font><font style="color:rgb(6, 6, 7);">。</font>
+ **<font style="color:rgb(6, 6, 7);">优点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">更好地遵循面向对象的设计原则，如封装和单一职责</font><font style="color:rgb(6, 6, 7);">。</font>
    - <font style="color:rgb(6, 6, 7);">业务逻辑集中于领域对象，便于维护和测试</font><font style="color:rgb(6, 6, 7);">。</font>
    - <font style="color:rgb(6, 6, 7);">更适合复杂业务场景，能够更好地表达业务规则</font><font style="color:rgb(6, 6, 7);">。</font>
+ **<font style="color:rgb(6, 6, 7);">缺点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">领域对象可能变得复杂，尤其是当业务逻辑较多时</font><font style="color:rgb(6, 6, 7);">。</font>
    - <font style="color:rgb(6, 6, 7);">如果设计不当，可能导致领域对象违反单一职责原则</font><font style="color:rgb(6, 6, 7);">。</font>

### <font style="color:rgb(6, 6, 7);">选择建议</font>
+ **<font style="color:rgb(6, 6, 7);">贫血模型</font>**<font style="color:rgb(6, 6, 7);">适合简单业务逻辑或需要清晰分层的系统设计</font><font style="color:rgb(6, 6, 7);">。</font>
+ **<font style="color:rgb(6, 6, 7);">充血模型</font>**<font style="color:rgb(6, 6, 7);">更适合复杂业务场景，能够更好地封装业务逻辑，保持系统的灵活性和可维护性。</font>

<font style="color:rgb(5, 7, 59);"></font>

<font style="color:rgb(5, 7, 59);">总的来说，贫血模型更注重简单性和易上手，而充血模型更注重业务复杂的系统开发。选择使用哪种模型取决于具体的业务需求和开发团队的技术能力。</font>

<font style="color:rgb(6, 6, 7);">在实际开发中，选择哪种模型应根据系统的复杂度、业务需求和团队的技术偏好综合考虑。</font>



> 更新: 2025-02-19 13:55:56  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/mq2vkccbiqgss7kg>