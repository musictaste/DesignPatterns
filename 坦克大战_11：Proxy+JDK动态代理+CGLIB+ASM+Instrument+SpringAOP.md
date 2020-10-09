[TOC]

# 代理模式

### V1:问题：我想记录坦克的移动时间


```
package com.mashibing.dp.proxy.v01;

import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

interface Movable {
    void move();
}
```


### V2:最简单的办法：修改代码，记录时间

问题：我想记录坦克的移动时间

最简单的办法：修改代码，记录时间

问题2：如果无法改变方法源码呢？ 例如benchmark性能测试

```
package com.mashibing.dp.proxy.v02;

import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 * 最简单的办法：修改代码，记录时间
 * 问题2：如果无法改变方法源码呢？ benchmark
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        long start = System.currentTimeMillis();

        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        long end = System.currentTimeMillis();
        System.out.println(end - start);
    }

    public static void main(String[] args) {
        new Tank().move();
    }
}

interface Movable {
    void move();
}
```


### V4：继承

用继承

==设计原则：慎用继承，因为耦合度高==


```
package com.mashibing.dp.proxy.v04;

import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 * 最简单的办法：修改代码，记录时间
 * 问题2：如果无法改变方法源码呢？
 * 用继承？
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        new Tank2().move();
    }
}

class Tank2 extends Tank {
    @Override
    public void move() {
        long start = System.currentTimeMillis();
        super.move();
        long end = System.currentTimeMillis();
        System.out.println(end - start);
    }
}

interface Movable {
    void move();
}
```


### V5：使用代理（使用聚合）

使用聚合，聚合了Tank


```
package com.mashibing.dp.proxy.v05;

import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 * 最简单的办法：修改代码，记录时间
 * 问题2：如果无法改变方法源码呢？
 * 用继承？
 * v05:使用代理
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        new TankTimeProxy(new Tank()).move();
    }
}

class TankTimeProxy implements Movable {

    Tank tank;

    public TankTimeProxy(Tank tank) {
        this.tank = tank;
    }

    @Override
    public void move() {
        long start = System.currentTimeMillis();
        tank.move();
        long end = System.currentTimeMillis();
        System.out.println(end - start);
    }
}

interface Movable {
    void move();
}
```


### V6：代理有各种类型，问题：如何实现代理的各种组合？继承？Decorator装饰？


```
package com.mashibing.dp.proxy.v06;

import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 * 最简单的办法：修改代码，记录时间
 * 问题2：如果无法改变方法源码呢？
 * 用继承？
 * v05:使用代理
 * v06:代理有各种类型
 * 问题：如何实现代理的各种组合？继承？Decorator?
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        new TankTimeProxy().move();
    }
}

class TankTimeProxy implements Movable {
    Tank tank;
    @Override
    public void move() {
        long start = System.currentTimeMillis();
        tank.move();
        long end = System.currentTimeMillis();
        System.out.println(end - start);
    }
}

class TankLogProxy implements Movable {
    Tank tank;
    @Override
    public void move() {
        System.out.println("start moving...");
        tank.move();
        System.out.println("stopped!");
    }
}

interface Movable {
    void move();
}
```


### V7:静态代理

![proxy1](B0C3346E9A6542AD82577E88E2025129)

==代理的对象改成movable类型，越来越像Decorator装饰模式了==

==各种各样的设计模式，其实只是语义上有区别==

代理进行嵌套
TankLogProxy、TankTimeProxy

==ClassLoader为什么要采用双亲委派的机制？为了安全，附带效果是节省内容==


```
package com.mashibing.dp.proxy.v07;


import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 * 最简单的办法：修改代码，记录时间
 * 问题2：如果无法改变方法源码呢？
 * 用继承？
 * v05:使用代理
 * v06:代理有各种类型
 * 问题：如何实现代理的各种组合？继承？Decorator?
 * v07:代理的对象改成Movable类型-越来越像decorator了
 *
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {

        Tank t = new Tank();
        TankTimeProxy ttp = new TankTimeProxy(t);
        TankLogProxy tlp = new TankLogProxy(ttp);
        tlp.move();

//        new TankLogProxy(
//                new TankTimeProxy(
//                        new Tank()
//                )
//        ).move();
    }
}

class TankTimeProxy implements Movable {
    Movable m;

    public TankTimeProxy(Movable m) {
        this.m = m;
    }

    @Override
    public void move() {
        long start = System.currentTimeMillis();
        m.move();
        long end = System.currentTimeMillis();
        System.out.println(end - start);
    }
}

class TankLogProxy implements Movable {
    Movable m;

    public TankLogProxy(Movable m) {
        this.m = m;
    }

    @Override
    public void move() {
        System.out.println("start moving...");
        m.move();
        long end = System.currentTimeMillis();
        System.out.println("stopped!");
    }
}

interface Movable {
    void move();
}
```


### ==V8:动态代理==

如果想让LogProxy可以重用，不仅可以代理Tank，还可以代理任何其他可以代理的Object

毕竟日志记录，时间计算是很多方法都需要的东西，这时改怎么做呢？

分离代理行为与被代理对象

使用JDK的动态代理

使用java的反射，java.lang.reflect.Proxy

类load到内存中，可以看到各种属性和方法

反射呢，我不需要类的源码，只通过二进制字节码就可以知道类的属性和方法

==reflection（反射） 通过二进制字节码分析类的属性和方法==

```
Movable m = (Movable)Proxy.newProxyInstance(Tank.class.getClassLoader(),
        new Class[]{Movable.class}, //tank.class.getInterfaces()
        new LogHander(tank)
);
```

```
package com.mashibing.dp.proxy.v08;


import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 * 最简单的办法：修改代码，记录时间
 * 问题2：如果无法改变方法源码呢？
 * 用继承？
 * v05:使用代理
 * v06:代理有各种类型
 * 问题：如何实现代理的各种组合？继承？Decorator?
 * v07:代理的对象改成Movable类型-越来越像decorator了
 * v08:如果有stop方法需要代理...
 * 如果想让LogProxy可以重用，不仅可以代理Tank，还可以代理任何其他可以代理的类型 Object
 * （毕竟日志记录，时间计算是很多方法都需要的东西），这时该怎么做呢？
 * 分离代理行为与被代理对象
 * 使用jdk的动态代理
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        Tank tank = new Tank();

        //reflection 通过二进制字节码分析类的属性和方法
        //Proxy.newProxyInstance创建代理对象
        //newProxyInstance(ClassLoader loader, Class<?>[] interfaces,InvocationHandler h)
        //参数ClassLoader：你要用哪个ClassLoader把你new的代理对象load到内存
        //参数interfaces：你这个代理对象要实现哪些接口，由于一个类可以实现多个接口，所以是一个Class数组
        //参数InvocationHandler：调用+处理器，指被代理对象被调用时我们做什么处理
        Movable m = (Movable)Proxy.newProxyInstance(Tank.class.getClassLoader(),
                new Class[]{Movable.class}, //tank.class.getInterfaces()
                new LogHander(tank)
        );

        m.move();
    }
}

class LogHander implements InvocationHandler {

    Tank tank;

    public LogHander(Tank tank) {
        this.tank = tank;
    }
    //getClass.getMethods[]
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("method " + method.getName() + " start..");
        Object o = method.invoke(tank, args);
        System.out.println("method " + method.getName() + " end!");
        return o;
    }
}



interface Movable {
    void move();
}
```

### v09: 横切代码与业务逻辑代码分离 AOP


```
package com.mashibing.dp.proxy.v09;



import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 * 最简单的办法：修改代码，记录时间
 * 问题2：如果无法改变方法源码呢？
 * 用继承？
 * v05:使用代理
 * v06:代理有各种类型
 * 问题：如何实现代理的各种组合？继承？Decorator?
 * v07:代理的对象改成Movable类型-越来越像decorator了
 * v08:如果有stop方法需要代理...
 * 如果想让LogProxy可以重用，不仅可以代理Tank，还可以代理任何其他可以代理的类型
 * （毕竟日志记录，时间计算是很多方法都需要的东西），这时该怎么做呢？
 * 分离代理行为与被代理对象
 * 使用jdk的动态代理
 *
 * v09: 横切代码与业务逻辑代码分离 AOP
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        Tank tank = new Tank();

        Movable m = (Movable)Proxy.newProxyInstance(Tank.class.getClassLoader(),
                new Class[]{Movable.class}, //tank.class.getInterfaces()
                new TimeProxy(tank)
        );

        m.move();

    }
}

class TimeProxy implements InvocationHandler {
    Movable m;

    public TimeProxy(Movable m) {
        this.m = m;
    }

    public void before() {
        System.out.println("method start..");
    }

    public void after() {
        System.out.println("method stop..");
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        before();
        Object o = method.invoke(m, args);
        after();
        return o;
    }

}

interface Movable {
    void move();
}
```



### V10: JDK动态代理源码分析


```
package com.mashibing.dp.proxy.v10;



import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;
import java.util.Random;

/**
 * 问题：我想记录坦克的移动时间
 * 最简单的办法：修改代码，记录时间
 * 问题2：如果无法改变方法源码呢？
 * 用继承？
 * v05:使用代理
 * v06:代理有各种类型
 * 问题：如何实现代理的各种组合？继承？Decorator?
 * v07:代理的对象改成Movable类型-越来越像decorator了
 * v08:如果有stop方法需要代理...
 * 如果想让LogProxy可以重用，不仅可以代理Tank，还可以代理任何其他可以代理的类型
 * （毕竟日志记录，时间计算是很多方法都需要的东西），这时该怎么做呢？
 * 分离代理行为与被代理对象
 * 使用jdk的动态代理
 *
 * v09: 横切代码与业务逻辑代码分离 AOP
 * v10: 通过反射观察生成的代理对象
 * jdk反射生成代理必须面向接口，这是由Proxy的内部实现决定的
 */
public class Tank implements Movable {

    /**
     * 模拟坦克移动了一段儿时间
     */
    @Override
    public void move() {
        System.out.println("Tank moving claclacla...");
        try {
            Thread.sleep(new Random().nextInt(10000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        Tank tank = new Tank();

        //ProxyGenerator 代理产生器
        //saveGeneratedFiles 产生的代理文件保存下来，会在IDEA生成新的文件目录：com.mashibing.dp.proxy.v10下，生成文件$Proxy0.class
        //打开文件（IDEA帮我们发编译过来了）
        //如果不确定，可以在IDEA双击shift，输入ProxyGenerator，发现只有java.lang.reflect中存在该类
        System.getProperties().put("jdk.proxy.ProxyGenerator.saveGeneratedFiles","true");//JDK11,13
//        System.getProperties().put("sun.misc.ProxyGenerator.saveGeneratedFiles","true");//JDK1.8

        Movable m = (Movable)Proxy.newProxyInstance(Tank.class.getClassLoader(),
                new Class[]{Movable.class}, //tank.class.getInterfaces()
                new TimeProxy(tank)
        );

        m.move();

    }
}

class TimeProxy implements InvocationHandler {
    Movable m;

    public TimeProxy(Movable m) {
        this.m = m;
    }

    public void before() {
        System.out.println("method start..");
    }

    public void after() {
        System.out.println("method stop..");
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //Arrays.stream(proxy.getClass().getMethods()).map(Method::getName).forEach(System.out::println);

        before();
        Object o = method.invoke(m, args);
        after();
        return o;
    }

}

interface Movable {
    void move();
}
```





```
$Proxy0.class文件分析

//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package com.mashibing.dp.proxy.v10;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;

//实现Moveable接口，继承Proxy类
final class $Proxy0 extends Proxy implements Movable {
    private static Method m1;
    private static Method m3;
    private static Method m2;
    private static Method m0;

    //需要传入InvocationHandler，本类传入的应该是TimeProxy
    public $Proxy0(InvocationHandler var1) throws  {
        //调用Proxy类的构造方法，指定TimeProxy为h
        //protected Proxy(InvocationHandler h) {
        //  Objects.requireNonNull(h);
        //  this.h = h;
        //}
       
        super(var1);
    }

    public final boolean equals(Object var1) throws  {
        try {
            return (Boolean)super.h.invoke(this, m1, new Object[]{var1});
        } catch (RuntimeException | Error var3) {
            throw var3;
        } catch (Throwable var4) {
            throw new UndeclaredThrowableException(var4);
        }
    }

    //生成的move方法
    public final void move() throws  {
        try {
            //点击h，发现在Proxy类中有属性：protected InvocationHandler h;
            //super.h.invoke就是调用TimeProxy的invoke()
            super.h.invoke(this, m3, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final String toString() throws  {
        try {
            return (String)super.h.invoke(this, m2, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final int hashCode() throws  {
        try {
            return (Integer)super.h.invoke(this, m0, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    static {
        try {
            m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
            m3 = Class.forName("com.mashibing.dp.proxy.v10.Movable").getMethod("move");
            m2 = Class.forName("java.lang.Object").getMethod("toString");
            m0 = Class.forName("java.lang.Object").getMethod("hashCode");
        } catch (NoSuchMethodException var2) {
            throw new NoSuchMethodError(var2.getMessage());
        } catch (ClassNotFoundException var3) {
            throw new NoClassDefFoundError(var3.getMessage());
        }
    }
}

```
==代理的过程大厂面试必问，需要用语言描述出来==

==如果想弄懂JDK的动态代理，一定要读JDK生成的代理类的源码==

**生成了一个动态代理，可以通过设置System.getProperties().put("sun.misc.ProxyGenerator.saveGeneratedFiles","true")将代理类文件保存下来**

分析文件你会发现，会有目标类的方法move()

你代码中调用了m.move(),那么代理类一定会调用invoke方法，那么invoke方法是谁的方法呢，是由Proxy.newProxyInstance的第三个参数指定的，那么本例中就是TimeProxy

TimeProxy的invoke方法分析

proxy.getClass.getMethods[]得到类的所有方法的数组

调用invoke方法时，将要调用的方法Method传入

```
    //proxy.getClass.getMethods[]
    //参数Proxy，不同的方法使用不同的代理，这个proxy不是TimeProxy，是生成的代理对象$Proxy0，是生成的m( Movable m = (Movable)Proxy.newProxyInstance)
    //参数Method,需要代理的方法
    //参数args，调用方法method时要用的参数
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("method " + method.getName() + " start..");
        //被代理的对象的方法在这里被调用，这里就是执行tank.move()
        //这种在JavaScript中也经常使用
        Object o = method.invoke(tank, args);
        System.out.println("method " + method.getName() + " end!");
        
        //Tank类的move方法是void，没有返回值；这里返回o，这个o是null值
        return o;
    }
```


### JDK实现动态代理(大厂面试必问)

![proxy2](9A83951EEB6A4981ACEACB400EE10C7F)

这张图解释JDK动态代理的执行过程，包含了代理类的生成过程和调用过程

客户端的main方法，调用了Proxy类的newProxyInstance(),在Proxy类里面它通过一系列的方式，生成了Proxy$0类，然后new了一个它的对象m

调用newProxyInstance()时，把InvocationHandler作为第三个参数传进去，

接下来，生成的对象m，调用了move()；实际调用的是Proxy$0的move方法

在Proxy$0的move方法中，调用了h.invoke方法，h是谁呢，h是InvocationHandler，也就是调用了TimeProxy类的invoke方法

在TimeProxy类的invoke方法中，执行method.invoke(m)前有before() 和后有after()

那么method.invoke调的是谁的方法呢，是m的，m是谁呢，是Tank（在new newProxyInstance的第三个参数中传入tank）

也就是执行了tank.move()

==注意：jdk的反射生成代理必须面向接口，这是由Proxy类的内部实现决定的，这是JDK动态代理的缺陷==

接下来解释一下ASM过程

查看Proxy.newProxyInstance的源码（JDK13、11的源码）


```
@CallerSensitive
    public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h) {
         //非空检查
        Objects.requireNonNull(h);
        //安全性检查
        final Class<?> caller = System.getSecurityManager() == null
                                    ? null
                                    : Reflection.getCallerClass();

        /*
         * Look up or generate the designated proxy class and its constructor.
         *重要的是这个方法，查找或生成指定的代理类
         */
        Constructor<?> cons = getProxyConstructor(caller, loader, interfaces);

        return newProxyInstance(caller, cons, h);
    }

```


```
private static Constructor<?> getProxyConstructor(Class<?> caller,
                                                      ClassLoader loader,
                                                      Class<?>... interfaces)
    {
        // optimization for single interface
        //判断了接口的数量，如果是1，做了一些优化的操作
        if (interfaces.length == 1) {
            Class<?> intf = interfaces[0];
            if (caller != null) {
                checkProxyAccess(caller, loader, intf);
            }
            //proxyCache缓存池，如果代理已经生成过了，去缓存池直接拿；如果没有，则生成一个
            //生成就是调用了ProxyBuilder的build方法
            return proxyCache.sub(intf).computeIfAbsent(
                loader,
                (ld, clv) -> new ProxyBuilder(ld, clv.key()).build()
            );
        } else {
            // interfaces cloned
            final Class<?>[] intfsArray = interfaces.clone();
            if (caller != null) {
                checkProxyAccess(caller, loader, intfsArray);
            }
            final List<Class<?>> intfs = Arrays.asList(intfsArray);
            return proxyCache.sub(intfs).computeIfAbsent(
                loader,
                (ld, clv) -> new ProxyBuilder(ld, clv.key()).build()
            );
        }
    }
```


```
Constructor<?> build() {
            Class<?> proxyClass = defineProxyClass(module, interfaces);
            final Constructor<?> cons;
            try {
                cons = proxyClass.getConstructor(constructorParams);
            } catch (NoSuchMethodException e) {
                throw new InternalError(e.toString(), e);
            }
            AccessController.doPrivileged(new PrivilegedAction<Void>() {
                public Void run() {
                    cons.setAccessible(true);
                    return null;
                }
            });
            return cons;
        }
```


```
private static Class<?> defineProxyClass(Module m, List<Class<?>> interfaces) {
            String proxyPkg = null;     // package to define proxy class in
            int accessFlags = Modifier.PUBLIC | Modifier.FINAL;


            //生成接口的过程
            /*
             * Record the package of a non-public proxy interface so that the
             * proxy class will be defined in the same package.  Verify that
             * all non-public proxy interfaces are in the same package.
             */
            for (Class<?> intf : interfaces) {
                int flags = intf.getModifiers();
                if (!Modifier.isPublic(flags)) {
                    accessFlags = Modifier.FINAL;  // non-public, final
                    String pkg = intf.getPackageName();
                    if (proxyPkg == null) {
                        proxyPkg = pkg;
                    } else if (!pkg.equals(proxyPkg)) {
                        throw new IllegalArgumentException(
                                "non-public interfaces from different packages");
                    }
                }
            }

            //生成包的过程
            if (proxyPkg == null) {
                // all proxy interfaces are public
                proxyPkg = m.isNamed() ? PROXY_PACKAGE_PREFIX + "." + m.getName()
                                       : PROXY_PACKAGE_PREFIX;
            } else if (proxyPkg.isEmpty() && m.isNamed()) {
                throw new IllegalArgumentException(
                        "Unnamed package cannot be added to " + m);
            }

            if (m.isNamed()) {
                if (!m.getDescriptor().packages().contains(proxyPkg)) {
                    throw new InternalError(proxyPkg + " not exist in " + m.getName());
                }
            }

            /*
             * Choose a name for the proxy class to generate.
             */
            long num = nextUniqueNumber.getAndIncrement();
            String proxyName = proxyPkg.isEmpty()
                                    ? proxyClassNamePrefix + num
                                    : proxyPkg + "." + proxyClassNamePrefix + num;

            ClassLoader loader = getLoader(m);
            trace(proxyName, m, loader, interfaces);

            /*
             * Generate the specified proxy class.真正生成代理类的过程
             * 大家还记得ProxyGenerator.saveGeneratedFile，设为true吗
             */
            byte[] proxyClassFile = ProxyGenerator.generateProxyClass(
                    proxyName, interfaces.toArray(EMPTY_CLASS_ARRAY), accessFlags);
            try {
                Class<?> pc = JLA.defineClass(loader, proxyName, proxyClassFile,
                                              null, "__dynamic_proxy__");
                reverseProxyCache.sub(pc).putIfAbsent(loader, Boolean.TRUE);
                return pc;
            } catch (ClassFormatError e) {
                /*
                 * A ClassFormatError here means that (barring bugs in the
                 * proxy class generation code) there was some other
                 * invalid aspect of the arguments supplied to the proxy
                 * class creation (such as virtual machine limitations
                 * exceeded).
                 */
                throw new IllegalArgumentException(e.toString());
            }
        }
```

```
generateProxyClass直接操作字节码

/**
     * Generate a proxy class given a name and a list of proxy interfaces.
     *
     * @param name        the class name of the proxy class
     * @param interfaces  proxy interfaces
     * @param accessFlags access flags of the proxy class
    */
    static byte[] generateProxyClass(final String name,
                                     Class<?>[] interfaces,
                                     int accessFlags)
    {
        ProxyGenerator gen = new ProxyGenerator(name, interfaces, accessFlags);
        final byte[] classFile = gen.generateClassFile();

        if (saveGeneratedFiles) {
            java.security.AccessController.doPrivileged(
            new java.security.PrivilegedAction<Void>() {
                public Void run() {
                    try {
                        int i = name.lastIndexOf('.');
                        Path path;
                        if (i > 0) {
                            Path dir = Path.of(name.substring(0, i).replace('.', File.separatorChar));
                            Files.createDirectories(dir);
                            path = dir.resolve(name.substring(i+1, name.length()) + ".class");
                        } else {
                            path = Path.of(name + ".class");
                        }
                        Files.write(path, classFile);
                        return null;
                    } catch (IOException e) {
                        throw new InternalError(
                            "I/O exception saving generated file: " + e);
                    }
                }
            });
        }

        return classFile;
    }
```


```
/**
     * Generate a class file for the proxy class.  This method drives the
     * class file generation process.
     */
    private byte[] generateClassFile() {

        /* ============================================================
         * Step 1: Assemble ProxyMethod objects for all methods to
         * generate proxy dispatching code for.
         */

        /*
         * Record that proxy methods are needed for the hashCode, equals,
         * and toString methods of java.lang.Object.  This is done before
         * the methods from the proxy interfaces so that the methods from
         * java.lang.Object take precedence over duplicate methods in the
         * proxy interfaces.
         */
         
         //正在往里面加方法
        addProxyMethod(hashCodeMethod, Object.class);
        addProxyMethod(equalsMethod, Object.class);
        addProxyMethod(toStringMethod, Object.class);

        /*
         * Now record all of the methods from the proxy interfaces, giving
         * earlier interfaces precedence over later ones with duplicate
         * methods.
         */
        for (Class<?> intf : interfaces) {
            for (Method m : intf.getMethods()) {
                if (!Modifier.isStatic(m.getModifiers())) {
                    addProxyMethod(m, intf);
                }
            }
        }

        /*
         * For each set of proxy methods with the same signature,
         * verify that the methods' return types are compatible.
         */
        for (List<ProxyMethod> sigmethods : proxyMethods.values()) {
            checkReturnTypes(sigmethods);
        }

        /* ============================================================
         * Step 2: Assemble FieldInfo and MethodInfo structs for all of
         * fields and methods in the class we are generating.
         */
         
         //在这里执行加方法，看methods.add方法
        try {
            methods.add(generateConstructor());

            for (List<ProxyMethod> sigmethods : proxyMethods.values()) {
                for (ProxyMethod pm : sigmethods) {

                    // add static field for method's Method object
                    fields.add(new FieldInfo(pm.methodFieldName,
                        "Ljava/lang/reflect/Method;",
                         ACC_PRIVATE | ACC_STATIC));

                    // generate code for proxy method and add it
                    methods.add(pm.generateMethod());
                }
            }

            methods.add(generateStaticInitializer());

        } catch (IOException e) {
            throw new InternalError("unexpected I/O Exception", e);
        }

        if (methods.size() > 65535) {
            throw new IllegalArgumentException("method limit exceeded");
        }
        if (fields.size() > 65535) {
            throw new IllegalArgumentException("field limit exceeded");
        }

        /* ============================================================
         * Step 3: Write the final class file.
         */

        /*
         * Make sure that constant pool indexes are reserved for the
         * following items before starting to write the final class file.
         */
        cp.getClass(dotToSlash(className));
        cp.getClass(superclassName);
        for (Class<?> intf: interfaces) {
            cp.getClass(dotToSlash(intf.getName()));
        }

        /*
         * Disallow new constant pool additions beyond this point, since
         * we are about to write the final constant pool table.
         */
        cp.setReadOnly();

        ByteArrayOutputStream bout = new ByteArrayOutputStream();
        DataOutputStream dout = new DataOutputStream(bout);

        try {
            /*
             * Write all the items of the "ClassFile" structure.
             * See JVMS section 4.1.
             */
                                        // u4 magic;
            dout.writeInt(0xCAFEBABE);
                                        // u2 minor_version;
            dout.writeShort(CLASSFILE_MINOR_VERSION);
                                        // u2 major_version;
            dout.writeShort(CLASSFILE_MAJOR_VERSION);

            cp.write(dout);             // (write constant pool)

                                        // u2 access_flags;
            dout.writeShort(accessFlags);
                                        // u2 this_class;
            dout.writeShort(cp.getClass(dotToSlash(className)));
                                        // u2 super_class;
            dout.writeShort(cp.getClass(superclassName));

                                        // u2 interfaces_count;
            dout.writeShort(interfaces.length);
                                        // u2 interfaces[interfaces_count];
            for (Class<?> intf : interfaces) {
                dout.writeShort(cp.getClass(
                    dotToSlash(intf.getName())));
            }

                                        // u2 fields_count;
            dout.writeShort(fields.size());
                                        // field_info fields[fields_count];
            for (FieldInfo f : fields) {
                f.write(dout);
            }

                                        // u2 methods_count;
            dout.writeShort(methods.size());
                                        // method_info methods[methods_count];
            for (MethodInfo m : methods) {
                m.write(dout);
            }

                                         // u2 attributes_count;
            dout.writeShort(0); // (no ClassFile attributes for proxy classes)

        } catch (IOException e) {
            throw new InternalError("unexpected I/O Exception", e);
        }

        return bout.toByteArray();
    }
```


```
package jdk.internal.org.objectweb.asm.tree;

public class MethodNode extends MethodVisitor{
    
}
```
### ASM

**这是发现用的是objectweb.asm，如果想把动态代理弄懂的，需要弄懂ASM**

**ASM是干嘛的呢，直接操作二进制的数据，ASM应用最广泛的就是二进制的操作类库**

**ASM非常小，大概只有50k，用起来非常方便；由于能够直接操作二进制码，不需要读懂源码，就是因为使用了ASM，java才被称之为动态语言**

大家都说JavaScript、python是动态语言，那么它动态在什么地方呢？就是在代码执行的过程中动态添加、删除方法

说到这里，**解释一下跟反射的区别；反射只能读出来，不能修改**

ASM怎么操作二进制呢，需要先了解class二进制字节码的格式

说到这就好玩了，任何一种语言如果能编译为class文件，就可以运行在JVM上，这就是scale、kotlin、grovy可以运行在jvm上的原因

所以总有说人scale要把java干掉，这不是搞笑么

**ASM实际项目中很少会用到，如果用了ASM，你会发现代码有错，你根本看不出来**

所以对ASM不需太过关心，我们了解ASM只是为了面试

### JDK生成动态代理的其他方式：Instrument（调琴弦）

除了JDK生成动态代理以外呢，还有其他方式，**比较牛叉的就是Instrument（调琴弦）**，也是java实现的，**这个方式是怎么实现的，是一个钩子函数，一个拦截器，在JDK load class到内存的过程中，拦截这个class，可以对这个class进行自己的订制，相当于你可以通过Instrument直接修改二进制码，这个功能非常强大，比ASM还要强大，可以完全控制二进制码；**

==ASM我们是通过JDK的API来用，它的API并不是所有的东西都能实现==，当然新版本出来以后，你的API没跟上，你就实现不了、

==Instrument是可以实现的， 但是这种方法做起来非常繁琐，你必须了解class文件每一个0,1代表的什么意思，所以这种方法用的并不多==

### CGLIB实现动态代理

用CGLIB生成动态代理比JDK生成的动态代理，简单很多

==CGLIB实现动态代理不需要接口==

==既然CGLIB不需要实现接口，那么代理类怎么知道实现哪些方法呢？就是因为是目标类的子类==

==如果目标类(Tank)是final的话，使用CGLIB就不能生成动态代理，这是CGLIB的缺点；==

**但是ASM不受影响；因为ASM把你的二进制文件读出来，把类中的方法和属性读出来，然后重新写一个新的class文件；如果读出来是final，看的不顺眼，直接给你删了；所以如果你的项目中真的用了ASM，你会发现你原来原来的逻辑不知不觉就变了**

ASM实际项目中很少会用到，如果用了ASM，你会发现代码有错，你根本看不出来

所以对ASM不需太过关心，我们了解ASM只是为了面试

==CGLIB的底层也是用的ASM；SPring用的也是ASM==

Tank没有实现任何方法

**如果要使用CGLIB,需要配置依赖**


```
<!-- https://mvnrepository.com/artifact/cglib/cglib -->
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.2.12</version>
</dependency>
```

### 动态代理的应用：SpringAOP-配置文件

**Spring IOC（Inverse of control控制反转）使用的是SpringbeanFactory**

**SpringBeanFactory完美了解决了工厂方法和抽象工厂的缺点**

我们好几个逻辑类，正在执行正常的逻辑代码，忽然间我想加一个功能，权限认证；我就可以用动态代理切进去，还不需要修改原来的代码，**动态代理这个功能是很强大的，我可以往里面切日志、也可以切权限，可以控制事务，这个就叫AOP(Aspect Oriented Programming 面向切面编程)**

那么往里面切什么，我们是这么做的；**写好两段要切入的代码，通过配置文件，我要对哪个类哪个方法往里切，切入的代码的顺序都在配置文件里写好，由框架帮我们自动生成intercept方法，就可以执行了，这个过程就叫织入（Weaving）**

```
<bean id="tank" class="com.mashibing.dp.spring.v1.Tank"/>
    <bean id="timeProxy" class="com.mashibing.dp.spring.v1.TimeProxy"/>

    <!---->
    <aop:config>
        <aop:aspect id="time" ref="timeProxy">
            <aop:pointcut id="onmove" expression="execution(void com.mashibing.dp.spring.v1.Tank.move())"/>
            <aop:before method="before" pointcut-ref="onmove"/>
            <aop:after method="after" pointcut-ref="onmove"/>
        </aop:aspect>
    </aop:config>
```

Tank.move()是被切入的方法

TimeProxy.before() after()是要切入的方法

**如果要切入多个方法呢：用*** ，<aop:pointcut id="onmove" expression="execution(void com.mashibing.dp.spring.v1.Tank.*)"/>

**如果要切入多个类的多个方法呢**：用*.*  ，<aop:pointcut id="onmove" expression="execution(void com.mashibing.dp.spring.v1.*.*)"/>

我们只需要写好被代理的方法和需要切入的逻辑，spring会通过配置文件帮我们配置进去，太灵活了

==Spring也是用ASM实现的==

### SpringAOP-注解

但是呢通过配置文件，配置起来很麻烦，spring也提供注解的方式

```
<aop:aspectj-autoproxy/>

<bean id="tank" class="com.mashibing.dp.spring.v2.Tank"/>
<bean id="timeProxy" class="com.mashibing.dp.spring.v2.TimeProxy"/>
```

==需要添加org.aspectj的依赖==

```
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>
```


```
package com.mashibing.dp.spring.v2;

import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

//@Aspect表示这是一个要往里切的类
@Aspect
public class TimeProxy {

    @Before("execution (void com.mashibing.dp.spring.v2.Tank.move())")
    public void before() {
        System.out.println("method start.." + System.currentTimeMillis());
    }

    @After("execution (void com.mashibing.dp.spring.v2.Tank.move())")
    public void after() {
        System.out.println("method stop.." + System.currentTimeMillis());
    }

}
```


@Aspect表示这是一个要往里切的类

@Before("execution (void com.mashibing.dp.spring.v2.Tank.move())")

@After("execution (void com.mashibing.dp.spring.v2.Tank.move())")

**使用注解很方便，但是这是要求你对你的文件组织的特别到位，你不能够把逻辑和切面混在一起，放在同一个包里，甚至不同的包里，那样你找起来会非常不方便**，所以要求做项目时按照一定规范来用


==Spring的两大核心：IOC+AOP==

==bean工程+灵活装配+动态行为拼接(织入)，成就spring在java框架中的一哥低位==

### 答疑：

问题1：ASM操作完的class文件需要重新load到内存中吗？如果这样有没有办法可以直接修改内存中的程序？

答：**可以，java的class文件是可以动态替换的，也就说java是可以实现热部署的**


