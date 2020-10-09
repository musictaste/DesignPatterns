[TOC]

### 1.应用场景
    
    只需要一个实例
    -比如各种Mgr
    -比如Spring的bean工厂，可以保证单例
    
### 2.饿汉式(最简单)


 * 饿汉式
 * 类加载到内存后，就实例化一个单例，==JVM保证线程安全==
 * **简单实用，推荐使用**！
 * 唯一缺点：不管用到与否，类装载时就完成实例化
 * Class.forName("")
 * （话说你不用的，你装载它干啥）
```
package com.mashibing.dp.singleton.mycode;

/**
 * @ClassName Mgr01
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/19
 * @Version V1.0
 **/
public class Mgr01 {
//    private static final Mgr01 INSTANCE = new Mgr01();
    private static final Mgr01 INSTANCE;
    static {
        INSTANCE = new Mgr01();
    }
    //关键在于构造方法是private的
    private Mgr01(){}

    public static Mgr01 getInstance(){
        return INSTANCE;
    }

    public static void main(String[] args) {
        Mgr01 o1 = Mgr01.getInstance();
        Mgr01 o2 = Mgr01.getInstance();
        System.out.println(o1==o2);
    }
}

```


### 3.懒汉式

 * lazy loading
 * 也称懒汉式
 * 虽然达到了按需初始化的目的，但却**带来线程不安全的问题**
 * **可以通过synchronized解决，但也带来效率下降**

```
package com.mashibing.dp.singleton.mycode;

import sun.security.jca.GetInstance;

import java.util.concurrent.TimeUnit;

/**
 * @ClassName Mgr02
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/19
 * @Version V1.0
 **/
public class Mgr02 {
    //没有final修饰，因为加了final修饰，必须进行初始化
    private static Mgr02 INSTANCE;

    public static synchronized Mgr02 getINstance(){
        if(INSTANCE ==null){
            //因为执行速度太快，加一下睡眠，增加了被其他打断的可能性
            try {
                TimeUnit.MILLISECONDS.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            INSTANCE = new Mgr02();
        }
        return INSTANCE;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                System.out.println(Mgr02.getINstance().hashCode());
            },"T"+i).start();
        }
    }
}

```

### 4.双重检查DCL(面试题)

== synchronized (Mgr03.class)==
```
package com.mashibing.dp.singleton.mycode;

import java.util.concurrent.TimeUnit;

/**
 * @ClassName Mgr03
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/19
 * @Version V1.0
 **/
public class Mgr03 {
    //必须加volatile，因为如果使用了JIT优化（优化成本地代码）会导致指令重排，volatile可以防止指令重排序
    private static volatile Mgr03 instance;

    public static Mgr03 getInstance(){
        if(instance ==null){
            //妄图通过减小同步代码块的方式来提高效率
            synchronized (Mgr03.class){
                if(instance ==null){
                    //因为执行速度太快，加一下睡眠，增加了被其他打断的可能性
                    try {
                        TimeUnit.MILLISECONDS.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    instance = new Mgr03();
                }
            }
        }
        return instance;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                System.out.println(Mgr03.getInstance().hashCode());
            },"T"+i).start();
        }
    }
}

```
    
### 5.静态内部类（完美）

 * 静态内部类方式
 * JVM保证单例
 * **加载外部类时不会加载内部类，这样可以实现懒加载**

```
package com.mashibing.dp.singleton.mycode;

/**
 * @ClassName Mgr04
 * @Description: 静态内部类
 *  可以保证单例，也能保证懒加载
 *  也算是比较完美的一种写法，除了不能防止反序列化
 *
 *  不能防止反序列化的意思是
 *  通过反射得到的对象和通过getInstance得到的对象不是同一个对象
 *
 * @Author 李淼
 * @Date 2020/3/19
 * @Version V1.0
 **/
public class Mgr04 {
    //构造方法是private的
    private Mgr04(){}

    //调用该方法时，内部类才会被加载
    public static Mgr04 getInstance(){
        return Mgr04Loader.INSTANCE;
    }

    //外部类 加载的时候，内部类不会被加载；是JVM的类加载机制保证的
    static class Mgr04Loader{
        private final static Mgr04 INSTANCE=new Mgr04();
    }

    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                System.out.println(Mgr04.getInstance().hashCode());
            },"T"+i).start();
        }
    }

}

```
    
### 6.枚举单例(完美-反序列化)

 * 不仅可以解决线程同步，还可以防止反序列化。
 * ==public enum Mgr05==
```
package com.mashibing.dp.singleton.mycode;

/**
* java创始人之一，《effective java》的作者
 * 《effective java》中的提到的单例
 * 序列化问题：反射 将class load到内存中，可以instance出一个实例；如果想反序列化非常复杂，需要设置一些内部的变量
 * 枚举类不能反序列化的问题：因为枚举类没有构造方法
 *
 *  通过反射得到的Instance和通过Enum.Instance得到的对象是同一个对象
 *
 * 所以该方法是最完美的单例方法
**/
public enum Mgr05 {
    INSTANCE;
    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                System.out.println(Mgr05.INSTANCE.hashCode());
            },"T"+i).start();
        }
    }

}

```
