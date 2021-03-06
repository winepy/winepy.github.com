## fail-fast 与 fail-safe

[摘抄自此博客](https://blog.csdn.net/u014692324/article/details/78577130)

1.什么是同步修改？

当一个或多个线程正在遍历一个集合Collection，此时另一个线程修改了这个集合的内容（添加，删除或者修改）。这就是并发修改

2.什么是 fail-fast 机制?

fail-fast机制在遍历一个集合时，当集合结构被修改，会抛出Concurrent Modification Exception。

fail-fast会在以下两种情况下抛出ConcurrentModificationException

    （1）单线程环境
    集合被创建后，在遍历它的过程中修改了结构。

    注意 remove()方法会让expectModcount和modcount相等，所以是不会抛出这个异常。

    （2）多线程环境

    当一个线程在遍历这个集合，而另一个线程对这个集合的结构进行了修改。

3. fail-fast机制是如何检测的？

迭代器在遍历过程中是直接访问内部数据的，因此内部的数据在遍历的过程中无法被修改。为了保证不被修改，迭代器内部维护了一个标记 “mode” ，当集合结构改变（添加删除或者修改），标记"mode"会被修改，而迭代器每次的hasNext()和next()方法都会检查该"mode"是否被改变，当检测到被修改时，抛出Concurrent
 Modification Exception

。下面看看ArrayList迭代器部分的源码

```java
private class Itr implements Iterator<E> {  
        int cursor;  
        int lastRet = -1;  
        int expectedModCount = ArrayList.this.modCount;  
  
        public boolean hasNext() {  
            return (this.cursor != ArrayList.this.size);  
        }  
  
        public E next() {  
            checkForComodification();  
            /** 省略此处代码 */  
        }  
  
        public void remove() {  
            if (this.lastRet < 0)  
                throw new IllegalStateException();  
            checkForComodification();  
            /** 省略此处代码 */  
        }  
  
        final void checkForComodification() {  
            if (ArrayList.this.modCount == this.expectedModCount)  
                return;  
            throw new ConcurrentModificationException();  
        }  
    }  

```


可以看到它的标记“mode”为 expectedModeCount

4. fail-safe机制

fail-safe任何对集合结构的修改都会在一个复制的集合上进行修改，因此不会抛出ConcurrentModificationException

fail-safe机制有两个问题

    （1）需要复制集合，产生大量的无效对象，开销大

    （2）无法保证读取的数据是目前原始数据结构中的数据。



5. fail-fast 和 fail-safe的例子

```java
import java.util.HashMap;  
import java.util.Iterator;  
import java.util.Map;  
  
public class FailFastExample  
{  
      
      
    public static void main(String[] args)  
    {  
        Map<String,String> premiumPhone = new HashMap<String,String>();  
        premiumPhone.put("Apple", "iPhone");  
        premiumPhone.put("HTC", "HTC one");  
        premiumPhone.put("Samsung","S5");  
          
        Iterator iterator = premiumPhone.keySet().iterator();  
          
        while (iterator.hasNext())  
        {  
            System.out.println(premiumPhone.get(iterator.next()));  
            premiumPhone.put("Sony", "Xperia Z");  
        }  
          
    }  
      
} 

```

输出：
```
iPhone
Exception in thread "main" java.util.ConcurrentModificationException
        at java.util.HashMap$HashIterator.nextEntry(Unknown Source)
        at java.util.HashMap$KeyIterator.next(Unknown Source)
        at FailFastExample.main(FailFastExample.java:20)

```


```java

import java.util.concurrent.ConcurrentHashMap;  
import java.util.Iterator;  
  
  
public class FailSafeExample  
{  
      
      
    public static void main(String[] args)  
    {  
        ConcurrentHashMap<String,String> premiumPhone =   
                               new ConcurrentHashMap<String,String>();  
        premiumPhone.put("Apple", "iPhone");  
        premiumPhone.put("HTC", "HTC one");  
        premiumPhone.put("Samsung","S5");  
          
        Iterator iterator = premiumPhone.keySet().iterator();  
          
        while (iterator.hasNext())  
        {  
            System.out.println(premiumPhone.get(iterator.next()));  
            premiumPhone.put("Sony", "Xperia Z");  
        }  
          
    }  
      
}

```

输出
```
S5
HTC one
iPhone
```