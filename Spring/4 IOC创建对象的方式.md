

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

> AOP（Aspect Oriented Programming）意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP面向对象的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

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

【重点】使用AOP植入，需要导入一个依赖包！

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

**第一种方式**

**通过 Spring API 接口实现**

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
    
    <!--方式1 ：使用原生 Spring API接口-->
    <!--aop的配置-->
    <aop:config>
        <!--切入点 pointcut  expression:表达式匹配要执行的方法  execution：要执行的位置 -->
        <aop:pointcut id="pointcut" expression="execution(* com.szq.service.UserServiceImpl.*(..))"/>
        <!--执行环绕advisor; advice-ref执行方法 . pointcut-ref切入点-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

在使用spring框架配置AOP的时候，不管是通过XML配置文件还是注解的方式都需要定义pointcut"切入点"

例如定义切入点表达式  execution (* com.sample.service.impl..*.*(..))

execution()是最常用的切点函数，其语法如下所示：

 **整个表达式可以分为五个部分：**

 1、execution(): 表达式主体。

 2、第一个*号：表示返回类型， *号表示所有的类型。

 3、包名：表示需要拦截的包名，后面的两个句点表示当前包和当前包的所有子包，com.sample.service.impl包、子孙包下所有类的方法。

 4、第二个*号：表示类名， *号表示所有的类。

 5、*(..):最后这个星号表示方法名， *号表示所有的方法，后面括弧里面表示方法的参数，两个句点表示任何参数。

 

测试

```java
public class MyTest {
    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //注意 动态代理代理的是接口！！！
        UserService userService = (UserService) context.getBean("userService");
        userService.search();
    }
}
```

Aop的重要性 : 很重要 . 一定要理解其中的思路 , 主要是思想的理解这一块 .

Spring的Aop就是将公共的业务 (日志 , 安全等) 和领域业务结合起来 , 当执行领域业务时 , 将会把公共业务加进来 . 实现公共业务的重复利用 . 领域业务更纯粹 , 程序猿专注领域业务 , 其本质还是动态代理 .

----

方法二：  **自定义类来实现Aop**【主要是切面定义】

目标业务类不变依旧是userServiceImpl

第一步：写一个自己的切入类

```java
package com.szq.diy;


public class DiyPiontCut {
    public void before() {
        System.out.println("方法执行前");
    }

    public void after() {
        System.out.println("方法执行后");
    }
}

```

第二步：去spring中配置

```xml
 <!--方法2：自定义类-->
    <bean id="diy" class="com.szq.diy.DiyPiontCut"/>

    <aop:config>
        <!--自定义切面 aspect  ref 是要引用的类-->
        <aop:aspect ref="diy">
            <!--切入点-->
            <aop:pointcut id="point" expression="execution(* com.szq.service.UserServiceImpl.*(..))"/>
            <!--通知-->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>

```

3.测试

```java
import com.szq.service.UserService;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;


public class Mytest {
    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //注意 动态代理代理的是接口！！！
        UserService userService = (UserService) context.getBean("userService");
        userService.add();
    }
}

```

**第三种方式**

**使用注解实现**

第一步：编写一个注解实现的增强类

```java
package com.szq.diy;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

/**
 * @author szq
 * @version 1.0
 * @date 2020/11/12 11:13
 */

//方式3：使用注解实现AOP
@Aspect  //标注这是一个切面
public class AnnotationPointCut {
    @Before("execution(* com.szq.service.UserServiceImpl.*(..))")
    public void before() {
        System.out.println("方法执行前");
    }

    @After("execution(* com.szq.service.UserServiceImpl.*(..))")
    public void after() {
        System.out.println("方法执行后");
    }

    //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点：
    @Around("execution(* com.szq.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");

        //获得签名
        Signature signature = jp.getSignature();
        System.out.println(signature);
        //执行方法
        Object proceed = jp.proceed(); //执行方法

        System.out.println("环绕后");
        System.out.println(proceed);

    }
}

```

第二步：在Spring配置文件中，注册bean，并增加支持注解的配置

```xml
   <!--方法3：-->
    <bean id="annotationPointCut" class="com.szq.diy.AnnotationPointCut"/>
    <!--开启注解支持    JDK（默认 proxy-target-class="false"）   cglib  proxy-target-class="true"-->
    <aop:aspectj-autoproxy proxy-target-class="true"/>

```

aop:aspectj-autoproxy 说明:

通过aop命名空间的<aop:aspectj-autoproxy />声明自动为spring容器中那些配置

@aspectJ切面的bean创建代理，植入切面。

当然，spring 在内部依旧采用AnnotationAwareAspectJAutoProxyCreator进行自动代理的创建工作，但具体实现的细节已经被<aop:aspectj-autoproxy />隐藏起来了

<aop:aspectj-autoproxy />有一个proxy-target-class属性，默认为false，表示使用jdk动态代理植入增强;  当配为<aop:aspectj-autoproxy  poxy-target-class="true"/>时，表示使用CGLib动态代理技术植入增强。不过即使proxy-target-class设置为false，如果目标类没有声明接口，则spring将自动使用CGLib动态代理。

#### 12.整合Mabatis

##### 12.1 回忆mybatis

步骤：

- 在pom.xml中导入相关jar包

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>com.szq</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>mybatis-10-mybatisTest</artifactId>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.13</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.2</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.16</version>
        </dependency>
    </dependencies>


    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>

</project>
```

配置Maven静态资源过滤问题！

- 编写实体类

```java
@Data
public class User {
    private int id;
    private String name;
    private String pwd;

}
```

- 编写核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>

    <!--引入外部配置文件-->
    <properties resource="db.properties">
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </properties>

    <!--可以给实体类起别名-->
    <typeAliases>
<!--        <typeAlias type="com.User" alias="User"/>-->
        <package name="com.szq.pojo"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>

        <environment id="test">
            <transactionManager type="MANAGED"></transactionManager>
            <dataSource type="UNPOOLED"></dataSource>
        </environment>
    </environments>


    <!--每一个Mapper.xml都需要在Mybatis核心配置文件中注册-->
    <mappers>
<!--        <mapper resource="com/szq/dao/UserMapper.xml"/>-->
<!--        <mapper class="com.UserMapper"/>-->
        <package name="com.szq.dao"/>
    </mappers>


</configuration>
```

- 编写接口

```java
public interface UserMapper {
    public List<User> selectUser();

}
```

- 编写Mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--以前是写一个UserDaoImpl的实现类，现在是写mapper.xml 配置文件实现 -->
<!--namespace = 绑定一个对应的Dao/Mapper 接口-->
<mapper namespace="com.szq.dao.UserMapper">



    <select id="selectUser" resultType="com.szq.pojo.User">
        select * from user
    </select>


</mapper>
```

- 测试

```java
public class MyTest {
    @Test
    public void test() throws IOException {

        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.selectUser();

        for (User user : userList) {
            System.out.println(user);
        }

        sqlSession.close();

    }
}
```

##### 12.2 Mabatis-Spring

http://www.mybatis.org/spring/zh/index.html

MyBatis-Spring 会帮助你将 MyBatis 代码无缝地整合到 Spring 中。

1.编写数据源配置

2.sqlSessionFactory

3.SqlSessionTemplate

4.需要给接口加实现类

5.将自己写的实现类，注入到Spring中

6.测试

##### 实现1

1.引入Spring配置文件 spring-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
```

2.配置数据源替换mybatis的数据源

```xml
<!--配置数据源：数据源有非常多，可以使用第三方的，也可使使用Spring的-->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
    <property name="username" value="root"/>
    <property name="password" value="root"/>
</bean>
```

3.配置SqlSessionFactory，关联Mybatis

```xml
<!--配置SqlSessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <!--关联Mybatis-->
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
    <property name="mapperLocations" value="classpath:com/szq/dao/*.xml"/>
</bean>
```

4.注册SqlSessionTemplate,关联sqlSessionFactory

```XML
<!--注册sqlSessionTemplate , 关联sqlSessionFactory-->
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <!--利用构造器注入-->
    <constructor-arg index="0" ref="sqlSessionFactory"/>
</bean>
```

5.增加dao接口的实现类，私有化sqlSessionTemplate

```java
public class UserDaoImpl implements UserMapper {
 
    //sqlSession不用我们自己创建了，Spring来管理
    private SqlSessionTemplate sqlSession;
 
    public void setSqlSession(SqlSessionTemplate sqlSession) {
        this.sqlSession = sqlSession;
    }
 
    public List<User> selectUser() {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.selectUser();
    }
    
}
```

6.注册bean实现

```xml
<bean id="userDao" class="com.szq.dao.UserDaoImpl">
    <property name="sqlSession" ref="sqlSession"/>
</bean>
```

7.测试

```java
  @Test
    public void test() throws IOException {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapper userMapper = context.getBean("userMapper",UserMapper.class);
        for (User user : userMapper.selectUser()) {
            System.out.println(user);
        }
    }
```

此时的mybatis配置文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>


    <!--可以给实体类起别名-->
    <typeAliases>
<!--        <typeAlias type="com.User" alias="User"/>-->
        <package name="com.szq.pojo"/>
    </typeAliases>
    
</configuration>
```



##### 实现2

dao继承Support类 , 直接利用 getSqlSession() 获得 , 然后直接注入SqlSessionFactory . 比起方式1 , 不需要管理SqlSessionTemplate , 而且对事务的支持更加友好 . 可跟踪源码查看

![image-20201216201139854](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201216201139854.png)

UserMapperImpl2.java

```java
public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper{
    public List<User> selectUser() {
        return getSqlSession().getMapper(UserMapper.class).selectUser();
    }
}
```

2.增加bean配置

```xml
    <bean id="userMapper2" class="com.szq.dao.UserMapperImpl2">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>
```

3.测试

```java
    @Test
    public void test2() throws IOException {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapper userMapper = context.getBean("userMapper2",UserMapper.class);
        for (User user : userMapper.selectUser()) {
            System.out.println(user);
        }
    }
```

##### 总结

将mybatis整合到spring后就可以不使用mybatis的配置文件了，除了上面的实现方式外，还可以使用注解实现。



#### 13.声明式事务

##### 1.回顾事务

事务就是把一系列的动作当成一个独立的工作单元，这些动作要么全部完成，要么全部不起作用。

**事务四个属性ACID**

- 原子性（atomicity）

> 事务是原子性操作，由一系列动作组成，事务的原子性确保动作要么全部完成，要么完全不起作用

- 一致性（consistency）

> 一旦所有事务动作完成，事务就要被提交。数据和资源处于一种满足业务规则的一致性状态中

- 隔离性（isolation）

> 可能多个事务会同时处理相同的数据，因此每个事务都应该与其他事务隔离开来，防止数据损坏

- 持久性（durability）

> 事务一旦完成，无论系统发生什么错误，结果都不会受到影响。通常情况下，事务的结果被写到持久化存储器中

##### 2.Spring中的事务

- 声明式事务：AOP
- 编程式事务：需要在代码中，进行事务的管理

测试

1.在UserMapper接口中增加两个方法，删除和增加用户

```java
//添加一个用户
int addUser(User user);
 
//根据id删除用户
int deleteUser(int id);
```

2.mapper文件，我们故意把 deletes 写错，测试！

```java
 <insert id="addUser" parameterType="com.kuang.pojo.User">
 insert into user (id,name,pwd) values (#{id},#{name},#{pwd})
 </insert>
 
 <delete id="deleteUser" parameterType="int">
 deletes from user where id = #{id}
</delete>
```

3.编写接口的实现类

```java
public class UserMapperImpl extends SqlSessionDaoSupport implements UserMapper{
    public List<User> selectUser() {

        User user = new User(7,"小王","555554466");

        UserMapper mapper = getSqlSession().getMapper(UserMapper.class);

        mapper.addUser(user);
        mapper.deleteUser(5);
        return mapper.selectUser();
    }

    public int addUser(User user) {
        return getSqlSession().getMapper(UserMapper.class).addUser(user);
    }

    public int deleteUser(int id) {
        return getSqlSession().getMapper(UserMapper.class).deleteUser(id);
    }
}
```

4.测试

```java
    @Test
    public void test() throws IOException {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapper userMapper = context.getBean("userMapper",UserMapper.class);
        for (User user : userMapper.selectUser()) {
            System.out.println(user);
        }
    }
```

报错：sql异常，delete写错了  但是插入依然成功。。。

原因是没有进行事务的管理操作。因此需要在spring中进行配置：

##### Spring中的事务管理

Spring在不同的事务管理API之上定义了一个抽象层，使得开发人员不必了解底层的事务管理API就可以使用Spring的事务管理机制。Spring支持编程式事务管理和声明式的事务管理。

**编程式事务管理**

- 将事务管理代码嵌到业务方法中来控制事务的提交和回滚
- 缺点：必须在每个事务操作业务逻辑中包含额外的事务管理代码

**声明式事务管理**

- 一般情况下比编程式事务好用。
- 将事务管理代码从业务方法中分离出来，以声明的方式来实现事务管理。
- 将事务管理作为横切关注点，通过aop方法模块化。Spring中通过Spring AOP框架支持声明式事务管理。

**使用Spring管理事务，注意头文件的约束导入 : tx**

```xml
xmlns:tx="http://www.springframework.org/schema/tx"
 
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd">
```

**事务管理器**

- 无论使用Spring的哪种事务管理策略（编程式或者声明式）事务管理器都是必须的。
- 就是 Spring的核心事务管理抽象，管理封装了一组独立于技术的方法。

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
 </bean>
```

**配置好事务管理器后我们需要去配置事务的通知**

```xml
<!--结合AOP实现事务的置入-->
<!--配置事务的类-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!--给哪些方法配置事务-->
    <!--   配置事务的传播特性 propagation   -->
    <tx:attributes>
        <tx:method name="add" propagation="REQUIRED"/>
        <tx:method name="delete" propagation="REQUIRED"/>
        <tx:method name="update" propagation="REQUIRED"/>
        <tx:method name="query" read-only="true"/>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
```

**spring事务传播特性：**

事务传播行为就是多个事务方法相互调用时，事务如何在这些方法间传播。spring支持7种事务传播行为：

- propagation_requierd：如果当前没有事务，就新建一个事务，如果已存在一个事务中，加入到这个事务中，这是最常见的选择。**也是Spring默认的**
- propagation_supports：支持当前事务，如果没有当前事务，就以非事务方法执行。
- propagation_mandatory：使用当前事务，如果没有当前事务，就抛出异常。
- propagation_required_new：新建事务，如果当前存在事务，把当前事务挂起。
- propagation_not_supported：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
- propagation_never：以非事务方式执行操作，如果当前事务存在则抛出异常。
- propagation_nested：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与propagation_required类似的操作

Spring 默认的事务传播行为是 PROPAGATION_REQUIRED，它适合于绝大多数的情况。

**配置AOP**

导入aop的头文件！

```xml
<!--配置事务切入-->
<aop:config>
    <aop:pointcut id="txPointCut" expression="execution(* com.szq.mapper.*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
</aop:config>
```

删掉刚才插入的数据，再次测试：

```java
public class UserMapperImpl extends SqlSessionDaoSupport implements UserMapper{
    public List<User> selectUser() {

        User user = new User(7,"小王","555554466");

        UserMapper mapper = getSqlSession().getMapper(UserMapper.class);

        mapper.addUser(user);
        mapper.deleteUser(5);
        return mapper.selectUser();
    }

    public int addUser(User user) {
        return getSqlSession().getMapper(UserMapper.class).addUser(user);
    }

    public int deleteUser(int id) {
        return getSqlSession().getMapper(UserMapper.class).deleteUser(id);
    }
}
```

成功将id 5 用户删除，新增id 7 用户。



为什么需要事务？

- 如果不配置事务，可能存在数据提交不一致的情况
- 如果不在spring中配置声明式事务，就需要在代码中手动配置
- 事务在项目中的开发中十分重要，涉及到数据库的一致性和完整性问题！！！

