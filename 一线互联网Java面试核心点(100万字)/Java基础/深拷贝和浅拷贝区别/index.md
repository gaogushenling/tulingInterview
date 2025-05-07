# 深拷贝和浅拷贝区别

### <font style="color:rgba(0, 0, 0, 0.9);">1. </font>**<font style="color:rgba(0, 0, 0, 0.9);">什么是浅拷贝？浅拷贝？</font>**
**<font style="color:rgba(0, 0, 0, 0.9);">浅拷贝</font>**<font style="color:rgba(0, 0, 0, 0.9);">是指复制对象时，只复制对象本身，而不复制对象引用的其他对象。浅拷贝后，新对象和原对象共享引用的其他对象。</font>

**<font style="color:rgba(0, 0, 0, 0.9);">深拷贝</font>**<font style="color:rgba(0, 0, 0, 0.9);">是指复制对象时，不仅复制对象本身，还递归地复制对象引用的其他对象。深拷贝后，新对象和原对象完全独立。</font>

深拷贝和浅拷贝是在进行对象的复制时常用的两种方式，它们有以下区别：

1. **拷贝的程度：**
    - 浅拷贝只拷贝对象的引用，不创建新的对象实例。拷贝后的对象与原始对象共享同一份数据，对其中一个对象的修改会影响到另一个对象。
    - 深拷贝创建一个全新的对象实例，并将原始对象的所有属性值复制到新对象中。拷贝后的对象与原始对象是独立的，对任一对象的修改不会影响另一个对象。
2. **对象引用：**
    - 浅拷贝只复制对象引用，新旧对象仍然指向同一块内存空间，修改其中一个对象的属性会影响另一个对象。
    - 深拷贝会复制对象本身以及对象引用指向的其他对象，所有对象的引用都将指向全新的内存空间。
3. **性能开销：**
    - 浅拷贝的性能开销较小，因为仅复制对象的引用。
    - 深拷贝的性能开销较大，因为需要创建新的对象实例并复制所有属性。

通常情况下，当我们需要复制一个对象并希望新对象与原始对象互不影响时，应使用深拷贝。而浅拷贝更适用于那些对象结构较简单、不包含引用类型成员变量或不需要独立修改的情况。



#### <font style="color:rgba(0, 0, 0, 0.9);">示例：浅拷贝</font>
```java
/**
 * @Auth:TianMing
 * @Description：浅拷贝
 */
public class ShallowCopyExample implements Cloneable {
    private int id;
    private String name;
    private Address address;

    public ShallowCopyExample(int id, String name, Address address) {
        this.id = id;
        this.name = name;
        this.address = address;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    public static void main(String[] args) {
        Address address = new Address("123 Main St", "City");
        ShallowCopyExample original = new ShallowCopyExample(1, "John", address);

        try {
            ShallowCopyExample copy = (ShallowCopyExample) original.clone();
            System.out.println("Original Address: " + original.address);
            System.out.println("Copy Address: " + copy.address);

            // 修改地址
            copy.address.setStreet("456 New St");
            System.out.println("After modification:");
            System.out.println("Original Address: " + original.address);
            System.out.println("Copy Address: " + copy.address);
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
    }
}

class Address {
    private String street;
    private String city;

    public Address(String street, String city) {
        this.street = street;
        this.city = city;
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    @Override
    public String toString() {
        return "Address{" + "street='" + street + '\'' + ", city='" + city + '\'' + '}';
    }
}
```

**<font style="color:rgba(0, 0, 0, 0.9);">输出：</font>**

<font style="color:rgba(0, 0, 0, 0.6);">复制</font>

```plain
Original Address: Address{street='123 Main St', city='City'}
Copy Address: Address{street='123 Main St', city='City'}
After modification:
Original Address: Address{street='456 New St', city='City'}
Copy Address: Address{street='456 New St', city='City'}
```



#### <font style="color:rgba(0, 0, 0, 0.9);">示例：深拷贝</font>
```java
/**
 * @Auth:TianMing
 * @Description：深拷贝
 */
public class DeepCopyExample implements Cloneable {
    private int id;
    private String name;
    private Address address;

    public DeepCopyExample(int id, String name, Address address) {
        this.id = id;
        this.name = name;
        this.address = address;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        DeepCopyExample copy = (DeepCopyExample) super.clone();
        copy.address = (Address) this.address.clone(); // 深拷贝地址
        return copy;
    }

    public static void main(String[] args) {
        Address address = new Address("123 Main St", "City");
        DeepCopyExample original = new DeepCopyExample(1, "John", address);

        try {
            DeepCopyExample copy = (DeepCopyExample) original.clone();
            System.out.println("Original Address: " + original.address);
            System.out.println("Copy Address: " + copy.address);

            // 修改地址
            copy.address.setStreet("456 New St");
            System.out.println("After modification:");
            System.out.println("Original Address: " + original.address);
            System.out.println("Copy Address: " + copy.address);
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
    }
}

class Address implements Cloneable {
    private String street;
    private String city;

    public Address(String street, String city) {
        this.street = street;
        this.city = city;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    @Override
    public String toString() {
        return "Address{" + "street='" + street + '\'' + ", city='" + city + '\'' + '}';
    }
}
```

**<font style="color:rgba(0, 0, 0, 0.9);">输出：</font>**

<font style="color:rgba(0, 0, 0, 0.6);">复制</font>

```plain
Original Address: Address{street='123 Main St', city='City'}
Copy Address: Address{street='123 Main St', city='City'}
After modification:
Original Address: Address{street='123 Main St', city='City'}
Copy Address: Address{street='456 New St', city='City'}
```

### <font style="color:rgba(0, 0, 0, 0.9);">3.</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>**<font style="color:rgba(0, 0, 0, 0.9);">浅拷贝和深拷贝的区别是什么？</font>**
+ **<font style="color:rgba(0, 0, 0, 0.9);">浅拷贝</font>**<font style="color:rgba(0, 0, 0, 0.9);">：只复制对象本身，引用的其他对象不复制，新对象和原对象共享引用的其他对象。</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);">深拷贝</font>**<font style="color:rgba(0, 0, 0, 0.9);">：递归地复制对象及其引用的所有对象，新对象和原对象完全独立。</font>

### <font style="color:rgba(0, 0, 0, 0.9);">4.</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>**<font style="color:rgba(0, 0, 0, 0.9);">如何实现深拷贝和浅拷贝？</font>**
+ **<font style="color:rgba(0, 0, 0, 0.9);">浅拷贝</font>**<font style="color:rgba(0, 0, 0, 0.9);">：使用 </font>`<font style="color:rgba(0, 0, 0, 0.9);">Object.clone()</font>`<font style="color:rgba(0, 0, 0, 0.9);"> 方法，或者通过构造方法或手动复制字段</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);">深拷贝</font>**<font style="color:rgba(0, 0, 0, 0.9);">：手动实现深拷贝，或者使用序列化。</font>

### <font style="color:rgba(0, 0, 0, 0.9);">5.</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>**<font style="color:rgba(0, 0, 0, 0.9);">深拷贝的实现方式有哪些？</font>**
1. **<font style="color:rgba(0, 0, 0, 0.9);">手动实现</font>**<font style="color:rgba(0, 0, 0, 0.9);">：手动实现深拷贝是最直接的方法，通过逐个复制对象的字段来实现。这种方法需要对对象的内部结构有详细的了解，并且需要递归地复制所有引用的对象。。</font>
2. **<font style="color:rgba(0, 0, 0, 0.9);">序列化</font>**<font style="color:rgba(0, 0, 0, 0.9);">：序列化是另一种实现深拷贝的方法。通过将对象序列化为字节流，然后再反序列化为新对象，可以实现深拷贝。这种方法适用于所有实现了 </font>`<font style="color:rgba(0, 0, 0, 0.9);">Serializable</font>`<font style="color:rgba(0, 0, 0, 0.9);"> 接口的类。</font>
3. **<font style="color:rgba(0, 0, 0, 0.9);">第三方库</font>**<font style="color:rgba(0, 0, 0, 0.9);">：一些第三方库（如 Apache Commons Lang）提供了深拷贝的功能，可以简化代码。</font>

#### <font style="color:rgba(0, 0, 0, 0.9);">示例（手动实现深拷贝）：</font>
```java
/**
 * @Auth:TianMing
 * @Description： 手动实现深拷贝
 */
public class DeepCopyExample implements Cloneable {
    private int id;
    private String name;
    private Address address;

    public DeepCopyExample(int id, String name, Address address) {
        this.id = id;
        this.name = name;
        this.address = address;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        // 手动实现深拷贝
        DeepCopyExample copy = (DeepCopyExample) super.clone();
        copy.address = (Address) this.address.clone(); // 深拷贝地址
        return copy;
    }

    public static void main(String[] args) {
        Address address = new Address("123 Main St", "City");
        DeepCopyExample original = new DeepCopyExample(1, "John", address);

        try {
            DeepCopyExample copy = (DeepCopyExample) original.clone();
            System.out.println("Original Address: " + original.address);
            System.out.println("Copy Address: " + copy.address);

            // 修改地址
            copy.address.setStreet("456 New St");
            System.out.println("After modification:");
            System.out.println("Original Address: " + original.address);
            System.out.println("Copy Address: " + copy.address);
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
    }
}

class Address implements Cloneable {
    private String street;
    private String city;

    public Address(String street, String city) {
        this.street = street;
        this.city = city;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    @Override
    public String toString() {
        return "Address{" + "street='" + street + '\'' + ", city='" + city + '\'' + '}';
    }
}
```



#### <font style="color:rgba(0, 0, 0, 0.9);">示例：</font>**<font style="color:rgba(0, 0, 0, 0.9);">序列化实现</font>**
```java
import java.io.*;
/**
 * @Auth:TianMing
 * @Description： 序列化实现深拷贝
 */
public class DeepCopyWithSerialization {
    public static <T extends Serializable> T deepCopy(T obj) throws IOException, ClassNotFoundException {
        // 将对象序列化为字节流
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(byteArrayOutputStream);
        objectOutputStream.writeObject(obj);

        // 将字节流反序列化为新对象
        ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(byteArrayOutputStream.toByteArray());
        ObjectInputStream objectInputStream = new ObjectInputStream(byteArrayInputStream);
        return (T) objectInputStream.readObject();
    }

    public static void main(String[] args) {
        Address address = new Address("123 Main St", "City");
        DeepCopyExample original = new DeepCopyExample(1, "John", address);

        try {
            DeepCopyExample copy = DeepCopyWithSerialization.deepCopy(original);
            System.out.println("Original Address: " + original.address);
            System.out.println("Copy Address: " + copy.address);

            // 修改地址
            copy.address.setStreet("456 New St");
            System.out.println("After modification:");
            System.out.println("Original Address: " + original.address);
            System.out.println("Copy Address: " + copy.address);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



#### <font style="color:rgba(0, 0, 0, 0.9);">示例（使用第三方库 Apache Commons Lang实现）：</font>
```java
import org.apache.commons.lang3.SerializationUtils;

/**
 * @Auth:TianMing
 * @Description： 第三方库实现深拷贝
 */
public class DeepCopyWithApacheCommons {
    public static <T> T deepCopy(T obj) {
        return SerializationUtils.clone(obj);
    }

    public static void main(String[] args) {
        Address address = new Address("123 Main St", "City");
        DeepCopyExample original = new DeepCopyExample(1, "John", address);

        DeepCopyExample copy = DeepCopyWithApacheCommons.deepCopy(original);
        System.out.println("Original Address: " + original.address);
        System.out.println("Copy Address: " + copy.address);

        // 修改地址
        copy.address.setStreet("456 New St");
        System.out.println("After modification:");
        System.out.println("Original Address: " + original.address);
        System.out.println("Copy Address: " + copy.address);
    }
}
```

  
 



> 更新: 2025-04-15 19:30:37  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ni0qchop1a9934tm>