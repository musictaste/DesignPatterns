[TOC]

# 作业：command + cor

## 第一种方式：采用list

## 第一种方法：采用双向链表（没有代码）

# prototype原型模式 -> Object.clone()

==应用于对象克隆==

## java中的原型模式

==java自带prototype模式==

**一般用于一个对象的属性已经确定，需要产生很多相同对象的时候**



![prototype](3F2F63C20E9A4F8FAC73975C5C90E51A)

## V1:浅克隆

==实现原型模式需要实现标记型接口Cloneable==

> 标记型接口，接口中没有任何需要实现的方法

==一般会重写clone方法（如果只是重写clone方法，而没有实现接口，调用时报异常==）

> 必须重写clone方法，如果不重写clone方法，会调用Object类的clone方法，而Object的clone方法是protected，只能在子类中调用，并且是native方法
> 
> Object的clone方法实现：如果一个对象在内存中诞生了，把内存里的内容直接拷贝一份
> 
> **clone时，成员变量是对象引用，直接拷贝对象引用;原对象和克隆对象指向同一个引用，这样修改引用时，原对象和克隆对象的内容都会改变**
> 
> 编译不报错，运行报错：java.lang.CloneNotSupportedException

```
Object.java

@HotSpotIntrinsicCandidate
    protected native Object clone() throws CloneNotSupportedException;
```

```
package com.mashibing.dp.prototype.v1;

/**
 * 浅克隆
 */

public class Test {
    public static void main(String[] args) throws Exception {
        Person p1 = new Person();
        Person p2 = (Person)p1.clone();
        System.out.println(p2.age + " " + p2.score);
        System.out.println(p2.loc);

        System.out.println(p1.loc == p2.loc);
        p1.loc.street = "sh";
        System.out.println(p2.loc);

    }
}

运行结果：
8 100
Location{street='bj', roomNo=22}
true
Location{street='sh', roomNo=22}

class Person implements Cloneable {
    int age = 8;
    int score = 100;

    Location loc = new Location("bj", 22);
    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

class Location {
    String street;
    int roomNo;

    @Override
    public String toString() {
        return "Location{" +
                "street='" + street + '\'' +
                ", roomNo=" + roomNo +
                '}';
    }

    public Location(String street, int roomNo) {
        this.street = street;
        this.roomNo = roomNo;
    }
}

```


## V2:深克隆

**clone方法中，克隆对象，并克隆对象中的对象引用；**

==要求克隆对象中的对象属性也实现Cloneable接口==


```
package com.mashibing.dp.prototype.v2;

/**
 * 深克隆的处理
 */
public class Test {
    public static void main(String[] args) throws Exception {
        Person p1 = new Person();
        Person p2 = (Person)p1.clone();
        System.out.println(p2.age + " " + p2.score);
        System.out.println(p2.loc);

        System.out.println(p1.loc == p2.loc);
        p1.loc.street = "sh";
        System.out.println(p2.loc);
    }
}

运行结果：
8 100
Location{street='bj', roomNo=22}
false
Location{street='bj', roomNo=22}

class Person implements Cloneable {
    int age = 8;
    int score = 100;

    Location loc = new Location("bj", 22);
    @Override
    public Object clone() throws CloneNotSupportedException {
        Person p = (Person)super.clone();
        p.loc = (Location)loc.clone();
        return p;
    }
}

class Location implements Cloneable {
    String street;
    int roomNo;

    @Override
    public String toString() {
        return "Location{" +
                "street='" + street + '\'' +
                ", roomNo=" + roomNo +
                '}';
    }

    public Location(String street, int roomNo) {
        this.street = street;
        this.roomNo = roomNo;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

```


## V3:String不需要进行深克隆

==String类型无法克隆==

如果想克隆String，只能 new String("内容")，new一个对象出来

==String在常量池中，是共用的==

如果String的内容改变了，只是改变了String的对应的字符串的常量池地址


```
package com.mashibing.dp.prototype.v3;

/**
 * String需要进一步深克隆吗？
 */
public class Test {
    public static void main(String[] args) throws Exception {
        Person p1 = new Person();
        Person p2 = (Person)p1.clone();
        System.out.println(p2.age + " " + p2.score);
        System.out.println(p2.loc);

        System.out.println(p1.loc == p2.loc);
        p1.loc.street = "sh";
        System.out.println(p2.loc);

        p1.loc.street.replace("sh", "sz");
        System.out.println(p2.loc.street);
    }
}

运行结果：
8 100
Location{street='bj', roomNo=22}
false
Location{street='bj', roomNo=22}
bj


class Person implements Cloneable {
    int age = 8;
    int score = 100;

    Location loc = new Location("bj", 22);
    @Override
    public Object clone() throws CloneNotSupportedException {
        Person p = (Person)super.clone();
        p.loc = (Location)loc.clone();
        return p;
    }
}

class Location implements Cloneable {
    String street;
    int roomNo;

    @Override
    public String toString() {
        return "Location{" +
                "street='" + street + '\'' +
                ", roomNo=" + roomNo +
                '}';
    }

    public Location(String street, int roomNo) {
        this.street = street;
        this.roomNo = roomNo;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

```


## V4:StringBuild需要进行深克隆

**StringBuild需要深克隆**

**new String也需要深克隆**

new出来的string，修改String对象，克隆对象的String内容也会改变

new一个String对象，如果对象内容改变，克隆对象的String对象内容也会改变


```
package com.mashibing.dp.prototype.v4;

/**
 * String需要进一步深克隆吗？
 */
public class Test {
    public static void main(String[] args) throws Exception {
        Person p1 = new Person();
        Person p2 = (Person)p1.clone();
        System.out.println("p1.loc == p2.loc? " + (p1.loc == p2.loc));

        p1.loc.street.reverse();
        System.out.println(p2.loc.street);
    }
}

运行结果：
p1.loc == p2.loc? false
jb

class Person implements Cloneable {
    int age = 8;
    int score = 100;

    Location loc = new Location(new StringBuilder("bj"), 22);
    @Override
    public Object clone() throws CloneNotSupportedException {
        Person p = (Person)super.clone();
        p.loc = (Location)loc.clone();
        return p;
    }
}

class Location implements Cloneable {
    StringBuilder street;
    int roomNo;

    @Override
    public String toString() {
        return "Location{" +
                "street='" + street + '\'' +
                ", roomNo=" + roomNo +
                '}';
    }

    public Location(StringBuilder street, int roomNo) {
        this.street = street;
        this.roomNo = roomNo;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

```


## 难点：需要区分深克隆与浅克隆

**如果对象中还有对象属性，克隆时没有clone对象属性就是浅克隆**

**浅克隆只是克隆对象属性的引用地址；深克隆是克隆对象属性中的内容**

**深克隆需要对象属性也实现Cloneable接口**

**String不需要进行深克隆**

**new出来的String对象，StringBuilder，需要进行深克隆（不再是常量池引用，变成了对象引用）**


# memento备忘录模式 -> java.io.Serializable

记录状态，便于回滚

应用：1.记录快照（瞬时状态）；2.存盘

![memento](CE5F864550EB4CC383609F96F9CF7834)

## 给坦克加入存盘和读记录功能

Tank实现Serializable，实际把GameObject序列化

**transient关键字设置序列化时忽略**

添加save()、load()

添加键盘事件响应：按下S存盘，按下L读盘

==优化：其实可以将整个GameModel序列化，==

> 为了演示先ObjectOutInput先写myTank先读myTank，所以绕了一个小圈

# TemplateMethod模板方法（钩子函数）

其实，你一直在用

重写paint（Graphics g）

WindowListener -> 重写windowClosing()

ASM -> ClassVisitor

==凡是我们重写一个方法，系统帮我们自动调用的，就叫模板方法模式==

**父类中有方法，我们在子类中重写这些方法，父类调用这些方法时，会自动调用子类重写的这些方法，这就叫模板方法**

![TemplateMethod](94AD77AF2C5E44BD8E71EEE6DEDD529F)


```
package com.mashibing.dp.TemplateMethod;

public class Main {
    public static void main(String[] args) {
        F f = new C1();
        f.m();
    }

}

运行结果：
op1
op2

abstract class F {
    public void m() {
        op1();
        op2();
    }

    abstract void op1();
    abstract void op2();
}

class C1 extends F {

    @Override
    void op1() {
        System.out.println("op1");
    }

    @Override
    void op2() {
        System.out.println("op2");
    }
}


```


# State状态模式

## V1:


```
package com.mashibing.dp.state.v1;

/**
 * 当增加新的状态时非常不方便
 */

public class MM {
    String name;
    private enum MMState {HAPPY, SAD}
    MMState state;

    public void smile() {
        //switch case

    }

    public void cry() {
        //switch case
    }

    public void say() {
        //switch case
    }
}

```


## V2:

GOF《设计模式》举的例子：**TCPConnection中的方法不会扩展，但是TCP的状态有很多**

==State模式适用于类的方法不扩展的情况下==

![State1](DD54F52929324D21B0E99382E6E639B8)

![State2](2DDF6584C7B64F6CA3C43BF13FE71D2C)


```
package com.mashibing.dp.state.v2;

/**
 * 当增加新的状态时非常不方便
 */

public class MM {
    String name;
    MMState state = new MMHappyState();

    public void smile() {
        state.smile();
    }

    public void cry() {
        state.cry();
    }

    public void say() {
        state.say();
    }

}

```

```
package com.mashibing.dp.state.v2;

public abstract class MMState {
    abstract void smile();
    abstract void cry();
    abstract void say();
}

```

```
package com.mashibing.dp.state.v2;

public class MMHappyState extends MMState {
    @Override
    void smile() {
        System.out.println("happy smile");
    }

    @Override
    void cry() {

    }

    @Override
    void say() {

    }
}

```

```
package com.mashibing.dp.state.v2;

public class MMNervousState extends MMState {
    @Override
    void smile() {

    }

    @Override
    void cry() {

    }

    @Override
    void say() {

    }
}

```

```
package com.mashibing.dp.state.v2;

public class MMSadState extends MMState {
    @Override
    void smile() {

    }

    @Override
    void cry() {

    }

    @Override
    void say() {

    }
}

```



## 有限状态机(FSM)

==很多人讲到State模式，会随带讲一下State Machine状态机（注意跟State模式没关系）==

**写状态机，主要要完成状态的迁移move()**

线程状态迁移图

![State3](8A4A821AA22043828C5E864B703FED3E)


```
public class Action {
    String msg;
}
```

```
package com.mashibing.dp.state.thread;

public abstract class ThreadState_ {
    abstract void move(Action input);
    abstract void run();
}

```

```
package com.mashibing.dp.state.thread;

public class NewState extends ThreadState_ {
    private Thread_ t;

    public NewState(Thread_ t) {
        this.t = t;
    }

    @Override
    void move(Action input) {
        if(input.msg == "start")
        t.state = new RunningState(t);
    }

    @Override
    void run() {}
}

```

```
package com.mashibing.dp.state.thread;

public class RunningState extends ThreadState_ {
    private Thread_ t;

    public RunningState(Thread_ t) {
        this.t = t;
    }

    @Override
    void move(Action input) { }

    @Override
    void run() {}
}

```

```
package com.mashibing.dp.state.thread;

public class TerminatedState extends ThreadState_ {
    private Thread_ t;

    public TerminatedState(Thread_ t) {
        this.t = t;
    }

    @Override
    void move(Action input) {}

    @Override
    void run() {}
}

```

```
package com.mashibing.dp.state.thread;

public class Thread_ {
    ThreadState_ state;

    void move(Action input) {
        state.move(input);
    }

    void run() {
        state.run();
    }
}

```


## 作业：Car的State与Action，用State模式

![tank14_zuoye](FC36F6813CA944EBBC5D396AAC95C7C3)

类Car:四个方法openTheDoor()   closeTheDoor()  runTheCar()  stopTheCar()

属性（记录状态）：open  closed  running  stopped
    
抽象类CarState:

实现类（实现CarState） OpenCarState、ClosedCarState、RunningCarState、StoppedCarState


# Interpreter解释器

**动态脚本解析**

==用的太少，写起来又太麻烦，所以略过==




# 设计的指导思想

1.可维护性Maintainability
    
    修改功能，需要改动的地方越少，可维护性就越好

2.可复用性Reusability

    代码可以被以后重复使用
    写出自己总结的类库

3.可扩展性Extensibility/Scalability

    添加功能无需修改原来代码

4.灵活性flexibility/mobility/adptability

    代码接口可以灵活调用

# 面向对象六大原则

1.single  Responsibility  Principle 单一职责原则

    一个类别太大，别太累，负责单一的职责
         Person、PersonManager
    高内聚、低耦合

2.Open-Closed Principle开闭原则

    对扩展开放，对修改关闭
        尽量不修改原来代码的情况下进行扩展
    抽象化，多态是开闭原则的关键
    
3.Liscov Substitution Principle 里式替换原则

    所有使用父类的地方，必须能够透明的使用子类对象
    
4.Dependency Inversion Priciple依赖倒置原则

    依赖抽象，而是依赖具体
    面向抽象编程（面向接口编程）
    
5.Interface Segregation Principle接口隔离原则

    每一个接口应该承担独立的角色，不干不该自己干的事儿
        Flyable Runnable 不该合二为一
        避免子类实现不需要实现的方法
        需要对客户提供接口的时候，只需要暴露最小的接口（也就是高内聚低耦合）

6.Law of Demeter迪米特法则

    尽量不要和陌生人说话
    在迪米特法则中，对于一个对象，非陌生人包括以下几类：
        当前对象本身this
        以参数形式传入到当前对象方法中的对象
        当前对象的成员对象
        如果当前对象的成员对象是一个集合，那么集合中的元素也都是朋友
        当前对象所创建的对象
    和其他类的耦合度变低
        
    
总结：

OCP：总纲，对扩展开放，对修改关闭

SRP：类的职责要单一

LSP：子类可以透明替换父类

DIP：面向接口编程

ISP：接口的职责要单一

LOD：降低耦合