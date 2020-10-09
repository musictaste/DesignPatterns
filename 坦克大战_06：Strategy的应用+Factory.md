[TOC]


### 1.策略模式在Tank中的应用
    package:day06Strategy
    
    对修改关闭，对扩展开放：Extensibility Scalability
    
    定义了接口：FireStrategy
        fire(Tank t);
        
    实现类：DefaultFireStrategy---敌方坦克
        FourDirectionFireStrategy---自己坦克
        
    
```
package com.mashibing.lianxi.day06Strategy.strategy;

import com.mashibing.lianxi.day06Strategy.Tank;

/**
 * @ClassName FireStrategy
 * @Description: 开火策略
 * @Author 李淼
 * @Date 2020/6/18
 * @Version V1.0
 **/
public interface FireStrategy {
    void fire(Tank t);
}
```

```
package com.mashibing.lianxi.day06Strategy.strategy;

import com.mashibing.lianxi.day06Strategy.*;

/**
 * @ClassName DefaultFireStrategy
 * @Description: 默认的开火策略-单例
 * @Author 李淼
 * @Date 2020/6/18
 * @Version V1.0
 **/
public class DefaultFireStrategy implements FireStrategy {
    //通过反射new对象时，会报：Tank can not access a member of class com.mashibing.lianxi.day06Strategy.strategy.DefaultFireStrategy with modifiers "private"
    //因为构造方法是private的
    private static final DefaultFireStrategy INSTANCE=new DefaultFireStrategy();

    private DefaultFireStrategy(){}

    public static DefaultFireStrategy getInstance(){
        return INSTANCE;
    }

    @Override
    public void fire(Tank t) {
        //让子弹从坦克的中心位置打出，公式=当前x + 坦克图片的一半  - 子弹图片的一半

        int bulletX = t.getX() + Tank.width/2 - Bullet.width/2;
        int bulletY = t.getY() + Tank.height/2 - Bullet.height/2;
        //为了以后的灵活，比如一次发射5个子弹，发射核弹，所以不建议return new Bullet()
        new Bullet(bulletX,bulletY,t.getDirection(),t.getGroup(),t.getTf());
        if(t.getGroup() == Group.GOOD) new Thread(()-> new Audio("audio/tank_fire.wav").play()).start();
    }
}

```

```
package com.mashibing.lianxi.day06Strategy.strategy;

import com.mashibing.lianxi.day06Strategy.*;

/**
 * @ClassName FourDirectionFireStrategy
 * @Description: 向四个方向开火--单例
 * @Author 李淼
 * @Date 2020/6/18
 * @Version V1.0
 **/
public class FourDirectionFireStrategy implements FireStrategy {
    private static final FourDirectionFireStrategy INSTANCE = new FourDirectionFireStrategy();

    private FourDirectionFireStrategy(){}

    public static FourDirectionFireStrategy getInstance(){
        return INSTANCE;
    }


    @Override
    public void fire(Tank t) {
        //让子弹从坦克的中心位置打出，公式=当前x + 坦克图片的一半  - 子弹图片的一半

        int bulletX = t.getX() + Tank.width/2 - Bullet.width/2;
        int bulletY = t.getY() + Tank.height/2 - Bullet.height/2;
        //为了以后的灵活，比如一次发射5个子弹，发射核弹，所以不建议return new Bullet()
        Direction[] directions = Direction.values();
        for (Direction dir:directions) {
            new Bullet(bulletX,bulletY, dir,t.getGroup(),t.getTf());
        }
        if(t.getGroup() == Group.GOOD) new Thread(()-> new Audio("audio/tank_fire.wav").play()).start();
    }
}

```
```
package com.mashibing.lianxi.day06Strategy;

/**
 * @ClassName Bullet
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/6/9
 * @Version V1.0
 **/
public class Bullet {
    ...
    public Bullet(int x, int y, Direction direction, Group group, TankFrame tf) {
        this.x = x;
        this.y = y;
        this.direction = direction;
        this.group=group;
        this.tf=tf;

        rect.x=this.x;
        rect.y=this.y;
        rect.width=width;
        rect.height=height;

        //拿到子弹直接加到列表中
        tf.bullets.add(this);
    }
    ...
}

```  


```
package com.mashibing.lianxi.day06Strategy;

/**
 * @ClassName Tank02
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/6/9
 * @Version V1.0
 **/
public class Tank {

    /**
     * 坦克发射子弹
     * 可以将FireStrategy 定义为成员变量，也可以作为参数传入
     *  1.将FireStrategy作为成员变量，因为成员变量的作用域是整个类；FireStrategy不需要为单例
     *  2.传参数的话，作用域为一个方法
     *      传参数带来的问题是，每次调用都需要new一个FireStrategy，所以可以将FireStrategy做成单例
     */
    public void fire(FireStrategy fireStrategy) {
        fireStrategy.fire(this);
    }

}

```

```
package com.mashibing.lianxi.day06Strategy;

/**
 * @ClassName TankFrame
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/6/9
 * @Version V1.0
 **/
public class TankFrame extends Frame {

    class MyKeyListener extends KeyAdapter {

        @Override
        public void keyReleased(KeyEvent e) {
            int keyCode = e.getKeyCode();
            switch (keyCode){
                case KeyEvent.VK_LEFT:
                    bL=false;
                    break;
                case KeyEvent.VK_DOWN:
                    bD=false;
                    break;
                case KeyEvent.VK_RIGHT:
                    bR=false;
                    break;
                case KeyEvent.VK_UP:
                    bU=false;
                    break;
                case KeyEvent.VK_CONTROL:
                    //在键盘释放的时候，发射子弹

                    //方法一：通过配置文件，因为FourDirectionFireStrategy是单例的，构造方式是private的，不支持反射
                   /* String fsName = (String)PropertyMgr.get("goodFireStrategy");
                    FireStrategy fireStrategy = null;
                    try {
                        //newInstance JDK9废弃了
                        //fireStrategy = (FireStrategy) Class.forName(fsName).newInstance();
                        fireStrategy = (FireStrategy) Class.forName(fsName).getDeclaredConstructor().newInstance();
                    } catch (InstantiationException ex) {
                        ex.printStackTrace();
                    } catch (IllegalAccessException ex) {
                        ex.printStackTrace();
                    } catch (ClassNotFoundException ex) {
                        ex.printStackTrace();
                    }
                    myTank.fire(fireStrategy);*/

                    //方法二：直接使用类名
                    myTank.fire(FourDirectionFireStrategy.getInstance());
                    break;
                default:
                    break;
            }
            setTankMainDirection();
        }

       
    }
}

```

        

### 2.工厂模式
    
    简单工厂
    静态工厂
    
    工厂方法FactoryMethod
        从产品维度扩展，新加一个产品：汽车(新增Car、CarFactory)、飞机(Flain、FlainFactory)
        
    抽象工厂
        对产品族的扩展，新加一个产品族：人(面包、汽车、AK47)、动物（肉、腿、木棍）、魔法师（蘑菇、扫帚、魔法棒）

#### 2.1 作业：用抽象工厂，完成一键风格替换

#### 2.2 预习责任链（在B站有视频）
    https://www.bilibili.com/video/av53954830/
    
    


### 3.项目增加maven模块:

    1.project - add framework support - maven
    
    2.pom.xml增加依赖
    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.6.RELEASE</version>
        </dependency>
    </dependencies>
    
    3.引入jar包
    import changes / auto import


#### 3.1.问题：maven是如何解决jar冲突的？


