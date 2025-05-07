# 什么是三色标记

<font style="color:rgba(0, 0, 0, 0.82);">三色标记法是一种用于垃圾回收算法中的对象标记方法，特别用于标记-清除型垃圾回收器。这种方法通过使用三种颜色（白色、灰色和黑色）来跟踪对象的可达性和垃圾回收状态，以避免对象的重复回收和丢失。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">三色标记的基本概念</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">白色</font>**<font style="color:rgba(0, 0, 0, 0.82);">：表示对象尚未被检查。白色对象可能是垃圾，直到证明它们是可达的。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">灰色</font>**<font style="color:rgba(0, 0, 0, 0.82);">：表示对象被检查过，并且其本身是可达的，但其引用的对象还未全部检查。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">黑色</font>**<font style="color:rgba(0, 0, 0, 0.82);">：表示对象和它所有引用的对象都已检查且是可达的。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">三色标记步骤</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">初始化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：所有对象开始时都是白色。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">标记开始</font>**<font style="color:rgba(0, 0, 0, 0.82);">：从GC Roots开始，根对象标记为灰色。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">扫描灰色对象</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">将灰色对象引用的所有白色对象标记为灰色。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">然后将该灰色对象标记为黑色。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">重复步骤3</font>**<font style="color:rgba(0, 0, 0, 0.82);">直到没有更多的灰色对象。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">清除</font>**<font style="color:rgba(0, 0, 0, 0.82);">：未标记为黑色的对象为白色，即垃圾，可被回收。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">代码示例模拟</font>
<font style="color:rgba(0, 0, 0, 0.82);">以下是一个简单的模拟示例，展示了假设某个对象图的三色标记流程：</font>

```java
import java.util.HashSet;  
import java.util.Set;  

class ObjectNode {  
    String name;  
    Set<ObjectNode> references = new HashSet<>();  
    String color = "White"; // White by default  

    ObjectNode(String name) {  
        this.name = name;  
    }  

    void addReference(ObjectNode node) {  
        references.add(node);  
    }  

    @Override  
    public String toString() {  
        return name;  
    }  
}  

public class TricolorMarkingExample {  
    private static Set<ObjectNode> roots = new HashSet<>();  

    public static void main(String[] args) {  
        // Create objects  
        ObjectNode objA = new ObjectNode("A");  
        ObjectNode objB = new ObjectNode("B");  
        ObjectNode objC = new ObjectNode("C");  

        // Create references  
        objA.addReference(objB);  
        objB.addReference(objC);  

        // GC Roots  
        roots.add(objA);   
        roots.add(objB);  

        // Perform tricolor marking  
        performTricolorMarking();  
    }  

    static void performTricolorMarking() {  
        // Initial marking of root nodes  
        for (ObjectNode root : roots) {  
            markGray(root);  
        }  

        while (roots.stream().anyMatch(node -> node.color.equals("Gray"))) {  
            for (ObjectNode root : roots) {  
                if (root.color.equals("Gray")) {  
                    scanAndBlacken(root);  
                }  
            }  
        }  

        // Output the color result  
        System.out.println("Final color states:");  
        roots.forEach(root -> System.out.println(root.name + ": " + root.color + ", references: " + root.references));  
    }  

    static void markGray(ObjectNode node) {  
        if (node.color.equals("White")) {  
            System.out.println("Marking " + node.name + " gray.");  
            node.color = "Gray";  
        }  
    }  

    static void scanAndBlacken(ObjectNode node) {  
        System.out.println("Scanning and blackening " + node.name);  
        node.references.forEach(reference -> {  
            if (reference.color.equals("White")) {  
                markGray(reference);  
            }  
        });  
        node.color = "Black";  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">解释</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">对象生成</font>**<font style="color:rgba(0, 0, 0, 0.82);">：创建若干对象并引用它们。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">根集合</font>**<font style="color:rgba(0, 0, 0, 0.82);">：设定起始对象集，通常指代程序运行时栈、全局引用等。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">三色规则应用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过</font>`<font style="color:rgba(0, 0, 0, 0.82);">markGray</font>`<font style="color:rgba(0, 0, 0, 0.82);">和</font>`<font style="color:rgba(0, 0, 0, 0.82);">scanAndBlacken</font>`<font style="color:rgba(0, 0, 0, 0.82);">函数演示从GC Roots开始的标记过程。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">最终输出</font>**<font style="color:rgba(0, 0, 0, 0.82);">：显示对象的颜色状态和引用关系，以验证三色标记的正确性。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">使用三色标记的好处</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">避免循环引用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：能够正确识别循环引用对象不被误判为不可回收。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">并发性增强</font>**<font style="color:rgba(0, 0, 0, 0.82);">：支持并发标记，减少“Stop-the-World”时间。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">渐进性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：可以执行增量垃圾回收，减小程序暂停。</font>

<font style="color:rgba(0, 0, 0, 0.82);">在实际的垃圾回收算法实现中，如G1和CMS，三色标记法被广泛应用，用于高效地管理内存和提高程序运行的性能。</font>



> 更新: 2024-08-09 15:59:48  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/sguahv8u1s2tckek>