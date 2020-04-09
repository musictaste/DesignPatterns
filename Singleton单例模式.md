## 应用场景
    
    只需要一个实例
    -比如各种Mgr
    -比如各种Factory
    
#### 饿汉式(最简单)


 * 饿汉式
 * 类加载到内存后，就实例化一个单例，==JVM保证线程安全==
 * **简单实用，推荐使用**！
 * 唯一缺点：不管用到与否，类装载时就完成实例化
 * Class.forName("")
 * （话说你不用的，你装载它干啥）
```
public class Mgr01 {
//    private static final Mgr01 INSTANCE = new Mgr01();
    private static final Mgr01 INSTANCE;
    static {
        INSTANCE = new Mgr01();
    }

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


#### 懒汉式

 * lazy loading
 * 也称懒汉式
 * 虽然达到了按需初始化的目的，但却**带来线程不安全的问题**
 * **可以通过synchronized解决，但也带来效率下降**

```
public class Mgr02 {
    private static Mgr02 INSTANCE;

    public static synchronized Mgr02 getINstance(){
        if(INSTANCE ==null){
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

#### 双重检查DCL(面试题)

== synchronized (Mgr03.class)==
```
/**
 * double check lock
 * 也称双重检查
 * 面试题
 */
public class Mgr03 {
    private static volatile Mgr03 instance;

    public static Mgr03 getInstance(){
        if(instance ==null){
            synchronized (Mgr03.class){
                if(instance ==null){
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
    
#### 静态内部类（完美）

 * 静态内部类方式
 * JVM保证单例
 * **加载外部类时不会加载内部类，这样可以实现懒加载**

```
public class Mgr04 {
    public static Mgr04 getInstance(){
        return Mgr04Loader.INSTANCE;
    }

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
    
#### 枚举单例(完美-反序列化)

 * 不仅可以解决线程同步，还可以防止反序列化。
 * ==public enum Mgr05==
```
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
