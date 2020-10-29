### 泛型Generics

Code that uses generics has many **benefits** over non-generic code:
• Stronger type checks at compile time.更强的编译时类型检查
• Elimination of casts. 消除强制类型转换

```java
// without generics
List list = new ArrayList();
list.add("hello");
String s = (String) list.get(0); // requires casting
// use generics
List<String> list = new ArrayList<String>();
list.add("hello");
String s = list.get(0); // no cast
```

• By using generics, programmers can implement generic algorithms
that work on collections of different types, can be customized, and are
type safe and easier to read.

通过使用泛型，程序员可以实现在不同类型的集合上工作的泛型算法，可以定制，是类型安全的，更容易阅读。

```java
public class Box<T> {
// T stands for "Type" private T t;
	public void set(T t) {
	this.t = t;
}
public T get() {
	return t;
	}
}
```

All occurrences of Object are replaced by T. A type variable can be any
non-primitive type you specify: any class type, any interface type, any
array type, or even another type variable.

类型变量可以是您指定的任何非原语类型:任何类类型、任何接口类型、任何数组类型，甚至其他类型变量。

 This same technique can be applied to create generic interfaces.同样的技术也可以用于创建通用接口

按照惯例，类型参数名称是单个大写字母。这与变量命名规则形成了鲜明的对比。最常用的类型参数名称有:

```java
• E - Element (used extensively by the Java Collections Framework)
• K - Key
• N - Number
• T - Type
• V - Value
• S,U,V etc. - 2nd, 3rd, 4th types
    
•E -元素(被Java Collections框架广泛使用)
•K -键
•N -数
T型
•V值
•S,U,V等-第二，第三，第四种
```

```java
Box<Integer> integerBox = new Box<Integer>();
/**
In Java SE 7 and later, you can replace the type arguments required to
invoke the constructor of a generic class with an empty set of type
arguments (<>) as long as the compiler can determine, or infer, the
type arguments from the context.
**/
Box<Integer> integerBox = new Box<>();
```

```java
//Multiple Type Parameters 多个类型参数
public interface Pair<K, V> {
	public K getKey();
	public V getValue();
}
public class OrderedPair<K, V> implements Pair<K, V> {
    private K key;
    private V value;
    public OrderedPair(K key, V value) {
    this.key = key;
    this.value = value;
}
    public K getKey() { return key; }
    public V getValue() { return value; }
}

Pair<String, Integer> p1 = new OrderedPair<String, Integer>("Even", 8);
Pair<String, String> p2 = new OrderedPair<String, String>("hello", "world");
```

---

**Parameterized Types 参数化类型**

You can also substitute a type parameter (i.e., K or V) with a
parameterized type (i.e., List<String>).

For example, using the OrderedPair<K, V> example:

```java
OrderedPair<String, Box<Integer>> p = new OrderedPair<>("primes", new Box<Integer>(...));
```

**Raw Types  原生类型**

A raw type is the name of a generic class or interface without any type arguments.原生类型是**没有任何类型参数**的**泛型类或接口**的名称

```java
//For example, given the generic Box class:
public class Box<T> {
	public void set(T t) { /* ... */ }
// ...
}

//To create a parameterized type of Box<T>, you supply an actual type
//argument for the formal type parameter T:
Box<Integer> intBox = new Box<>();

//If the actual type argument is omitted, you create a raw type of Box<T>:
Box rawBox = new Box();
//Therefore, Box is the raw type of the generic type Box<T>. However,
//a non-generic class or interface type is not a raw type.

```

如果**省略了实际类型参数，则创建原始类型**< T >:Box rawBox = new Box();

因此，**Box是泛型类型Box<T>的原生类型**。



但是如果你将一个原始类型赋值给一个参数化类型，你会得到一个警告:

> ```java
> Box rawBox = new Box();  // rawBox is a raw type of Box<T>
> Box<Integer> intBox = rawBox; // **warning**: unchecked conversion
> ```
>
> 

如果使用原始类型调用在相应泛型类型中定义的泛型方法，也会得到警告:

```java
Box<String> stringBox = new Box<>();
Box rawBox = stringBox;
rawBox.set(8); // warning: unchecked invocation to set(T)
```

The warning shows that raw types bypass generic type checks,
deferring the catch of unsafe code to runtime. Therefore, you should
avoid using raw types

该警告显示原始类型绕过泛型类型检查，将不安全代码的捕获延迟到运行时。因此，**应该避免使用原始类型**

---

**泛型方法 Generic Methods**

Generic methods are methods that introduce their own type  parameters.

泛型方法是引入它们自己的类型参数的方法。

Static and non-static generic methods are allowed, as well as generic class constructors.

允许使用静态和非静态泛型方法以及泛型类构造函数。

```java
public class Util {
// Generic static method
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
        	p1.getValue().equals(p2.getValue());
    }
}
    public class Pair<K, V> {
        private K key; private V value;
// Generic constructor
        public Pair(K key, V value) {
        this.key = key; this.value = value;
    }
// Generic methods
    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    public K getKey() { return key; }
    public V getValue() { return value; }
}

Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.<Integer, String>compare(p1, p2);

//Generally, this can be left out and the compiler will infer the type that
//is needed:一般来说，这可以忽略，编译器会推断出需要的类型
Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.compare(p1, p2);

```

This feature, known as type inference, allows you to invoke a generic
method as an ordinary method, without specifying a type between
angle brackets.

这个特性称为**类型推断**，允许您将**泛型方法作为普通方法调用**，而不**需要在尖括号之间指定类型**。

---

**Bounded Type Parameters**   **受限的类型参数**

There may be times when you want to restrict the types that can be
used as type arguments in a parameterized type.

在参数化类型中，可能需要限制可作为类型参数使用的类型。

-例如，一个对数字进行操作的方法可能只希望接受Number或其子类的实例。

这就是有界类型参数的作用。

要声明有界类型参数，请列出类型参数的名称，后跟extends关键字，再加上它的上限，在本例中为Number。

```java
public class Box<T> {
    private T t;
    public void set(T t) {
    this.t = t;
	}
	public T get() {
		return t;
}
public <U extends Number> void inspect(U u){
    System.out.println("T: " + t.getClass().getName());
    System.out.println("U: " + u.getClass().getName());
	}
public static void main(String[] args) {
    Box<Integer> integerBox = new Box<Integer>();
    integerBox.set(new Integer(10));
    integerBox.inspect("some text"); // error: this is still String!
	}
}
```

---

**Multiple Bounds**  **多重限定**

```java
<T extends B1 & B2 & B3>
```

具有多个边界的类型变量是边界中列出的所有类型的子类型。

如果**其中一个边界是类，则必须首先指定它**。例如:

```java
Class A { /* ... */ }
interface B { /* ... */ }
interface C { /* ... */ }
class D <T extends A & B & C> { /* ... */ }
```

如果不首先指定绑定A，你会得到一个编译时错误:

```java
class D <T extends B & A & C> { /* ... */ } // compile-time error
```

You cannot use the > operator to compare objects . **不能使用>操作符比较对象**

```java
public static <T> int countGreaterThan(T[] anArray, T elem) {
    int count = 0;
    for (T e : anArray)
    if (e > elem)   // compiler error
    ++count;
    return count;
}


//To fix the problem, use a type parameter bounded by the Comparable<T>        interface:
public interface Comparable<T> {
	public int compareTo(T o);
}
//The resulting code will be:
public static <T extends Comparable<T>> int countGreaterThan(T[] anArray, T elem) {
    int count = 0;
    for (T e : anArray)
    if (e.compareTo(elem) > 0)
    ++count;
    return count;
}
```

**Generics, Inheritance, and Subtypes   泛型、继承和子类型**

```java
//例如，你可以给一个对象分配一个整数，因为对象是Integer的超类型之一:
Object someObject = new Object();
Integer someInteger = new Integer(10);
someObject = someInteger; // OK

//整数也是一种数字，所以下面的代码也是有效的:
public void someMethod(Number n) { /* ... */ }
someMethod(new Integer(10)); // OK
someMethod(new Double(10.1)); // OK

//对于泛型也是如此。可以执行泛型类型调用
Box<Number> box = new Box<Number>();
box.add(new Integer(10)); // OK
box.add(new Double(10.1)); // OK
```

有一个方法：

public void boxTest(Box<Number> n) { /* ... */ }

它接受一个类型为Box<Number>的参数。是否允许像预期的那样传入Box<Integer>或Box<Double> ?

答案是“否”，因为Box<Integer>和Box<Double>不是Box<Number>的子类型。

![image-20201029215226012](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201029215226012.png)

可以通过扩展或实现泛型类或接口来子类化它。

一个类或接口的类型参数与另一个类或接口的类型参数之间的关系由extends和implements子句决定。

只要不改变类型参数，类型之间的子类型关系就会保留。

![image-20201029215711941](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201029215711941.png)

现在假设我们想定义自己的列表接口PayloadList，它将泛型类型P的可选值与每个元素关联起来。

它的声明可能类似于:

```java
interface PayloadList<E,P> extends List<E> {
	void setPayload(int index, P val);
	...
}
```

![image-20201029215831470](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201029215831470.png)

