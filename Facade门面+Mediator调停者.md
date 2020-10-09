[TOC]

# 门面+调停者
    我们现在的坦克游戏有中坦克、子弹、爆炸，以后还有会有地雷、墙、碉堡；
    坦克可以撞坦克，可以撞墙、可以撞碉堡
    他们之间的关系很复杂；
    如果以后新加一个物体进来，会使关系更复杂，这样代码改起来会非常麻烦
    
    这时候怎么办呢？
    
    增加一个大管家，把这些关系封装起来，TankFrame直接跟大管家打交道
    
    这就是门面模式
    第一种情况：对外的（门面模式）
        我有一些特别复杂的关系，别人用起来特别麻烦，然后我把他们封装起来，封装到一个类里，
        由这个类来管理；由这个类对外提供统一的接口
        
![Facade](A6D682967F3542C083AA8238AF595BC5)  

    第二种情况：对内的(调停者模式)
        内部之间打交道也很复杂，添加一个新功能进来时，又需要跟其他内容打交道，太麻烦了，尤其是一些老系统，系统跟系统之间打交道，那么怎么办呢？
        我们可以抽出一个新的部门来，大家都跟这个新的部门打交道，互相不再打交道了
        类似居委会大妈，调停者
        
![Mediator](0D02BE24EDD54D30B79916ABB062EE82)

==这个有著名的应用：消息中间件，消息中间件就是一个Mediator==
    
==秒杀系统，消息中间件跟订单系统、库存系统、支付系统打交道==
    
# 在Tank中应用（TankFrame-解决添加新游戏物体的问题）

    我们现在有主战坦克、敌人坦克、子弹、爆炸，将来还会有墙、碉堡、飞机其他各种各样的物体
    TankFrame跟他们打交道是非常费劲的
    例如碰撞检测，添加新物体需要做碰撞检测，以后会越来越麻烦
    
    TankFrame-解决添加新游戏物体的问题
    1：TankFrame - Facade 
        Frame - > 只做展示
        GameModel -> 内部逻辑计算
    2：GameObject 游戏物体的父类
 
## Facade的实现

==GameModel既是门面，也是调停者==
    
    GameModel对外是门面，对外例如：TankFrame
    GameModel对内是调停者，对内例如：游戏物体间的碰撞检测


    抽象出GameModel,将model和view分离；
    同时GameModel作为Facade(门面模式)负责和Frame打交道
    同时负责内部事务
    
    1.创建GameModel（Model）
        将TankFrame中有关模型的代码移植过来
        将Main类，初始化tank的代码移植过来
        
    2.修改Tank类/Bullet类/Explode类，构造方法的TankFrame改为GameModel
        
    3.TankFrame中的关于模型的代码去掉，只跟GameModel打交道（只做View）
        TankFrame不再跟显示打交道
        
        model-view的分离
        
## Mediator的实现

    1.抽象游戏物体的父类GameObject(abstract)
        名词用抽象类，形容词用接口
        
        位置：x，y
        画Paint(abstract)
            
        
    2.Tank、Bullet、Explode  继承GameObject
    
    3.GameModel
        新建List<GameObject> objects，以后游戏物体往objects中放
        去掉bullets、tanks、explodes
        新增add（GameObject）、remove（GameObject）
        
        
    4.Tank、Bullet、Explode类中使用GameModel的add(GameObject)、remove(GameObject)
    
    5.坦克和子弹的碰撞
        考虑到添加新的游戏物体，都需要做碰撞检测，这时候需要修改代码
        而我不想修改代码，所以新建一个接口类Collider
    
        借鉴Comparator.compare(o1,o2)【策略模式】
        新建一个接口Collider碰撞器，collide()
        
        新建BulletTankCollider
            判断是否为Bullet和Tank，如果是进行碰撞检测
            将Bullet中碰撞检测的代码移植过来
        
        新建TankTankCollider
            如果两个坦克碰撞，在坦克碰撞之前，让坦克回到它们原来的位置，不让坦克可以互相碾压
            
            *这个碰撞没有考虑主战坦克的碰撞情况
        
        GameObject将所有的objects都进行碰撞
            碰撞时使用BulletTankCollider和TankTankCollider
        
        
## 动态添加新的Collier【责任链模式】

    chainOfResponsibility责任链模式

    如果再添加新的Coller，还需要在GameObject中new出来Collier，需要修改代码
    
    现在我想做动态添加，而不修改代码（对修改关闭，对扩展开放原则）

    现在有多个Collider，所有的物体都要经过这些Collier
    把这些Collier串到一起就是一个责任链
    
    1.新建CollierChain，实现Collier
        实现Collier的好处是，如果有两个链条，链条可以组合
    
    2.GameObject中增加属性CollierChain
    
    3.如果先进行了子弹坦克碰撞，其实不需要做该坦克跟其他坦克的碰撞检测
        增加返回类型boolean
        如果返回true，继续
        返回false，停止检测
        
        *返回的true、false不是很明白
        
    4.将colliders 写到配置文件
        以后新增Collider,不再需要修改代码
    
    
用到了LinkedList,学习“数据结构和算法”

    1.如果使用了ArrayList,数组长度的动态扩展，会导致数组的一部分使用不到
    2.最主要的，我不需要用下标值访问每一个Collier，我反正是从头到尾访问整个链表
    
    