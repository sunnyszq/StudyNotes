### I/O

A stream can represent many different kinds of sources and
destinations, including disk files, devices, other programs, and
memory arrays.

流可以表示许多不同类型的源和目的地，包括磁盘文件、设备、其他程序和内存数组。

Java **IO** is an API that comes with Java which is targeted at **reading**
**and writing data (input and output)**.

The Java IO API is located in the Java IO package (**java.io**).

Java's IO package mostly concerns itself with the reading of raw
data from a source and writing of raw data to a destination. The
most typical sources and destinations of data are these:

Java的IO包主要关注从源读取原始数据并将原始数据写入目标。数据最典型的来源和目的地是:

![image-20201030081646634](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201030081646634.png)

#### **IO Class:**

![image-20201030081829446](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201030081829446.png)

----

#### **Byte Streams**

Programs use byte streams to perform input and output of 8-bit bytes.

All byte stream classes are **descended from InputStream and**
**OutputStream.**

Example:

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
public class CopyBytes {
public static void main(String[] args) throws IOException {
    FileInputStream in = null;
    FileOutputStream out = null;
    try {
        in = new FileInputStream("xanadu.txt");
        out = new FileOutputStream("outagain.txt");
        int c;
        while ((c = in.read()) != -1) { out.write(c); }
        } finally {
            if (in != null) { in.close(); }
            if (out != null) { out.close(); }
        }
    }
}
```

![image-20201030082532331](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201030082532331.png)

Closing a stream when it's no longer needed is very important — so
important that CopyBytes **uses a finally block to guarantee that both**
**streams will be closed even if an error occurs. This practice helps**
**avoid serious resource leaks.**

One possible error is that CopyBytes was unable to open one or both
files.

**When Not to Use Byte Streams**

CopyBytes看起来像一个普通的程序，但它实际上表示一种您**应该避免的低级I/O**。

由于xanadu.txt包**含字符数据，最好的方法是使用字符流**。

•还有用于**更复杂数据类型的流。字节流只能用于最原始的I/O**。

**所有其他流类型都是在字节流上构建的。**

----

#### **Character Streams**

**All character stream classes**  are **descended from Reader and Writer**

As with byte streams, there are character stream classes that specialize
in file I/O: **FileReader and FileWriter.**

```java
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
public class CopyCharacters {
    public static void main(String[] args) throws IOException {
    FileReader inputStream = null; FileWriter outputStream = null;
    try {
        inputStream = new FileReader("xanadu.txt");
        outputStream = new FileWriter("characteroutput.txt");
        int c;
        while ((c = inputStream.read()) != -1) { outputStream.write(c); }
    } finally {
        if (inputStream != null) { inputStream.close(); }
        if (outputStream != null) { outputStream.close(); }
    }
    }
} 
```

Character streams are often "wrappers" for byte streams.字符流通常是字节流的“包装器”。

The character stream uses the byte stream to perform the physical I/O,
while the character stream handles translation between characters and
bytes.

**字符流使用字节流执行物理I/O，而字符流处理字符和字节之间的转换**。

FileReader, for example, uses FileInputStream, while FileWriter
uses FileOutputStream.
– There are two general-purpose byte-to-character "bridge" streams:
InputStreamReader and OutputStreamWriter. Use them to create
character streams when there are no prepackaged character stream
classes that meet your needs.

例如，FileReader使用FileInputStream，而FileWriter使用FileOutputStream。

-有两种通用的字节到字符“桥”流:InputStreamReader OutputStreamWriter。当没有预先打包的字符流类满足您的需要时，使用它们创建字符流。

InputStreamReader
The Java InputStreamReader class, java.io.InputStreamReader, wraps a Java
InputStream, thereby turning the byte based InputStream into a character based
Reader.

```java
InputStream inputStream = new FileInputStream("c:\\data\\input.txt");
Reader inputStreamReader = new InputStreamReader(inputStream);
int data = inputStreamReader.read();
while(data != -1){
    char theChar = (char) data;
    data = inputStreamReader.read();
}
inputStreamReader.close();
```

OutputStreamWriter

Java OutputStreamWriter类，Java .io。OutputStreamWriter，用于包装Java OutputStream，从而将基于字节的输出流转换为基于字符的写入器。Java OutputStreamWriter还可以包装OutputStream的任何子类。

```java
OutputStream outputStream = new FileOutputStream("c:\\data\\output.txt");

OutputStreamWriter outputStreamWriter = new OutputStreamWriter(outputStream);
outputStreamWriter.write("Hello World");

outputStreamWriter.close();

```



----

#### Buffered Streams

Buffered input streams read data from a memory area known as
a buffer. Similarly, buffered output streams write data to a buffer.

Example:

inputStream = new BufferedReader(new FileReader("xanadu.txt"));
outputStream = new BufferedWriter(new FileWriter("characteroutput.txt"));

There are four buffered stream classes used to wrap unbuffered
streams: BufferedInputStream and BufferedOutputStream create buffered byte
streams, While BufferedReader and BufferedWriter create buffered character
streams.

有四个缓冲流类用于包装未缓冲的流:BufferedInputStream和BufferedOutputStream创建缓冲的字节流，而BufferedReader和BufferedWriter创建缓冲的字符流。

---

**Flushing Buffered Streams  刷新缓冲区**

在关键时刻写入缓冲区，而不是等待缓冲区被填满，这通常是有意义的。这被称为刷新缓冲区。

•一些缓冲输出类支持自动刷新，由一个可选的构造函数参数指定。

•当自动刷新启用时，某些关键事件会导致缓冲区被刷新。例如，autoflush PrintWriter对象在每次调用println或格式时刷新缓冲区。

•要手动刷新流，请调用其刷新方法。刷新方法对任何输出流都是有效的，但除非流被缓冲，否则不会生效。

----

**PushbackInputStream**

PushbackInputStream用于解析来自InputStream的数据。有时候，在决定如何解释当前字节之前，您需要提前读取几个字节，以了解将要发生什么。

PushbackInputStream允许您这样做。它允许您将读取的字节推回流中。下次调用read()时将再次读取这些字节。

ava PushbackInputStream是Java InputStream的一个子类，因此它继承了它的公共方法——read()、close()等。

The PushbackInputStream is similar to the Java PushbackReader, except
the PushbackInputStream reads raw bytes, and the PushbackReader reads
characters (text).

PushbackInputStream与Java PushbackReader类似，只是PushbackInputStream读取原始字节，而PushbackReader读取字符(文本)。

```java
PushbackInputStream input = new PushbackInputStream(new FileInputStream("c:\\data\\input.txt"));
int data = input.read();
input.unread(data);


//Use the try-with-resources construct
InputStream inputStream = new FileInputStream("data/data.bin");
try(PushbackInputStream pushbackInputStream =
    new PushbackInputStream(inputStream)){
    int data = pushbackInputStream.read();
    while(data != -1) {
    byte aByte = (byte) data;
    data = pushbackInputStream.read();
}
} 
```

---

**Scanning and Formatting**

-The scanner API breaks input into individual tokens associated
with bits of data.
– The formatting API assembles data into nicely formatted, humanreadable
form.

Breaking Input into Tokens

```java
import java.io.*;
import java.util.Scanner;
public class ScanXan {
    public static void main(String[] args) throws IOException {
    	Scanner s = null;
    	try {
    		s = new Scanner(new BufferedReader(new FileReader("xanadu.txt")));
    		while (s.hasNext()) {
            System.out.println(s.next());
            }
            } finally {
                if (s != null) {
                s.close();
    		}
    	}
    }
} 
```

---

**Formatting**

```java
public class Root2 {
public static void main(String[] args) {
    int i = 2;
    double r = Math.sqrt(i);
    System.out.format("The square root of %d is %f.%n", i, r);
    }
}

Here is the output:
The square root of 2 is 1.414214.
```

\- d将整数值格式化为十进制值。

\- f将浮点值格式化为十进制值。

\- n输出一个平台特定的行终止符。

•这里有一些其他的转换:

\- x将整数格式化为十六进制值。

\- s将任何值格式化为字符串。

\- tB将整数格式化为特定于地区的月份名称

```java
public class Format {
public static void main(String[] args) {
System.out.format("%f, %1$+020.10f %n", Math.PI);
}
}
Here's the output:
3.141593, +00000003.1415926536
```

![image-20201030112538458](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201030112538458.png)

---

Java平台支持三种标准流:

-通过System.in访问的标准输入;

-通过System.out访问的标准输出;

-通过System.err访问的标准错误。

•这些对象是自动定义的，不需要打开。

•标准输出和标准误差均为输出;单独的错误输出允许用户将常规输出转移到文件中，并且仍然能够读取错误消息。

To use Standard Input as a character stream, wrap System.in in
InputStreamReader.   在 InputStreamReader使用标准输入作为字符流，包装系统。

InputStreamReader cin = new InputStreamReader(System.in);

---

#### **Data Streams**  **数据流**

数据流支持原始数据类型值(布尔值、char值、字节值、short值、int值、long值、float值和double值)和字符串值的二进制I/O。

•所有数据流要么实现DataInput接口，要么实现DataOutput接口。

例如:DataInputStream和DataOutputStream





DataStreams使用一种非常糟糕的编程技术:它使用浮点数来表示货币值。

•一般来说，浮点数不适合精确值。这对于小数部分尤其不利，因为公共值(比如0.1)没有二进制表示。

•货币值使用的正确类型是java.math.BigDecimal。

不幸的是，BigDecimal是一种对象类型，因此它不能用于数据流。

•然而，BigDecimal将与对象流一起工作



---

#### **Object Streams  对象流**

就像数据流支持原始数据类型的I/O一样，对象流也支持对象的I/O。

•大多数(但不是全部)标准类支持其对象的序列化。那些实现了标记接口Serializable的代码。

对象流类是ObjectInputStream和ObjectOutputStream。

•这些类实现了ObjectInput和ObjectOutput，它们是DataInput和DataOutput的子接口。

•这意味着所有原始数据I/O方法都包含在数据中 流也在对象流中实现。因此，对象流可以包含原语值和对象值的混合物。

---

#### **Serializable   序列化**

```java
class T
implements Serializable
{
int i = 10;
int j = 9;
double d = 2.3;
transient int k = 15;
}

```

transient是序列化中使用的变量修饰符。**在序列化时，如果不想在文件中保存特定变量的值，则使用transient关键字。**

当**JVM遇到transient关键字时，它会忽略变量的原始值，并保存该变量数据类型的默认值,如int型，默认为0**

transient在满足安全约束方面发挥着重要作用。在现实生活中，我们有很多不想在文件中保存私有数据的例子。

transient关键字的另一个用途是不序列化变量，它的值可以通过其他序列化对象或系统计算/派生，比如人的年龄、当前日期等。

```java
import java.io.*;
public class TestObjectIO {
public static void main(String args[]) throws Exception {
    T t = new T();
    t.k = 8;
    FileOutputStream fos = new FileOutputStream("testobjectio.dat");
    ObjectOutputStream oos = new ObjectOutputStream(fos);
    oos.writeObject(t);
    oos.flush();
    oos.close();
    FileInputStream fis = new FileInputStream("testobjectio.dat");
    ObjectInputStream ois = new ObjectInputStream(fis);
    T tReaded = (T)ois.readObject();
    System.out.println(tReaded.i + " " + tReaded.j + " " + tReaded.d + " " + tReaded.k);
}
}

i:10
j:9
d:2.3
//k:0
```

---

#### **RandomAccessFile**
Java IO API中的Java RandomAccessFile类允许您移动、导航文件，并按您的意愿从文件中读取或写入文件。您也可以替换文件的现有部分。

RandomAccessFile file = new RandomAccessFile("c:\\data\\file.txt", "rw");

file.write(65); // ASCII code for A

file.seek(200);

r 读模式。调用write方法将导致一个IOException。

rw 读写模式。

rwd 读写模式-同步。对文件内容的所有更新都以同步方式写入磁盘。

rws 读取和写入模式-同步。所有对文件内容或元数据的更新都同步写入磁盘。

---

#### **Java File**

Java文件类，Java .io。Java IO API中的文件允许您访问底层文件系统。使用Java文件类，你可以:

-检查是否存在一个文件或目录。

-创建一个目录，如果它不存在。

-读取文件的长度。

-重命名或移动文件。

-删除文件。

-检查路径是否文件或目录。

-读取目录中的文件列表。

•Java File类只允许您**访问文件和目录元数据**。如果您需要读取或写入文件的内容，您应该使用任何一种方法来完成

FileInputStream, FileOutputStream或RandomAccessFile。

```java
//create a Java File
File file = new File("c:\\data\\input-file.txt");

//check if a file referenced by a Java File object exists
File file = new File("c:\\data\\input-file.txt");   //用两个//在路径中
boolean fileExists = file.exists();

//check if a directory exists
File file = new File("c:\\data");
boolean fileExists = file.exists();

//如果单个目录不存在，则使用mkdir()方法创建该目录。
File file = new File("c:\\users\\jakobjenkov\\newdir");
boolean dirCreated = file.mkdir();
//The mkdir() returns true if the directory was created, and false if not.
//The mkdirs() method will return true if all the directories were created, and false if not.
//mkdirs()将创建文件对象所表示的路径中缺少的所有目录。


//read the length in bytes of a file
File file = new File("c:\\data\\input-file.txt");
long length = file.length();

//rename (or move) a file
File file = new File("c:\\data\\input-file.txt");
boolean success = file.renameTo(new File("c:\\data\\new-file.txt"));
//renameTo()方法还可以用于将文件移动到另一个目录。传递给renameTo()方法的新文件名不必位于文件已经驻留的同一目录中。


//delete a file call the Java File delete() method.
File file = new File("c:\\data\\input-file.txt");
boolean success = file.delete();

//Java File delete() method can only delete a directory if the directory is empty.
//要删除包含文件和子目录的目录，必须遍历该目录并首先删除所有文件和子目录，然后才能删除根目录。

//before you can delete the root directory.
public static boolean deleteDir(File dir){
    File[] files = dir.listFiles();
    if(files != null){
    for(File file : files){
        if(file.isDirectory()){
        deleteDir(file);
        } else {
        	file.delete();
        	}
        }
    }
    return dir.delete();
}


//Check if Path is File or Directory
/*
*File对象既可以指向文件，也可以指向目录。通过调用文件对象的isDirectory()方法，可以检查文件对象是否指向文件或目录。如果文件指向一个目录，该方法返回true;如果文件指向一个文件，该方法返回false。
*/
File file = new File("c:\\data");
boolean isDirectory = file.isDirectory();


```

**Read List of Files in Directory**

您可以通过调用Java File list()方法或listFiles()方法来获得目录中所有文件。

 list()方法的作用是:返回一个字符串数组，其中包含文件对象所指向的文件和/或目录的名称。listFiles()返回一个文件对象数组，该数组表示文件所指向的目录中的文件和/或目录。

```java
File file = new File("c:\\data");
String[] fileNames = file.list();
File[] files = file.listFiles();
```

