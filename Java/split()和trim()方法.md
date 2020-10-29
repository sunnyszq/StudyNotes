### Java split()方法和 Java trim()方法

#### 1.split()

```java
public String[] split(String regex, int limit)
```

- regex -- 正则表达式分隔符。
- limit -- 分割的份数。

##### 下面就让我们来举个例子

```java
String str = "one two three, four";
        String[] tokens = str.split(" ");
        for (String s: tokens)
            System.out.println(s);
```

这个例子中，我们用了split函数中的第一个参数，我们用空格（“ ”），进行分割，所以这段代码的结果如下：

![img](https://upload-images.jianshu.io/upload_images/14596327-ef197755edc247f4.png?imageMogr2/auto-orient/strip|imageView2/2/w/228/format/webp)

##### 如果我们想用两个分割符进行分割，及即想用空格（" "）分割,也想用逗号（","）分割,我们可以用 "|" 来使其实现，下面看一个例子：

```java
String str = "one tw,o th,ree fo,ur";
        String[] tokens = str.split(" |,");
        for (String s: tokens)
            System.out.println(s);
```

结果如下：

![img](https://upload-images.jianshu.io/upload_images/14596327-135d310b6e3563cf.png?imageMogr2/auto-orient/strip|imageView2/2/w/214/format/webp)

##### 下面就让我们来看看第二个参数的作用

第二个参数是分割的份数，我们来举个例子：

```java
 String str = "one two three four";
        String[] tokens = str.split(" ",2);
        for (String s: tokens)
            System.out.println(s);
```

![img](https://upload-images.jianshu.io/upload_images/14596327-9bc7f7d8d24d5e66.png?imageMogr2/auto-orient/strip|imageView2/2/w/270/format/webp)

可以看出，由于多了第二个参数，结果发生了改变，是因为我们限制了**分割的份数为2份**，所以**当分割结果达到2份时，分割就结束了**。

#### 2.trim()

trim() 方法用于删除字符串的**头尾**空白符。

### 语法

```java
public String trim()
```

### 参数

- 无

### 返回值

删除头尾空白符的字符串。

### 实例

```java
public class Test {
    public static void main(String args[]) {
        String Str = new String("    www.runoob.com    ");
        System.out.print("原始值 :" );
        System.out.println( Str );

        System.out.print("删除头尾空白 :" );
        System.out.println( Str.trim() );
    }
}
```

以上程序执行结果为：

```
原始值 :    www.runoob.com    
删除头尾空白 :www.runoob.com
```

