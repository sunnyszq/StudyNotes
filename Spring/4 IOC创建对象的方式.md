

#### 4 IOC创建对象的方式

---

1.使用无参构造创建对象。spring**默认** 若没有，报错

```java
 Failed to instantiate [com.szq.pojo.User]: No default constructor found
```

2.要使用**有参构造创建对象**

​	1.下标赋值

```xml
<!-- 1.下标赋值   对有参构造器-->
<bean id="user" class="com.szq.pojo.User">
    <constructor-arg index="0" value="szq"/>
</bean>
```

​	2.类型

```xml
<!--2.通过类型创建，不建议使用，如当多个属性是相同类型时-->
<bean id="user" class="com.szq.pojo.User">
    <constructor-arg type="java.lang.String" value="typeszq"/>
</bean>
```

​	3.参数名

```xml
<!--3.直接通过参数名来设置-->
<bean id="user" class="com.szq.pojo.User">
    <constructor-arg name="name" value="sszq"/>
</bean>
```

3.总结：

```
在配置文件加载的时候，容器中管理的对象就已经初始化了
```

#### 5 Spring配置说明

##### 5.1 别名

```xml
<!--别名，如果添加了别名，也可以使用别名来获取到这个对象-->
<alias name="user" alias="userNew"/>
```

##### 5.2  Bean的配置

```xml
<!--
    id:bean 的唯一标识符，也就是相当于对象名
    class：bean对象所对应的全限定名：包名+类型
    name:也是别名,而且更高级，可以同时取多个别名,可以用空格，逗号，分号分开
    -->
<bean id="user2" class="com.szq.pojo.User2" name="user22,u2 u3;u4">
    <property name="name" value="user22"/>

</bean>
```

##### 5.3 import

import一般用于团队开发，可以将多个配置文件，导入合并为一个

假设，现在项目中有多个人开发，这些人负责不同的类开发，不同的类需要注册在不同的bean中，我们可以**利用import将所有人的beans.xml(配置文件)合并为一个总的。**

- person1
- person2
- person3
- applicationContext.xml

```xml
<import resource="beans.xml"/>
<import resource="beans2.xml"/>
<import resource="beans3.xml"/>
```

使用的时候，直接使用总的配置applicationContext.xml就可以了

#### 6. DI依赖注入

##### 6.1 构造器注入

见前面4.2

##### 6.2 Set方式注入【重点】

- 依赖注入：set注入
  - 依赖：bean对象的创建依赖于容器
  - 注入：bean对象中的所有属性，由容器来注入
- 【环境搭建】

1.复杂类型

```java
public class Address {
    private String adderss;

    public String getAdderss() {
        return adderss;
    }

    public void setAdderss(String adderss) {
        this.adderss = adderss;
    }

    @Override
    public String toString() {
        return "Address{" +
                "adderss='" + adderss + '\'' +
                '}';
    }
}
```



2.真实测试对象

```java
public class Student {
    private String name;
    private Address address; //引用对象

    private String[] book;
    private List<String> hobbys;
    private Map<String,String> card;
    private Set<String> games;
    private String wife;
    private Properties info;
    
    //seter and getter
}
```

3.beans.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="student" class="com.szq.pojo.Student">
        <!--1.普通值注入，value=""-->
        <property name="name" value="xiaoming"/>
    </bean>

</beans>
```



4.测试类

```java
public class Mytest {
    public static void main(String[] args) {
       ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student student = (Student) context.getBean("student");
        System.out.println(student.getName());
        System.out.println(student.getAddress());
    }
}
```

完善注入信息：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="address" class="com.szq.pojo.Address">
        <property name="adderss" value="苏州"/>
    </bean>

    <bean id="student" class="com.szq.pojo.Student">
        <!--1.普通值注入，value=""-->
        <property name="name" value="xiaoming"/>
        <!--2.bean注入，ref=-->
        <property name="address" ref="address"/>        <!--ref = 引用的id-->
        <!--3.数组注入-->
        <property name="book">
            <array>
                <value>红楼梦</value>
                <value>三国</value>
                <value>西游记</value>
                <value>水浒传</value>
            </array>
        </property>

        <!--list注入-->
        <property name="hobbys">
            <list>
                <value>听歌</value>
                <value>看电影</value>
                <value>coding</value>
            </list>
        </property>
        <!--map注入-->
        <property name="card">
            <map>
                <entry key="身份证" value="123456555546666"/>
                <entry key="银行卡" value="12222222222222222"/>
            </map>
        </property>

        <!--set-->
        <property name="games">
            <set>
                <value>LOL</value>
                <value>COC</value>
                <value>BOB</value>
            </set>
        </property>

        <!--null-->
        <property name="wife">
            <null/>
        </property>
        <!--properties-->
        <property name="info">
            <props>
                <prop key="学号">20200525</prop>
                <prop key="性别">男</prop>
                <prop key="username">root</prop>
                <prop key="password">123456</prop>
            </props>
        </property>
    </bean>


</beans>
```

##### 6.3 拓展方式注入

我们可以使用**p命名空间和c命名空间进行注入**

区别：

- p命名空间注入，可以直接注入属性的值 p:property  p注入可以没有带参数的构造方法
- c命名空间注入，通过带参数的构造器注入：construct-args

官方解释：

![img](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201107205719289.png)

使用：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--p命名空间注入，可以直接注入属性的值 p:property  p注入可以没有带参数的构造方法-->
    <bean id="user" class="com.szq.pojo.User" p:age="18" p:name="szq"/>

    <!--c命名空间注入，通过带参数的构造器注入：construct-args -->
    <bean id="user2" class="com.szq.pojo.User" c:age="20" c:name="sss"/>

</beans>
```

测试：

```java
@Test
public void test2() {
    ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
    User user = context.getBean("user2",User.class);   //context.getBean("user",User.class)  最后声明User.class 就不用强转类型了
    System.out.println(user);

}
```

注意点：p命名和c命名空间不能直接使用，需要导入xml约束。

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

##### 6.4 bean的作用域 beans scope

![image-20201108092206961](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201108092206961.png)

1.单例模式 singleton（默认）

![image-20201108092142679](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201108092142679.png)

单例模式是spring默认的，也可以显示的说明。只管理一个单例bean的共享实例，并且所有对具有一个或多个ID的bean的请求都与该bean定义相匹配，从而导致Spring容器返回一个特定的bean实例。

```xml
<bean id="user2" class="com.szq.pojo.User" c:age="20" c:name="sss" scope="singleton"/>
```

单例模式生成的对象只有一个，测试如下：

```java
@Test
public void test2() {
    ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
    User user = context.getBean("user2",User.class);   //context.getBean("user",User.class)  最后声明User.class 就不用强转类型了
    User user2 = context.getBean("user2",User.class);

    System.out.println(user.hashCode());
    System.out.println(user2.hashCode());
    System.out.println(user == user2);

}


out:
1381128261
1381128261
true
```

2.原型模式  prototype

原型模式中每次对特定bean发出请求时都创建一个新的bean实例,每次从容器中get的时候，都会产生一个新对象。

![img](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201108093556369.png)

使用时，修改xml

```xml
<bean id="user2" class="com.szq.pojo.User" c:age="20" c:name="sss" scope="prototype"/>
```

测试：

```java
    @Test
    public void test2() {
       ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
        User user = context.getBean("user2",User.class);   //context.getBean("user",User.class)  最后声明User.class 就不用强转类型了
        User user2 = context.getBean("user2",User.class);

        System.out.println(user.hashCode());
        System.out.println(user2.hashCode());
        System.out.println(user == user2);

    }

out:
1381128261
999609945
false
```

3.其余的：Request, Session, Application, and WebSocket Scopes ，这些只能在web开发中使用到。

#### 7.Beans的自动装配

- 自动装配是spring满足bean依赖的一种方式！
- spring会在上下文中自动寻找，并自动给bean装配属性。

在spring中有三种装配的方式：

1. 在xml中显示的配置
2. 在java中显示配置
3. 隐式的自动装配bean（重要）

##### 7.1 测试

环境搭建：一个人有两个宠物。



##### 7.2 byName自动装配

```xml
<bean id="cat" class="com.szq.pojo.Cat"/>
<bean id="dog" class="com.szq.pojo.Dog"/>
<!--
        byName: 会在容器上下文中查找，和自己对象set方法后面的值对应的beanid   
				名字要唯一
    -->
<bean id="people" class="com.szq.pojo.People" autowire="byName">
    <property name="name" value="szq"/>
</bean>
```

##### 7.3 byType自动装配

```xml
<bean id="cat" class="com.szq.pojo.Cat"/>
<bean id="dog1111" class="com.szq.pojo.Dog"/>


<bean class="com.szq.pojo.Cat"/>
<bean class="com.szq.pojo.Dog"/>

<!--
        byName: 会在容器上下文中查找，和自己对象set方法后面的值对应的beanid   名字要唯一
        byType:会在容器上下文中查找，和自己对象属性类型相同的bean，注意要保证类型全局唯一  在bean中可以省略id
    -->
<bean id="people" class="com.szq.pojo.People" autowire="byType">
    <property name="name" value="szq"/>
</bean>
```

小结：

byName的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致。

byType的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致。

##### 7.4 使用注解实现自动装配

jdk1.5支持注解，Spring2.5开始支持注解。

The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML. The short answer is “it depends.” 

使用注解须知

1. 导入约束  context 约束
2. **配置注解的支持  <context:annotation-config/>**   

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

@Autowired

直接在属性上使用即可，也可以在set方式上使用。

使用Autowired可以不编写set方法，前提是这个自动装配的属性在IOC（Spring）容器中存在，且符合名字byName

科普：

```xml
@Nullable  如果字段标记了这个注解，说明这个字段可以为null

public People(@Nullable String name) {
	this.name = name;
}
```

```java
public @interface Autowired {
    boolean required() default true;
}
```

测试代码：

```java
public class People {
    //如果显示的定义了Autowired的required属性为false，说明这个对象可以为null，否则不允许为空
    @Autowired(required = false)
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;
}
```

如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired】完成的时候，我们可以使用@Qualifier(value="xxx")去配置@Autowired的使用，指定一个唯一的bean对象注入。

```java
public class People {
    @Autowired
    @Qualifier(value = "cat111")
    private Cat cat;
    @Autowired
    @Qualifier(value = "dog222")
    private Dog dog;
    private String name;
}


    <bean id = "cat111" class="com.szq.pojo.Cat"/>
    <bean id = "cat11" class="com.szq.pojo.Cat"/>

    <bean id = "dog222" class="com.szq.pojo.Dog"/>
    <bean id = "dog22" class="com.szq.pojo.Dog"/>
```

**@Resource注解**

```java
public class People {

    @Resource(name = "cat2")
    private Cat cat;
    @Resource
    private Dog dog;
    private String name;
}
```

小结：

@Resource和@Autowired区别：

- 都是用来自动装配的，都可以放在属性字段上
- @Autowired通过byType的方式实现，且要求这个对象必须存在！【常用】
- @Resource默认通过byName的方式实现，如果找不到名字，就通过byType实现。如果两个都找不到的情况下，报错。

- 执行顺序不同：@Autowired通过byType的方式实现，@Resource默认通过byName的方式实现



#### 8.使用注解开发

在spring4之后，要使用注解开发，必须要保证aop的包导入了。

![image-20201108175430863](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201108175430863.png)

使用注解需要导入context约束，增加注解的支持。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```



1.bean

2.属性如何注入

```java
//Component组件
//等价于 <bean id="user" class="com.szq.pojo.User"/>
@Component
public class User {

    //相当于 <property name="name" value="szq"/>
//    @Value("szq")
    public String name;

    @Value("szq")
    public void setName(String name) {
        this.name = name;
    }
}
```

3.衍生的注解

@Component有几个衍生注解，我们在web开发中，会按照mvc三层架构分层。

- dao 【@Repository】
- service【@Service】
- controller【@Controller】

这四个注解功能是一样的，都是代表么某个类注册到Spring中，装配bean

4.自动装配

```xml
- @Autowired：自动装配通过类型--名字
    如果Autowired不能唯一自动装配上属性，则需要通过@Qualifier(value="xxx");
- @Nullable  如果字段标记了这个注解，说明这个字段可以为null
- @Resource :自动装配通过 名字--类型
```

5.作用域

```java
@Component
@Scope("singleton")
//@Scope("prototype")
public class User {

    //相当于 <property name="name" value="szq"/>
//    @Value("szq")
    public String name;

    @Value("szq")
    public void setName(String name) {
        this.name = name;
    }
}
```

6.小结：

xml与注解：

- xml更加万能，适用于任何场合，维护简单方便。
- 注解 不是自己的类实现不了，维护相对复杂

xml与注解最佳实践：

- xml用来管理bean
- 注解只负责完成属性的注入
- 我们在使用的过程中只需要注意一个问题：必须让注解生效，就需要开启注解的支持。

```xml
    <!--指定要扫描的包，这个包下的注解就会生效-->
    <context:component-scan base-package="com.szq"/>
    <context:annotation-config/>
```



#### 9.使用java的方式配置Spring

我们现在完全不使用Spring的xml配置了，全权交给java来做

JavaConfig是Spring的一个子项目，在Spring4之后，成为了一个核心功能

![image-20201108185214477](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201108185214477.png)

实体类：

```java
//@Component 把User类注册到容器中，被Spring接管
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("szq")      //属性注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

配置文件：

```java

//也会被Spring容器托管，注册到容器中，因为他本来就是一个@Component
//@Configuration 代表这是一个配置类，效果和beans.xml效果一样
@Configuration
@ComponentScan("com.szq.pojo")
@Import(MyConfig2.class)
public class MyConfig {

    //注册一个bean，相当于之前写的一个bean标签，
    //这个方法的名字，就相当于bean标签中的id属性
    //方法的返回值，相当于bean标签中的class属性
    @Bean
    public User getUser() {
        return new User();      //返回要注入的bean对象
    }
}
```

测试类：

```java
public class Mytest {
    public static void main(String[] args) {

        //如果完全使用了配置类方式去做，我们就只能通过AnnotationConfig 上下文来获取容器，通过配置类的class对象加载
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        User getUser = (User) context.getBean("getUser");
        System.out.println(getUser.getName());
    }
}
```

这种纯java的配置方式，在SpringBoot中随处可见。

#### 10  代理模式

Spring AOP的底层是代理模式。

代理模式的分类：

- 静态代理
- 动态代理

##### 10.1静态代理

角色分析：

- 抽象角色：一般使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附加操作
- 客户：访问代理对象的人

代码步骤：

1. 接口

```java
//租房
public interface Rent {
    public void rent();
}
```

​	2.真实角色

```java
//房东
public class Host implements Rent{

    public void rent() {
        System.out.println("房东要出租房子");
    }
}
```

​	3.代理角色

```java
public class Proxy implements Rent{
    private Host host;

    public Proxy() {
    }

    public Proxy(Host host) {
        this.host = host;
    }

    public void rent() {
        seeHouse();
        host.rent();
        contract();
        fare();

    }
    //看房
    public void seeHouse(){
        System.out.println("中介带你看房");
    }

    //签租赁合同
    public void contract() {
       System.out.println("Contract ");
    }

    //收中介费
    public  void fare() {
        System.out.println("收中介费");
    }
}
```

​	4.客户端访问代理角色

```java
public class Client {
    public static void main(String[] args) {
        //房东要出租房子
        Host host = new Host();
        //代理  中介帮房东租房子，还有一些别的附加操作
        Proxy proxy = new Proxy(host);
        
        //不用找房东，直接找中介即可
        proxy.rent();
    }
}
```

代理模式的优点：

- 可以使真实角色的操作更加纯粹，不用去关注一些公共的业务
- **公共业务交给代理角色，实现了业务的分工**
- 公共业务发生扩展的时候，方便集中管理

缺点：

- 一个真实角色就会产生一个代理角色，代码量会翻倍，开发效率会降低。

##### 10.2加深理解

代码：

1.接口

```java
public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void query();


}
```



2.真实角色

```java
public class UserServiceImpl implements UserService {

    public void add() {
//        System.out.println("使用add");
        System.out.println("增加一个用户");
    }

    public void delete() {
        System.out.println("删除一个用户");
    }

    public void update() {
        System.out.println("修改一个用户");
    }

    public void query() {
        System.out.println("查询一个用户");
    }

    //1.改动原有的代码,是大忌
}
```



3.代理角色

```java
public class UserServiceProxy implements UserService{

    private UserServiceImpl userService;

    public void setUserService(UserServiceImpl userService) {
        this.userService = userService;
    }

    public void add() {
        log("add");
        userService.add();
    }

    public void delete() {
        log("delete");
        userService.delete();
    }

    public void update() {
        log("update");
        userService.update();
    }

    public void query() {
        log("query");
        userService.query();
    }

    //日志方法
    public void log(String msg) {
        System.out.println("[Debug]使用了" + msg + "方法");
    }
}
```



4.客户端访问代理角色

```java
public class Client {
    public static void main(String[] args) {
        UserServiceImpl userService = new UserServiceImpl();

        UserServiceProxy proxy = new UserServiceProxy();
        proxy.setUserService(userService);
        proxy.add();

    }
}
```

我们在不改变原来的代码的情况下，实现了对原有功能的增强，这是AOP中最核心的思想

聊聊AOP：纵向开发，横向开发

![img](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy91SkRBVUtyR0M3TG9laWNQMU8ybmZ5QTZIMFhQYTlqTUxKcWNnaWNBNWFFS3R4WWliZ0xQaWNOZkR3aWNLSW45TmxGbDg2cnJpYVZSaWNLbkVYbFBOaWFjYkhpYUxpYncvNjQw)

##### 10.3 动态代理

- 动态代理和静态代理角色一样
- 动态代理的代理类是动态生成的，不是我们直接写好的。
- 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  - 基于接口---JDK 动态代理 【学习使用】
  - 基于类：cglib
  - java字节码实现：javasist



需要了解两个类：Proxy:代理  InvocationHandler：调用处理程序

动态代理的好处：

- 可以使真实角色的操作更加纯粹，不用去关注一些公共的业务
- **公共业务交给代理角色，实现了业务的分工**
- 公共业务发生扩展的时候，方便集中管理
- 一个动态代理类代理的是一个接口，一般就是对应的一类业务r
- 一个动态代理类可以代理多个类，只要是实现了同一个接口即可。

#### 11 AOP

##### 11.1 什么是AOP

> AOP（Aspect Oriented Programming）意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy91SkRBVUtyR0M3SkFlVFlPYWFINnJaNldtTExnd1FMSGY1cG1IMzBnajZtWm04MVBDN2lhdWljRnU1NXNpY0p0c3BVN0szdlRDVmRaQ0RUU0hxN0Q1WEhsdy82NDA?x-oss-process=image/format,png)

##### 11.2 Aop在Spring中的作用

提供声明式事务；允许用户自定义切面

以下名词需要了解下：

- 横切关注点：跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志 , 安全 , 缓存 , 事务等等 ....
- 切面（ASPECT）：横切关注点 被模块化 的特殊对象。即，它是一个类。
- 通知（Advice）：切面必须要完成的工作。即，它是类中的一个方法。
- 目标（Target）：被通知对象。
- 代理（Proxy）：向目标对象应用通知之后创建的对象。
- 切入点（PointCut）：切面通知 执行的 “地点”的定义。
- 连接点（JointPoint）：与切入点匹配的执行点。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy91SkRBVUtyR0M3SkFlVFlPYWFINnJaNldtTExnd1FMSFZPWjFKcFJiN1ZpYXByWkNSWHNVYkgwYlpwaWJpYVRqcWliNjhMUUhPV1ppY1N2dVU4WTFkcXVVVkd3LzY0MA?x-oss-process=image/format,png)

SpringAOP中，通过Advice定义横切逻辑，Spring中支持5种类型的Advice:

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy91SkRBVUtyR0M3SkFlVFlPYWFINnJaNldtTExnd1FMSGJBV0g4aGFVUWVKMExWQnh4WDBpY0M1VFpsQmtFQkdpYmliZXk3akZyQ2JpYlB6UWNSaGtORmNHQUEvNjQw?x-oss-process=image/format,png)

**即 Aop 在 不改变原有代码的情况下 , 去增加新的功能 .**

##### 11.3 使用Spring实现Aop

【重点】使用AOP织入，需要导入一个依赖包！

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

**第一种方式**

**通过 Spring API 实现**

首先编写我们的业务接口和实现类

```java
public interface UserService {
 
    public void add();
 
    public void delete();
 
    public void update();
 
    public void search();
 
}
```

```java
public class UserServiceImpl implements UserService{
 
    @Override
    public void add() {
        System.out.println("增加用户");
    }
 
    @Override
    public void delete() {
        System.out.println("删除用户");
    }
 
    @Override
    public void update() {
        System.out.println("更新用户");
    }
 
    @Override
    public void search() {
        System.out.println("查询用户");
    }
}
```

然后去写我们的**增强类** , 我们编写两个 , **一个前置增强 一个后置增强**

```java
public class Log implements MethodBeforeAdvice {
 
    //method : 要执行的目标对象的方法
    //objects : 被调用的方法的参数
    //Object : 目标对象
    @Override
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println( o.getClass().getName() + "的" + method.getName() + "方法被执行了");
    }
}
```

```java
public class AfterLog implements AfterReturningAdvice {
    //returnValue 返回值
    //method被调用的方法
    //args 被调用的方法的对象的参数
    //target 被调用的目标对象
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了" + target.getClass().getName()
        +"的"+method.getName()+"方法,"
        +"返回值："+returnValue);
    }
}
```

最后去spring的文件中注册 , 并实现aop切入实现 , 注意导入约束 .

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userService" class="com.szq.service.UserServiceImpl"/>
    <bean id="log" class="com.szq.log.Log"/>
    <bean id="afterLog" class="com.szq.log.AfterLog"/>
    <!--aop的配置-->
    <aop:config>
        <!--切入点  expression:表达式匹配要执行的方法-->
        <aop:pointcut id="pointcut" expression="execution(* com.szq.service.UserServiceImpl.*(..))"/>
        <!--执行环绕; advice-ref执行方法 . pointcut-ref切入点-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

测试

```java
public class MyTest {
    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        UserService userService = (UserService) context.getBean("userService");
        userService.search();
    }
}
```

Aop的重要性 : 很重要 . 一定要理解其中的思路 , 主要是思想的理解这一块 .

Spring的Aop就是将公共的业务 (日志 , 安全等) 和领域业务结合起来 , 当执行领域业务时 , 将会把公共业务加进来 . 实现公共业务的重复利用 . 领域业务更纯粹 , 程序猿专注领域业务 , 其本质还是动态代理 .

