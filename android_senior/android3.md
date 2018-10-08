## java类的编译，加载，执行

## 1. java的编译

```
java的编译就是讲java文件转化为.class文件
主要分为三步；

1. 分析和输入到符号表
2. 注解处理
3. 语义分析和生成class文件

最后生成的class文件由以下部分组成：
1. 结构信息。包括class文件格式版本号及各部分的数量与大小的信息
2. 元数据。对应于Java源码中声明与常量的信息。包含类/继承的超类/实现的接口的声明信息、域与方法声明信息和常量池
3. 方法信息。对应Java源码中语句和表达式对应的信息。包含字节码、异常处理器表、求值栈与局部变量区大小、求值栈的类型记录、调试符号信息
```

## 2. java的加载

JVM的类加载是通过ClassLoader及其子类来完成的，
```
1）Bootstrap ClassLoader

负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类,该路径不需要我们指定

2）Extension ClassLoader

负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中jre/lib/*.jar或-Djava.ext.dirs指定目录下的jar包，该路径不需要我们指定

3）App ClassLoader

负责加载classpath中指定的jar包及目录中class，命令行可以通过-classpath 或者CLASSPATH环境变量来指定，IDE中可以在项目当前目录直接加入jar文件即可。

4）Custom ClassLoader

属于应用程序根据自身需要自定义的ClassLoader，如tomcat、jboss都会根据j2ee规范自行实现ClassLoader

```

