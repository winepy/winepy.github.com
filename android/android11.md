## java的序列化和反序列化

```
Android序列化中又两种方式：
1. implement Serializable接口
2. implement Parcelable接口

第一种方式 实现比较简单，实现借口就行；

1，在未手动定义serialVersionUID的值时，其取值是Java运行时环境根据类的内部细节自动生成的。如果对类的源代码修改，再重新编译，新生成的类文件的serialVersionUID的取值有可能也会发生变化。

2，类的serialVersionUID的默认值完全依赖于Java编译器的实现，对于同一个类，用不同的Java编译器编译，有可能会导致不同的 serialVersionUID，也有可能相同。为了提高serialVersionUID的独立性和确定性，强烈建议在一个可序列化类中显式的定义serialVersionUID，为它赋予明确的值。

显式地定义serialVersionUID有两种用途：
　　1、 在某些场合，希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有相同的serialVersionUID；
　　2、 在某些场合，不希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有不同的serialVersionUID。



2.1首先需要实现：writeToParcel和readFromPacel方法

2.2接着需要在：该类中添加一个名为CREATOR的static final属性 
改属性需要实现：android.os.Parcelable.Creator接口

2.3再接着需要从写接口中的两个方法： 
createFromParcel(Parcel source)方法:实现从source创建出JavaBean实例的功能 

```

```java
public class Demo implements Parcelable {

    String name;
    int Size;

    protected Demo(Parcel in) {
       name= in.readString();
        Size=in.readInt();
    }
    public static final Creator<Demo> CREATOR = new Creator<Demo>() {
        @Override
        public Demo createFromParcel(Parcel in) {
            return new Demo(in);
        }
        @Override
        public Demo[] newArray(int size) {
            return new Demo[size];
        }
    };
    @Override
    public int describeContents() {
        return 0;//默认返回O
    }
    @Override
    public void writeToParcel(Parcel dest, int flags) {
     dest.writeInt(Size);
        dest.writeString(name);
    }

}

```