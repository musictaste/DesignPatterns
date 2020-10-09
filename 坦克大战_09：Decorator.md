[TOC]

### 1.代码重构：解耦合

    工程：day09CodeRefactor
    
    1.tank.back()
        用oldX, oldY记录上一步的位置，坦克互相撞击后回到上一步
        修改TankTankCollider
    2.添加一堵墙
        class Wall extends GameObject
        
        GameModel中初始化墙
        
        定义BulletWallCollider
            子弹不能穿墙
            
        定义TankWallCollider
            敌方坦克不能穿墙
        
==体会到设计模式的好处：添加一堵墙，没有改动代码==
            
    3.bullet.CollideWithTank()的逻辑移到BulletTankCollider中
        编程原则：高内聚，低耦合
        耦合度级别从高到底：继承、聚合（A类是B类的属性）、关联（A类不是B类的属性，但是B类使用了A类）、没有关联（A、B类互相谁都不用谁）
    
    4.GameModel做成单例
    
    5.对于GameModel.add()方法的优化
        由于GameModel现在是单例的
        
        考虑消除new GameModel需要new Tank, new Tank又需要new GameModel
            解决：1.GameModel.init();
                2.静态语句块
        
### 2.详解Decorator装饰器(不是很重要)

    GOF 四人组的《设计模式》中提到一个应用：自处理软件（Ideaplus++、word、notepad）外围加边框或滚动条
    
    问题：
        坦克想加一个外壳提示
        坦克想加一个血条
        坦克想加一条尾巴
        子弹想加一条尾巴
        子弹想加一个外壳
        
    如果不熟悉设计模式的话，最简单的方法是：继承
        BloodTank、TailTank、RectTank、BloodTailTank、TailBullet
        
    采用继承，不灵活；装饰和被装饰者之间耦合度太高；也会产生类爆炸的现象（就是新增很多类）
        
    
    进一步：
        Tank
        TankDecorator
            tank
            paint -> tank.paint()+装饰
            
        该方法不是很完美，因为现在是坦克加尾巴，如果子弹也加尾巴需要再建一个BulletDecorato；因为TankDecorator中属性为tank
        
        这样把具体的子类和Decorator耦合到一起了
        
    最灵活的方式：
        Tank
        TankDecorator
            gameObject
            paint -> gameObject.paint()+装饰
    

### 3.Decorator在Tank中的应用
    
![decorator](87CDF23AC581405FB574B70C88FB2CF2)
    
    工程：day09Decorator
    
    作业：遗留bug：decorator模式之后new Bullet()加了两遍
    
    问题1：使用了RectDecorator以后，敌方坦克打出的子弹，黄色边框靠前一点的原因是：Bullet.paint以后，又move()的原因    
    
    问题2：RectDecorator中调用了TailDecorator,导致子弹没有黄色边框的效果
        原因：RectDecorator和TailDecorator，都聚合了GameObject的x/y属性
        bullet是不停move的，
        RectDecorator和TailDecorator使用GameObject时，x、y的信息还是之前的信息，
        所以在 RectDecorator和TailDecorator使用GameObject时需要读取最新的Bullet的x/y信息
    
    
```
DefaultFireStrategy
    
    ...
    //装饰者模式经常跟责任链模式组合使用，这样就可以避免下面的代码
    GameModel.getInstance().add(
        new RectDecorator(
                new TailDecorator(
                    new Bullet(bulletX,bulletY,t.getDirection(),t.getGroup())
                )
        ));
    ...
    
TailDecorator

    ...
    @Override
    public void paint(Graphics g) {
        //为了解决问题1，因为子弹是不停move，所以需要更新x,y的位置
        this.x=go.x;
        this.y=go.y;

        super.go.paint(g);
        Color color = g.getColor();
        g.setColor(Color.WHITE);
        //问题1:，当RectDecorator和TailDecorator组合时，不是没有边框了
//        g.drawLine(super.go.x,super.go.y,super.go.x+super.go.getWidth(),super.go.y+super.go.getHeight());

        g.drawLine(go.x,go.y,go.x+go.getWidth(),go.y+go.getHeight());

        g.setColor(color);
    }
    ....

RectDecorator

    ...
     @Override
    public void paint(Graphics g) {
        //子弹是不停move，所以需要更新x/y的位置
        this.x=go.x;
        this.y=go.y;

        super.go.paint(g);
        Color color = g.getColor();
        g.setColor(Color.YELLOW);
        g.drawRect(go.x,go.y,go.getWidth(),go.getHeight());
        g.setColor(color);
    }
    ....


```

==另外呢，装饰者模式经常跟责任链模式一起组合使用==



 
###  4.装饰者模式在框架中的应用

==典型的应用：java的IO流==

==实际项目中暂时没有用到装饰者模式==

==装饰者模式，实际开发使用很少==

==设计模式中优先级比较高的是：责任链模式、代理模式 -> 工厂模式、单例模式、观察者模式、策略模式==

reader and inputStream
 

writer and outputStream
    writer里面聚合了outputStream
    output.write + Decorator
    
    Writer也有很多子类：BufferWriter、PrintWriter
    
    writer中的outputStream有很多种，FileOutputStream、IOOutputStream
  
    通过源码发现OutputStream和Writer都实现了Flushable接口，从结构上是应用了装饰者模式
    
    OutputStream
    OutputStreamWriter
    BufferedWriter
    
==现在网上有很多争论，说IO流究竟是不是装饰者模式；我觉得不需要太吹毛求疵，设计模式要做到心中有剑，手中无剑，熟练使用即可==

==我认为呢，严格来说，应该是代理模式；因为Flushable不是一个有意义的对象==
==另外呢，学了桥接模式，你会发现桥接模式跟装饰者模式、代理模式也很像==    

==另外呢，23中设计模式任何一种在实际开发中都是非必要的==

==当你代码越写越变扭的时候，你就会使用设计模式，自然而然就会使用==
  
![decorator2](4B193A80428145D8AA063FF01A825676)
    
作业：补老的IO知识

### 课堂答疑：java编程思想上说构造方法是静态的，怎么理解

    java的方法都是可以继承重写的，比如说现在有一个方法m(),子类中可以重写这个m(),执行m()的时候有一个动态绑定的过程（这个在菜鸟预习的视频中讲到）这个动态绑定是多态的核心
    
    构造方法是没有动态绑定过程的，所以说是静态的
    

### 5.作业：敲代码

