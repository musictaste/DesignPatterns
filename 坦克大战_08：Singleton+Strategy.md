[TOC]

坦克从今日始，分支两个：DesignPatterns(设计模式) Network(网络版本)


### 1.单例：
        应用：1.配置文件：PropertyMgr、ResourceMgr
            2.Spring的bean工厂，可以保证单例
    
         关键在于构造方法是private的
        
        饿汉式
            类加载到内存后，就实例化一个单例，JVM保证线程安全
            简单实用，推荐使用！
            唯一缺点：不管用到与否，类装载时就完成实例化
        
        懒汉式
            虽然达到了按需初始化的目的，但却带来线程不安全的问题
            可以通过synchronized解决，但也带来效率下降
        
        DCL
            妄图通过减小同步代码块的方式来提高效率
            
            必须加volatile，因为如果使用了JIT优化（优化成本地代码）会导致指令重排，volatile可以防止指令重排序
        
        静态内部类
            JVM保证单例
            加载外部类时不会加载内部类，这样可以实现懒加载
            
            可以保证单例，也能保证懒加载
            也算是比较完美的一种写法，除了不能防止反序列化
            
            
            不能防止反序列化的意思是
            通过反射得到的对象和通过getInstance得到的对象不是同一个对象
        
        枚举单例
            java创始人之一，《effective java》的作者
            《effective java》中的提到的单例
            序列化问题：反射 将class load到内存中，可以instance出一个实例；如果想反序列化非常复杂，
            需要设置一些内部的变量
            
            枚举类不能反序列化的问题：因为枚举类没有构造方法
            通过反射得到的Instance和通过Enum.Instance得到的对象是同一个对象
            
            所以该方法是最完美的单例方法

#### 1.1 单例模式在Tank中的应用       


```
config.properties

initTankCount=6
tankSpeed=2
bulletSpeed=6
gameWidth=1080
gameHeight=960
```


```
package com.mashibing.lianxi.day05;

import java.io.IOException;
import java.util.Properties;

/**
 * @ClassName PropertyMgr
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/6/16
 * @Version V1.0
 **/
public class PropertyMgr {
    private static Properties pros = new Properties();

    static {
        try {
            pros.load(PropertyMgr.class.getClassLoader().getResourceAsStream("com/mashibing/lianxi/day05/config"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //构造方法为private时，就是一个简单实用的单例模式
    private PropertyMgr(){}

    public static Object get(String key){
        if(pros==null) return null;
        return pros.get(key);
    }

    public static void main(String[] args) {
        System.out.println(PropertyMgr.get("initTankCount"));
    }
}

```

```
package com.mashibing.lianxi.day05;

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.io.IOException;

/**
 * @ClassName ResourceMgr
 * @Description: 资源文件
 * @Author 李淼
 * @Date 2020/6/11
 * @Version V1.0
 **/
public class ResourceMgr {
    //坦克的方向图片
    private static BufferedImage goodTankL,goodTankR,goodTankU,goodTankD;
    private static BufferedImage badTankL,badTankR,badTankU,badTankD;

    //子弹的方向图片
    private static BufferedImage bulletL,bulletR,bulletU,bulletD;

    //爆炸的图片
    private static BufferedImage[] explodes=new BufferedImage[16];

    static{
        try {
            goodTankU= ImageIO.read(ResourceMgr.class.getClassLoader().getResourceAsStream("images/GoodTank1.png"));
            goodTankL= ImageUtil.rotateImage(goodTankU,-90);
            goodTankR= ImageUtil.rotateImage(goodTankU,90);
            goodTankD= ImageUtil.rotateImage(goodTankU,180);

            badTankU= ImageIO.read(ResourceMgr.class.getClassLoader().getResourceAsStream("images/BadTank1.png"));
            badTankL= ImageUtil.rotateImage(badTankU,-90);
            badTankR= ImageUtil.rotateImage(badTankU,90);
            badTankD= ImageUtil.rotateImage(badTankU,180);

            bulletU= ImageIO.read(ResourceMgr.class.getClassLoader().getResourceAsStream("images/bulletU.png"));
            bulletL= ImageUtil.rotateImage(bulletU,-90);
            bulletR= ImageUtil.rotateImage(bulletU,90);
            bulletD= ImageUtil.rotateImage(bulletU,180);

            for (int i = 0; i < 16; i++) {
                explodes[i] = ImageIO.read(ResourceMgr.class.getClassLoader().getResourceAsStream("images/e"+(i+1)+".gif"));
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //单例模式的关键就是：构造方法是private的
    private ResourceMgr(){}

    public static BufferedImage getGoodTankL(){
        return goodTankL;
    }

    public static BufferedImage getGoodTankR() {
        return goodTankR;
    }

    public static BufferedImage getGoodTankU() {
        return goodTankU;
    }

    public static BufferedImage getGoodTankD() {
        return goodTankD;
    }

    public static BufferedImage getBadTankL() {
        return badTankL;
    }

    public static BufferedImage getBadTankR() {
        return badTankR;
    }

    public static BufferedImage getBadTankU() {
        return badTankU;
    }

    public static BufferedImage getBadTankD() {
        return badTankD;
    }

    public static BufferedImage getBulletL() {
        return bulletL;
    }

    public static BufferedImage getBulletR() {
        return bulletR;
    }

    public static BufferedImage getBulletU() {
        return bulletU;
    }

    public static BufferedImage getBulletD() {
        return bulletD;
    }

    public static BufferedImage[] getExplodes() {
        return explodes;
    }
}

```


        
### 2.策略：Strategy
        
    Comparable 
        没有使用策略模式，随带讲一下
        
        int compareTo(T t);
         
    Comparator
        开闭原则：对修改关闭，对扩展开放
        
        函数式接口 @FunctionalInterface
        如果只有一个抽象方法，可以不加注解，另外可以使用lambda表达式
        
        1.8以后，可以写方法实现;是为了兼容1.8之前的版本
        default void m(){}
        
        sorter.sort(arr,(o1,o2)->{
            if(o1.weight<o2.weight) return -1;
            else if(o1.weight >o2.weight) return 1;
            else return 0;
        });
        
![002](F444251CBE5D406E8DDEF6663F05A437)

### 3.作业：

    策略模式应用到：Tank.fire
        策略1：默认策略 一颗子弹
        策略2：四个方向同时打出子弹
        策略3：打核弹
        .....
    
        fire(FireStrategy s) -> 每次调用，都需要new，因此，应该把DefaultStrategy -> Singleton
        成员变量
    
        Extensibility Scalability