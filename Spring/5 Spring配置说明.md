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