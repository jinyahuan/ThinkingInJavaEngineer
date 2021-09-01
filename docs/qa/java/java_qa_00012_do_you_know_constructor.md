# Java 的构造方法你真的懂了吗？
内容基于```java 8```。我对```JVM```懂得不是很多，内容不一样是对的，有错的会慢慢纠正。

## 证明类是有构造方法的，即使没有写，编译器也会自动生成
例1和例2两个类（编译后）是等效的。因为如果类中没有构造方法，编译器会生成一个默认的构造方法（无参构造方法）。
同时也证明了```<init>```方法就是构造方法，例1及例2只证明了无参构造方法，构造方法的重载请读者自己动手试试。
还有个参考文献可以证明：_The Java ® Virtual Machine Specification, Java SE 8 Edition, 2.9 Special Methods_，其中第一段就说明了：
***At the level of the Java Virtual Machine, every constructor written in the Java programming language (JLS §8.8) appears as an instance initialization method that has the special name <init> .***

```java
// 例1
public class Constructor_1_Implicit {}
// 例2
public class Constructor_1_Explicit {
    public Constructor_1_Explicit() {
        super();
    }
}
```
编译后的字节码如下：
```
  // 例1类编译后的字节码
  public cn.jinyahuan.course.java.constructor.Constructor_1_Implicit();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  // 例2类编译后的字节码
  public cn.jinyahuan.course.java.constructor.Constructor_1_Explicit();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

```

## 证明普通成员变量（非 static）的赋值是在构造方法中进行的
变量正常的赋值顺序：由测试类的情况来看是：按照先字段顺序，后构造器的顺序；然后按照其各自的定义顺序。还需要再找找资料确定这个结论。
可以确定的是普通成员变量（非 static）的赋值是在构造方法中进行的。也是这个结论使得我对创建一个对象（例：new Constructor_1_Implicit()）一个对象有了更深的了解。
```java
// 例5
public class Constructor_3_Field_201 {
    private int id = 129;
    private long no = 666;
    private boolean flag = false;
}
// 例6
public class Constructor_3_Field_202 {
    private int id = 129;
    private long no = 666;
    private boolean flag;
    public Constructor_3_Field_202() {
        flag = true;
    }
    public Constructor_3_Field_202(int id, long no) {
        flag = true;
        this.no = no;
        this.id = id;
    }
}
```

```
  // 例5类编译后的字节码
  public cn.jinyahuan.course.java.constructor.Constructor_3_Field_201();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: sipush        129
       8: putfield      #2                  // Field id:I
      11: aload_0
      12: ldc2_w        #3                  // long 666l
      15: putfield      #5                  // Field no:J
      18: aload_0
      19: iconst_0
      20: putfield      #6                  // Field flag:Z
      23: return

  // 例6类编译后的字节码
  public cn.jinyahuan.course.java.constructor.Constructor_3_Field_202();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: sipush        129
       8: putfield      #2                  // Field id:I
      11: aload_0
      12: ldc2_w        #3                  // long 666l
      15: putfield      #5                  // Field no:J
      18: aload_0
      19: iconst_1
      20: putfield      #6                  // Field flag:Z
      23: return
  public cn.jinyahuan.course.java.constructor.Constructor_3_Field_202(int, long);
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: sipush        129
       8: putfield      #2                  // Field id:I
      11: aload_0
      12: ldc2_w        #3                  // long 666l
      15: putfield      #5                  // Field no:J
      18: aload_0
      19: iconst_1
      20: putfield      #6                  // Field flag:Z
      23: aload_0
      24: lload_2
      25: putfield      #5                  // Field no:J
      28: aload_0
      29: iload_1
      30: putfield      #2                  // Field id:I
      33: return
```
