 
#### 定义
    
    任何可以产生对象的方法或类，都可以称之为工厂
    单例也是一种工厂，也叫静态工厂
    为什么有了new之后，还要有工厂？
        -灵活控制生产过程
        -权限、修饰、日志。。。
    
    简单工厂
    静态工厂
        
    工厂方法
    抽象工厂
        
#### 举例
    
    任意定制交通工具
        继承MoveAble
    任意定制生产过程
        Moveable XXXFactory.create();
    任意定制产品一族
     
#### 简单方法

```
/**
 * 简单工厂的可扩展性不好
 */
public class SimpleVehicleFactory {
    public Car createCar() {
        //before processing
        return new Car();
    }

    public Broom createBroom() {
        return new Broom();
    }
}
```

#### 工厂方法 FactoryMethod

==产品纬度进行扩展==

![image](https://raw.githubusercontent.com/musictaste/DesignPatterns/master/image/004.png)    


> public ==interface== Moveable {
>     void run();
> }

> public ==abstract class== Factory {
>     abstract Moveable create();
> }


#### 抽象工厂 AbstractFactory

形容词用接口，名称用抽象类

==产品一族进行扩展==
    
    作业：用抽象工厂，完成一键风格替换
    类似：皮肤的切换
![image](https://raw.githubusercontent.com/musictaste/DesignPatterns/master/image/003.png)


```
public ==abstract class== AbstractFactory {
    abstract Food createFood();
    abstract Vehicle createVehicle();
    abstract Weapon createWeapon();
}

public== abstract class== Food {
    abstract void eat();
}
```


   
