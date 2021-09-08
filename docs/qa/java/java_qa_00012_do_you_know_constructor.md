# Java 的构造方法你真的懂了吗？
内容基于```java 8```。我对```JVM```懂得不是很多，内容不一定是对的，有错的会慢慢纠正。

## 证明类是有构造方法的，即使没有写，编译器也会自动生成
例1和例2两个类（编译后）是等效的。因为如果类中没有构造方法，编译器会生成一个默认的构造方法（无参构造方法）。
同时也证明了```<init>```方法就是构造方法，例1及例2只证明了无参构造方法，构造方法的重载请读者自己动手试试。

还有个参考文献可以证明：_The Java ® Virtual Machine Specification, Java SE 8 Edition, 2.9 Special Methods_
> At the level of the Java Virtual Machine, every constructor written in the Java programming language (JLS §8.8) appears as an instance initialization method that has the special name \<init\> .

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
    private boolean flag1 = true;
    private int id1 = 129;
    private long no1 = 666;
    private int id2 = 130;
    private long no2 = 667;
    private boolean flag2 = true;
}
// 例6
public class Constructor_3_Field_202 {
    private boolean flag1 = true;
    private int id1 = 129;
    private long no1 = 666;
    public Constructor_3_Field_202(long no, int id) {
        this.flag2 = false;
        this.id1 = id;
        this.no2 = id;
    }
    private int id2 = 130;
    private long no2 = 667;
    private boolean flag2 = true;
}
```

```
  // 例5类编译后的字节码
  public Constructor_3_Field_201();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: iconst_1
         6: putfield      #2                  // Field flag1:Z
         9: aload_0
        10: sipush        129
        13: putfield      #3                  // Field id1:I
        16: aload_0
        17: ldc2_w        #4                  // long 666l
        20: putfield      #6                  // Field no1:J
        23: aload_0
        24: sipush        130
        27: putfield      #7                  // Field id2:I
        30: aload_0
        31: ldc2_w        #8                  // long 667l
        34: putfield      #10                 // Field no2:J
        37: aload_0
        38: iconst_1
        39: putfield      #11                 // Field flag2:Z
        42: return

  // 例6类编译后的字节码
  public Constructor_3_Field_202(long, int);
    descriptor: (JI)V
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=4, args_size=3
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: iconst_1
         6: putfield      #2                  // Field flag1:Z
         9: aload_0
        10: sipush        129
        13: putfield      #3                  // Field id1:I
        16: aload_0
        17: ldc2_w        #4                  // long 666l
        20: putfield      #6                  // Field no1:J
        23: aload_0
        24: sipush        130
        27: putfield      #7                  // Field id2:I
        30: aload_0
        31: ldc2_w        #8                  // long 667l
        34: putfield      #10                 // Field no2:J
        37: aload_0
        38: iconst_1
        39: putfield      #11                 // Field flag2:Z
        42: aload_0
        43: iconst_0
        44: putfield      #11                 // Field flag2:Z
        47: aload_0
        48: iload_3
        49: putfield      #3                  // Field id1:I
        52: aload_0
        53: iload_3
        54: i2l
        55: putfield      #10                 // Field no2:J
        58: return
```
