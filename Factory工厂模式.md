[TOC]
 
### 1.定义
    
    任何可以产生对象的方法或类，都可以称之为工厂
    单例也是一种工厂，也叫静态工厂
    不可咬文嚼字，死抠概念
    为什么有了new之后，还要有工厂？
        -灵活控制生产过程
        -权限、修饰、日志。。。
    
    简单工厂
    静态工厂
        
    工厂方法
    抽象工厂
        
### 2.举例
    
    任意定制交通工具
        继承MoveAble
    任意定制生产过程
        Moveable XXXFactory.create();
    任意定制产品一族
     
### 3.简单工厂

==简单工厂的可扩展性不好==

```
package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName Moveable
 * @Description: 接口，形容词
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public interface Moveable {
    void run();
}


package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName Car
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Car implements Moveable {
    @Override
    public void run() {
        System.out.println(" car run");
    }
}


package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName Flain
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Flain implements Moveable {
    @Override
    public void run() {
        System.out.println(" flain fly");
    }
}


package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName SimpleFactory
 * @Description: 简单工厂的可扩展性不好
 * @Author 李淼
 * @Date 2020/6/18
 * @Version V1.0
 **/
public class SimpleFactory {
    public Car createCar(){
        return new Car();
    }

    public Flain createFlain(){
        return new Flain();
    }
}

```

### 4.工厂方法 FactoryMethod

是抽象工厂的一个特例

只生产一个产品

==好处：对产品的扩展，新加一个产品：汽车(新增Car、CarFactory)、飞机(Flain、FlainFactory)==

==产品纬度进行扩展==

![004](0F0471B155D24BAF9BA284F71BD7D46A)  


> public ==interface== Moveable {
>     void run();
> }

> public ==abstract class== Factory {
>     abstract Moveable create();
> }


```
package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName Moveable
 * @Description: 接口，形容词
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public interface Moveable {
    void run();
}


package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName Car
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Car implements Moveable {
    @Override
    public void run() {
        System.out.println(" car run");
    }
}


package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName Flain
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Flain implements Moveable {
    @Override
    public void run() {
        System.out.println(" flain fly");
    }
}

package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName Factory
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public abstract class Factory {
    abstract Moveable create();
}

package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName CarFactory
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class CarFactory extends Factory {
    @Override
    Moveable create() {
        return new Car();
    }
}

package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName FlainFactory
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class FlainFactory extends Factory {
    @Override
    Moveable create() {
        return new Flain();
    }
}

package com.mashibing.dp.factorymethod.mycode;

/**
 * @ClassName Main
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Main {
    public static void main(String[] args) {
       Moveable moveable = new CarFactory().create();
       moveable.run();

       Moveable moveable1 = new FlainFactory().create();
       moveable1.run();
    }
}

```



### 5.抽象工厂 AbstractFactory

==形容词用接口，名词用抽象类==

==抽象的工厂、抽象的产品==

==好处：对产品族的扩展，新加一个产品族：人(面包、汽车、AK47)、动物（肉、腿、木棍）、魔法师（蘑菇、扫帚、魔法棒）==

==产品族进行扩展==
    
    作业：用抽象工厂，完成一键风格替换
    类似：皮肤的切换
        产品族：坦克、子弹、爆炸
        产品族：自己坦克、敌军坦克
![003](4006611B0F9B43F692FBD0A06C6C6ABA)



> ==public abstract class AbstractFactory {==
> 
> abstract Food createFood();
>     
> abstract Vehicle createVehicle();
>     
> abstract Weapon createWeapon();
>     
> }
> 
> ==public abstract class Food {==
> 
> abstract void eat();
>     
> }
> 


```
package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Food
 * @Description: 抽象的产品
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public abstract class Food {
    abstract void eat();
}

package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Bread
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Bread extends Food {
    @Override
    void eat() {
        System.out.println("eat Bread");
    }
}


package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Meat
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Meat extends Food {
    @Override
    void eat() {
        System.out.println("eat meat");
    }
}


package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Vehicle
 * @Description: 抽象的产品
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public abstract class Vehicle {
    abstract void run();
}

package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Car
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Car extends Vehicle {
    @Override
    void run() {
        System.out.println("car run");
    }
}

package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Leg
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Leg extends Vehicle {
    @Override
    void run() {
        System.out.println(" leg run");
    }
}

package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Weapon
 * @Description: 抽象的产品
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public abstract class Weapon {
    abstract void shoot();
}


package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName AK47
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class AK47 extends Weapon {
    @Override
    void shoot() {
        System.out.println("AK47  shoot");
    }
}


package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Mugun
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Mugun extends Weapon {
    @Override
    void shoot() {
        System.out.println(" mugun  hahah");
    }
}



package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName AbstractFactory
 * @Description: 抽象的工厂
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public abstract class AbstractFactory {
    abstract Food createFood();
    abstract Vehicle createVehicle();
    abstract Weapon createWeapon();
}


package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName AnimalFactory
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class AnimalFactory extends AbstractFactory {
    @Override
    Meat createFood() {
        return new Meat();
    }

    @Override
    Vehicle createVehicle() {
        return new Leg();
    }

    @Override
    Weapon createWeapon() {
        return new Mugun();
    }
}


package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName PersonFactory
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class PersonFactory extends AbstractFactory {
    @Override
    Food createFood() {
        return new Bread();
    }

    @Override
    Vehicle createVehicle() {
        return new Car();
    }

    @Override
    Weapon createWeapon() {
        return new AK47();
    }
}

package com.mashibing.dp.abstractfactory.mycode;

/**
 * @ClassName Main
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Main {
    public static void main(String[] args) {
//        AbstractFactory factory = new PersonFactory();
        AbstractFactory factory = new AnimalFactory();
        Food food = factory.createFood();
        food.eat();
        Vehicle vehicle = factory.createVehicle();
        vehicle.run();

        Weapon weapon = factory.createWeapon();
        weapon.shoot();
    }
}


```

### 6.抽象工厂模式在Tank中的应用

具体的应用：使用抽象工厂模式，动态添加产品族（矩形的坦克、子弹、爆炸效果）

==请记住：在使用抽象工厂模式时，父子类，父类里抽象的东西应该尽可能的少；==

因为父类抽象的东西越多，子类需要实现的东西就越多

继承：父类对子类进行抽象时，不要一下子多很多东西的抽象，用到什么东西抽象什么东西


这次demo中使用抽象工厂模式，后续会将抽象工厂的代码移除；因为这样使代码变复杂了

因为已经使用了策略模式，所以在做抽象工厂的时候，抽象父类需要添加很多属性；所以决定先去掉策略模式的代码，即删除 开火相关的策略模式

具体的类：DefaultFireStrategy、FireStrategy、FourDirectionFireStrategy

使用抽象工厂的好处：新增一个产品族很方便，坏处就是需要新增很多类

==面向接口编程、面向抽象编程==

![006](886A76CE545840DB930F1FF5152E0395)
代码：day07abstractFactory

### 7.Spring IOC

==基于工厂方法和抽象工厂的缺点，介绍SpringbeanFactory（完美的解决了上面两种的缺点）==

==Spring IOC使用了反射，还不是java的反射（java的反射效率太低了），它使用的反射效率更高，可以直接操作源码==


1.新建一个分支：dp_gamemodel

2.使用Gradle创建工程：HelloSpring

3.在build.gradle文件中dependencies增加依赖：
compile group:'org.springframework',name: 'spring-context',version:'5.1.6.RELEASE'

4.新建spring的application.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-4.2.xsd">

    <bean id="d" class="com.mashibing.Driver"></bean>
    <bean id="tank" class="com.mashibing.Tank">
        <property name="driver" ref="d"></property>
    </bean>

</beans>
```
    
5.IOC控制反转 /DI依赖注入
   
```
package com.mashibing;

/**
 * @ClassName Tank
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/7/2
 * @Version V1.0
 **/
public class Tank {
    Driver driver;

    public void setDriver(Driver driver) {
        this.driver = driver;
    }
}

```

```
package com.mashibing;

/**
 * @ClassName Driver
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/7/2
 * @Version V1.0
 **/
public class Driver {
    public Driver() {
        System.out.println("driver");
    }
}

```


```
package com.mashibing;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @ClassName Main
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/7/2
 * @Version V1.0
 **/
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("app.xml");

        //spring的IOC（Inverse of control）控制反转
        //bean的创建由spring 来控制
//        Driver d = (Driver) context.getBean("d");

        //DI(Dependency injection)依赖注入
        //Tank类中的driver属性 动态注入
        Tank t = (Tank) context.getBean("tank");
    }
}

```
