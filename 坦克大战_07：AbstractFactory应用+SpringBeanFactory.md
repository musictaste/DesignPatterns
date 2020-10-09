[TOC]

### 抽象工厂模式在Tank中的应用

具体的应用：使用抽象工厂模式，动态添加产品族（矩形的坦克、子弹、爆炸效果）

==请记住：在使用抽象工厂模式时，父子类，父类里抽象的东西应该尽可能的少；==

因为父类抽象的东西越多，子类需要实现的东西就越多

继承：父类对子类进行抽象时，不要一下子多很多东西的抽象，用到什么东西抽象什么东西


这次demo中使用抽象工厂模式，后续会将抽象工厂的代码移除；因为这样使代码变复杂了

因为已经使用了策略模式，所以在做抽象工厂的时候，抽象父类需要添加很多属性；所以决定先去掉策略模式的代码，即删除 开火相关的策略模式

具体的类：DefaultFireStrategy、FireStrategy、FourDirectionFireStrategy

==使用抽象工厂的好处：新增一个产品族很方便，坏处就是需要新增很多类==

==面向接口编程、面向抽象编程==

![006](16E64227408241CEA287955401D3855C)
代码：day07abstractFactory


### SpringBeanFactory
将笔记补充到“工厂模式”中

1.新建一个分支：dp_gamemodel

2.使用Gradle创建工程：HelloSpring

3.在build.gradle文件中dependencies增加依赖：
compile group:'org.springframework',name: 'spring-context',version:'5.1.6.RELEASE'

4.新建spring的application.xml

5.IOC控制反转 /DI依赖注入


### Facade门面+Mediator 调停者

    TF - 解决添加新游戏物体的问题
    1：TF - Facade 
        Frame - > 展示
        GameModel -> 内部逻辑计算
    2：GameObject


