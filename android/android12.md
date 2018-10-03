## java的 值类型 和 引用类型

在将这个之前先讲一个字符串一些特性；
```java
String a = "hello";
String b = "hello";

String c = new String("hello");
String d = new String("hello");

System.out.println(a == b);//true
System.out.println(a == c);//false
System.out.println(c == d);//false
System.out.println(a.equals(c));//true
System.out.println(c.equals(d));//true
```

由上面的输出可以看出
字符串的两种定义是不同的
```java

//不论那种方式，声明字符串时，首先建立一个缓冲区，里面放着字符串“hello”。

//1.第一种先在栈中创建一个对String类的对象引用变量s，然后去查找"abc"是否被保存在字符串常量池中，如果没有则在栈中创建三个char型的值'a'、'b'、'c'，然后在堆中创建一个String对象object，它的值是刚才在栈中创建的三个char型值组成的数组{'a'、'b'、'c'}，接着这个String对象object被存放进字符串常量池，最后将s指向这个对象的地址，如果"abc"已经被保存在字符串常量池中，则在字符串常量池中找到值为"abc"的对象object，然后将s指向这个对象的地址。
String a = "abc";

//2、这个分为两个步骤；
第一步：参考第一种创建方式；
第二部：由于“hello”已经被创建，并且
同样会检查缓冲区是否存在这个字符串，但是不管有没有，都会在堆中新建一个对象，如果缓冲区不存在这个字符串，就要新建一个。以为是两个对象，所以两个字符串不一样，输出为false。
String c = new String("hello");
```



接下来继续将java的值类型和引用类型；

其实这个很简单，java中除了八种基本数值类型是值类型外，其他的全部是引用类型：
八种基本数据类型分别是：int、short、float、double、long、boolean、byte、char；
它们的封装类分别是：Integer、Short、Float、Double、Long、Boolean、Byte、Character。

```java
//同样，
//
Integer inta = new Integer(1);
Integer intb = new Integer(1);

int intc = 1;
int intd = 1;


System.out.println(intc == intd);//true
System.out.println(inta == intb);//false

System.out.println(inta.intValue() == intb.intValue());//true
```

