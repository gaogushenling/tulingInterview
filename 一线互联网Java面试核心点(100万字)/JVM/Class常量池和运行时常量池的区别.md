# Class常量池和运行时常量池的区别

### <font style="color:rgba(0, 0, 0, 0.82);">Class 常量池（Class Constant Pool）</font>
**<font style="color:rgba(0, 0, 0, 0.82);">定义</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">Class 常量池是Java类文件的一部分，它由编译器在编译Java源文件时生成，存储在.class文件中。它包含了类或接口的字面量（如字符串、整数常量等）以及符号引用（如类和接口的名字、字段和方法的名字及描述符）。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">特点</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">位于每个类文件的开头，是描述类或接口在Java平台上运行所需信息的重要部分。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">它是静态的，包含类文件中的所有依赖项，以便于类加载器在运行时解析。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">运行时常量池（Runtime Constant Pool）</font>
**<font style="color:rgba(0, 0, 0, 0.82);">定义</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">运行时常量池是Class常量池在类加载到JVM后的一种表现形式。它是类加载过程的一部分，在类或接口被载入JVM时，Class常量池的信息被载入运行时常量池。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">它在类加载时被创建，是方法区的一部分（在Java 8后部分实现为元空间的一部分）。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">特点</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">动态的，可以在运行时扩展，因为它不仅包含Class常量池的映射数据，还允许在运行时添加新的常量，例如通过字符串interning。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">用于解析类、方法、字段的引用，以及存储JVM执行所需的其他信息。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">关系</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">来源与转换</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">Class常量池是从Java编译器生成的静态数据结构，是.class文件的一部分。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">运行时常量池是JVM执行环境的一部分，是Class常量池在类加载时被解析、验证后存储的方法区中的数据结构。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">作用域与用途</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">Class常量池是在磁盘上文件级别的数据结构，定义了类的编译时依赖和信息。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">运行时常量池存在于内存中，在类加载期间被JVM转化和使用，维护符号引用的解析，动态链接和跨越生命周期的优化。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">使用与管理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">编译器生成Class常量池，它是只读的。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">运行时常量池在运行期间可以被动态更新，允许JVM对类执行管理和优化。</font>

<font style="color:rgba(0, 0, 0, 0.82);">总结来说，Class常量池和运行时常量池在Java的编译和执行阶段分别扮演着不同的角色。Class常量池是类文件的组成部分，而运行时常量池是JVM的执行环境结构，它将Class常量池的数据转换成JVM可以理解和使用的形式，并对其进行动态管理。</font>



> 更新: 2024-08-09 15:47:09  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/eipi6t9x8bueoqgl>