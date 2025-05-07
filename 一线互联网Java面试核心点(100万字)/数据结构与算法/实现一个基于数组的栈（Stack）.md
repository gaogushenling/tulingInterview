# 实现一个基于数组的栈（Stack）

<font style="color:rgba(0, 0, 0, 0.82);">实现一个基于数组的栈需要定义一个类来管理栈的基本操作，如入栈、出栈、查看栈顶元素等。以下是一个简单的基于数组的栈实现：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">基于数组的栈实现</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">设计思路</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用一个数组来存储栈的元素。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用一个整数变量</font>`<font style="color:rgba(0, 0, 0, 0.82);">top</font>`<font style="color:rgba(0, 0, 0, 0.82);">来跟踪栈顶元素的索引。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">提供基本的栈操作：</font>`<font style="color:rgba(0, 0, 0, 0.82);">push</font>`<font style="color:rgba(0, 0, 0, 0.82);">（入栈）、</font>`<font style="color:rgba(0, 0, 0, 0.82);">pop</font>`<font style="color:rgba(0, 0, 0, 0.82);">（出栈）、</font>`<font style="color:rgba(0, 0, 0, 0.82);">peek</font>`<font style="color:rgba(0, 0, 0, 0.82);">（查看栈顶元素）、</font>`<font style="color:rgba(0, 0, 0, 0.82);">isEmpty</font>`<font style="color:rgba(0, 0, 0, 0.82);">（检查栈是否为空）和</font>`<font style="color:rgba(0, 0, 0, 0.82);">isFull</font>`<font style="color:rgba(0, 0, 0, 0.82);">（检查栈是否已满）。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">代码示例</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
public class ArrayStack {  
    private int[] stack;  
    private int top;  
    private int capacity;  

    // 初始化栈  
    public ArrayStack(int capacity) {  
        this.capacity = capacity;  
        stack = new int[capacity];  
        top = -1; // 栈为空时，top为-1  
    }  

    // 入栈操作  
    public void push(int value) {  
        if (isFull()) {  
            throw new StackOverflowError("Stack is full");  
        }  
        stack[++top] = value;  
    }  

    // 出栈操作  
    public int pop() {  
        if (isEmpty()) {  
            throw new IllegalStateException("Stack is empty");  
        }  
        return stack[top--];  
    }  

    // 查看栈顶元素  
    public int peek() {  
        if (isEmpty()) {  
            throw new IllegalStateException("Stack is empty");  
        }  
        return stack[top];  
    }  

    // 检查栈是否为空  
    public boolean isEmpty() {  
        return top == -1;  
    }  

    // 检查栈是否已满  
    public boolean isFull() {  
        return top == capacity - 1;  
    }  

    // 获取栈的大小  
    public int size() {  
        return top + 1;  
    }  

    public static void main(String[] args) {  
        ArrayStack stack = new ArrayStack(5);  

        stack.push(10);  
        stack.push(20);  
        stack.push(30);  

        System.out.println("Top element is: " + stack.peek());  
        System.out.println("Stack size is: " + stack.size());  

        System.out.println("Popped element is: " + stack.pop());  
        System.out.println("Stack size after pop is: " + stack.size());  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">说明</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">初始化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：构造函数</font>`<font style="color:rgba(0, 0, 0, 0.82);">ArrayStack(int capacity)</font>`<font style="color:rgba(0, 0, 0, 0.82);">用于初始化栈的容量和数组。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">入栈</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">push</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：在栈顶插入一个元素，并更新</font>`<font style="color:rgba(0, 0, 0, 0.82);">top</font>`<font style="color:rgba(0, 0, 0, 0.82);">索引。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">出栈</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">pop</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：移除并返回栈顶元素，更新</font>`<font style="color:rgba(0, 0, 0, 0.82);">top</font>`<font style="color:rgba(0, 0, 0, 0.82);">索引。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">查看栈顶</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">peek</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：返回栈顶元素但不移除它。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">检查空栈</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">isEmpty</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：判断栈是否为空。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">检查满栈</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">isFull</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：判断栈是否已满。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">获取栈大小</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">size</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：返回当前栈中元素的数量。</font>

<font style="color:rgba(0, 0, 0, 0.82);">这种基于数组的栈实现简单且高效，但需要注意栈的容量限制。如果需要动态调整栈的大小，可以考虑使用动态数组（如</font>`<font style="color:rgba(0, 0, 0, 0.82);">ArrayList</font>`<font style="color:rgba(0, 0, 0, 0.82);">）或链表来实现。</font>



> 更新: 2024-08-30 22:18:02  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/dovgvw2vx40m0e9x>