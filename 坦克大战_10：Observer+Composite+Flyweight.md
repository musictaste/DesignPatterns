[TOC]

### 详解Observer观察者模式


==不要把前端叫美工，应该叫设计==

#### 问题：小朋友睡醒了哭，饿！
    
##### V1:披着面向对象外衣的面向过



##### V2:面向对象的傻等

    
##### V3:加入观察者

##### V4:加入多个观察者
    如果需要再加入观察者，需要修改代码，不灵活
    
    例如一只小鸡跳到墙头，小狗也会汪汪汪叫
    对于事件的反应，观察者做出的反应不一定耦合到某一个特定的被观察者身上
    所以写死肯定不好，耦合度也不好
    
##### V5:分离观察者和被观察者
    
    定义一个接口：Observer
    定义观察者（实现Observer）：Dad、Mum、Dog
    Child中有观察者的列表 List<Observer>,并对列表进行迭代
        
    
> 讲到这，是否发现观察者模式跟责任链模式有很多相像的地方
> 
> 观察者只是多态的应用，而责任链呢会经常串在一起
> 
> 当一个event事件发生时，有一连串的Observer需要响应，这时是观察者模式
> 
> 如果其中一个Observer执行以后不想继续执行了，==中断==了，就变成了责任链模式
> 
> 观察Observer和监听listener是一个意思

##### 面试题：责任链和观察者模式的区别

    他们的实现方式很像，都是
        定义一个接口：Observer
        定义观察者（实现Observer）：Dad、Mum、Dog
        Child中有观察者的列表 List<Observer>,并对列表进行迭代
    
    责任链的重点在链条上
    观察者的重点在观察上，在事件上
    
    一般两种模式都混着使用
    
==主要的区别在语义上==

##### V6:有很多时候，观察者需要根据事件的具体情况来进行处理
    
    这时候Observer设计模式才刚刚开始
    观察者模式一般都是根据event的具体情况来做具体的处理
    拿小孩来举例：根据小孩哭的厉害程度、哭的时间（中午还是晚上）、哭的地点（床上、还是地上）
    
    事件类：fire Event
        看到这，有的同学认为是一个生产者消费者模式
        是，可以认为它生产了一个事件，其他人去消费
        但是，不用非的把它归结到哪种设计模式上，要学会灵活运用
    
    接口Observer，actionOnWakeUp(event)，方法接受参数event
    
    下来看图，观察者模式通常由三个部分组成：
        Source事件源对象
        Observer观察者：观察者观察到具体的事件做相应的反应
        Event事件本身
        
![Observer](CA00601D6E8947FF99045E7F0275BD7D)

    其实坦克项目中已经用到了观察者模式
    TankFrame类中的内部类MyKeyListener就是观察者，方法keyPressed(KeyEvent)、keyReleased(KeyEvent)
    KeyEvent就是事件本身，键盘事件
    事件源对象是窗口

##### V7:大多数时候，我们处理事件的时候，需要事件源对象

> 一个观察者可以监听到多个事件源身上，也可以发出好多种不同类型的事件
> 
> 例如TankFrame中KeyListener、WindowListener
>     
> Spring的切面，弄一堆观察者在切面上，另外spring的耦合度更低（解耦的地方在于：spring将Event中的事件源Source写到配置文件中了）
>     
> ==Spring的切面，本质上是代理模式，但从语义上理解就是一个观察者模式==
    
    
> 不能将事件源作为参数直接传入Observer，因为这样做就相当于Observer跟数据源绑定在一起，不能监听别的数据源，
> 
> 那么应该怎么做呢：将Source作为Event的一个属性
> 
> ==Observer只跟Event打交道，不跟Source打交道，这样就解耦合了==
> 
> 例如WindowEvent.getSource()、KeyEvent.getSource();

##### V8.事件也可以形成集成体系

以java.awt为例：KeyEvent -> InputEvent -> ComponentEvent ->AWTEvent ->EventObject(source属性)

```
public class KeyEvent extends InputEvent

public abstract class InputEvent extends ComponentEvent

public class ComponentEvent extends AWTEvent

public abstract class AWTEvent extends EventObject

public class EventObject implements java.io.Serializable 
    protected transient Object  source;

```
   
##### V9.java中观察者模式的应用
    
事件源对象：button
监听类Observer:ActionListener
事件本身：ActionEvent

==在java，UI(awt)大部分使用都是Observer设计模式==

==并且呢在java.awt中Listener的event参数是怎么传入呢，我们代码中并没有传入啊；
是由awt框架本身传入event参数的==

再详细一点就是：JVM运行在操作系统，jvm会监听操作系统的键盘事件，windows系统产生一个键盘事件，然后发现windows产生的这个事件是一个java的窗口，那么交给JVM虚拟机，JVM帮我们生成Event对象，帮我们调用keyPressed方法，所以当我们拿到KeyPressed方法中参数KeyEvent时，KeyEvent对象已经new出来了，调用keyCode方法就可以用了




```
package com.mashibing.dp.observer.v9;

import java.awt.Button;
import java.awt.Frame;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;

public class TestFrame extends Frame {
	public void launch() {
		//按钮是事件源对象
		Button b = new Button("press me");
		//相当于添加多个观察者Observer
		b.addActionListener(new MyActionListener());
		b.addActionListener(new MyActionListener2());
		//将按钮添加到窗口
		this.add(b);
		//窗口把按钮包的严严实实
		this.pack();
		
		this.addWindowListener(new WindowAdapter(){

			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
			
		});
		this.setLocation(400, 400);
		this.setVisible(true);
	}
	
	public static void main(String[] args) {
		new TestFrame().launch();
	}
	
	private class MyActionListener implements ActionListener { //相当于Observer

		public void actionPerformed(ActionEvent e) {
			((Button)e.getSource()).setLabel("press me again!");
			System.out.println("button pressed!");
		}
		
	}
	
	private class MyActionListener2 implements ActionListener {

		public void actionPerformed(ActionEvent e) {
			System.out.println("button pressed 2!");
		}
		
	}
}
```

##### V9-2.自己模拟Java.awt的观察者模式


##### 钩子函数


```
package com.mashibing.dp.observer.v9;

import java.util.ArrayList;
import java.util.List;


public class Test {
	public static void main(String[] args) {
		Button b = new Button();
		b.addActionListener(new MyActionListener());
		b.addActionListener(new MyActionListener2());
		b.buttonPressed();
	}
}

class Button {
	
	private List<ActionListener> actionListeners = new ArrayList<ActionListener>();
	
	public void buttonPressed() {
		ActionEvent e = new ActionEvent(System.currentTimeMillis(),this);
		for(int i=0; i<actionListeners.size(); i++) {
			ActionListener l = actionListeners.get(i);
			l.actionPerformed(e);
		}
	}
	
	public void addActionListener(ActionListener l) {
		actionListeners.add(l);
	}
}

interface ActionListener {
	public void actionPerformed(ActionEvent e);
}

class MyActionListener implements ActionListener {

	public void actionPerformed(ActionEvent e) {
		System.out.println("button pressed!");
	}
	
}

class MyActionListener2 implements ActionListener {

	public void actionPerformed(ActionEvent e) {
		System.out.println("button pressed 2!");
	}
	
}

class ActionEvent {
	
	long when;
	Object source;
	
	public ActionEvent(long when, Object source) {
		super();
		this.when = when;
		this.source = source;
	}
	
	
	public long getWhen() {
		return when;
	}

	public Object getSource() {
		return source;
	}
	
}
```

##### V10.认识javascript的event对象

点击button时，帮我们生成一个event对象，所以可以直接拿来用

所以本质上还是Observer模式

```
<input type="button" value="press me" onclick="handle()">

<script type="text/javascript">
    function handle(){
        alert(event.target.value);//press me
    }
</script>

```


##### 总结

> Observer
> Listener监听器
> Hook钩子函数
> Callback回调函数
> 这些都是观察者模式

==在很多系统中，Observer模式往往和责任链共同负责对于事件的处理，
其中的某一个observer负责是否将事件进一步传递==

##### Observer在Tank中的应用

==这个纯粹就是为了练习Observer，纯粹就是为了牵强附会==

```
package com.mashibing.lianxi.day10Observer.observer;

import com.mashibing.lianxi.day10Observer.Tank;

 //事件本身Event
public class TankFireEvent {
    Tank tank;
    public Tank getSource(){
        return this.tank;
    }

    public TankFireEvent(Tank tank) {
        this.tank = tank;
    }
}

```

```
package com.mashibing.lianxi.day10Observer.observer;

public interface TankFireObserver {
    void actionOnFire(TankFireEvent event);
}

```

```
package com.mashibing.lianxi.day10Observer.observer;

import com.mashibing.lianxi.day10Observer.Tank;
import com.mashibing.lianxi.day10Observer.strategy.FourDirectionFireStrategy;


public class TankFireHandler implements TankFireObserver{

    @Override
    public void actionOnFire(TankFireEvent event) {
        Tank t = event.getSource();
        t.fire(FourDirectionFireStrategy.getInstance());
    }
}

```

```
package com.mashibing.lianxi.day10Observer;

public class Tank extends GameObject {
    
    ...
    private List<TankFireObserver> fireObservers = Arrays.asList(new TankFireHandler());

    public void handleFirekey() {
        TankFireEvent event = new TankFireEvent(this);
        for (TankFireObserver o:fireObservers) {
            o.actionOnFire(event);
        }
    }
}

```


```
package com.mashibing.lianxi.day10Observer;

import com.mashibing.lianxi.day10Observer.strategy.FourDirectionFireStrategy;

public class TankFrame extends Frame {
   
   ...

    class MyKeyListener extends KeyAdapter {
        
        ...
        
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
                    //方法二：直接使用类名
//                    gameModel.getMainTank().fire(FourDirectionFireStrategy.getInstance());
                    gameModel.getMainTank().handleFirekey();
                    break;
                default:
                    break;
            }
            setTankMainDirection();
        }

        
    }
}

```


### Composite组合(树状结构专用模式)

组合模式的应用：树状结构

以树为例，一颗树有叶子节点（没有子节点），也有枝节点（还有子节点）

==采用递归来遍历==


![Composite](CC8EE10D8A754859A43EF91EB311CE43)

```java
package com.mashibing.dp.composite;

import java.util.ArrayList;
import java.util.List;

abstract class Node {
    abstract public void p();
}

//叶子节点
class LeafNode extends Node {
    String content;
    public LeafNode(String content) {this.content = content;}

    @Override
    public void p() {
        System.out.println(content);
    }
}

//枝节点
class BranchNode extends Node {
    List<Node> nodes = new ArrayList<>();

    String name;
    public BranchNode(String name) {this.name = name;}

    @Override
    public void p() {
        System.out.println(name);
    }

    public void add(Node n) {
        nodes.add(n);
    }
}


public class Main {
    public static void main(String[] args) {

        BranchNode root = new BranchNode("root");
        BranchNode chapter1 = new BranchNode("chapter1");
        BranchNode chapter2 = new BranchNode("chapter2");
        Node r1 = new LeafNode("r1");
        Node c11 = new LeafNode("c11");
        Node c12 = new LeafNode("c12");
        BranchNode b21 = new BranchNode("section21");
        Node c211 = new LeafNode("c211");
        Node c212 = new LeafNode("c212");

        root.add(chapter1);
        root.add(chapter2);
        root.add(r1);
        chapter1.add(c11);
        chapter1.add(c12);
        chapter2.add(b21);
        b21.add(c211);
        b21.add(c212);

        tree(root, 0);

    }

    //采用递归来遍历
    static void tree(Node b, int depth) {
        for(int i=0; i<depth; i++) System.out.print("--");
        b.p();

        if(b instanceof BranchNode) {
            for (Node n : ((BranchNode)b).nodes) {
                tree(n, depth + 1);
            }
        }
    }
}

运行结果：
root
--chapter1
----c11
----c12
--chapter2
----section21
------c211
------c212
--r1
```



#### 作业：树的遍历：递归遍历，栈遍历（面试会考）

二叉树的非递归遍历

递归遍历，就是采用的栈遍历


### Flyweight享元模式(重复利用对象)

==这个模式其实不简单==

GOF《设计模式》举的例子是自处理软件word

你在work中敲个A，这个A可大，可小，还可以加下划线等等各种功能

你每敲一个字母，就会产生一个字母对象；如果每敲一个字母就多产生一个字母对象的话，那么字母对象就会特别多

所以享元模式就是把这些小对象装起来，A、B、一直到Z

装到一个大池子里，使用的时候直接从大池子里拿就可以，不用再创建了

所以这个就叫享元，==共享元数据==

**本质上这是池化思想，连接池、线程池都是这么回事，如果每次都new，
会new出很多，所以提前new出几个，然后重复使用，这就是享元**

![Flyweight](E72CF53DBDC74FAEB23A7EE29A2317B6)  


```
package com.mashibing.dp.flyweight;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

class Bullet{
    public UUID id = UUID.randomUUID();
    boolean living = true;

    @Override
    public String toString() {
        return "Bullet{" +
                "id=" + id +
                '}';
    }
}

public class BulletPool {
    List<Bullet> bullets = new ArrayList<>();
    {
        for(int i=0; i<5; i++) bullets.add(new Bullet());
    }

    public Bullet getBullet() {
        for(int i=0; i<bullets.size(); i++) {
            Bullet b = bullets.get(i);
            if(!b.living) return b;
        }

        return new Bullet();
    }

    public static void main(String[] args) {
        BulletPool bp = new BulletPool();

        for(int i=0; i<10; i++) {
            Bullet b = bp.getBullet();
            System.out.println(b);
        }
    }

}

```


### Java.Lang.String使用享元模式

Java中的String就是使用的享元模式

//intern就是s3对象内部指向常量池的引用


```
package com.mashibing.dp.flyweight;

public class TestString {
    public static void main(String[] args) {
        String s1 = "abc";
        String s2 = "abc";
        String s3 = new String("abc");
        String s4 = new String("abc");

        System.out.println(s1 == s2); //true
        System.out.println(s1 == s3); //false
        System.out.println(s3 == s4);//false
        //intern就是s3对象内部指向常量池的引用
        System.out.println(s3.intern() == s1);//true
        System.out.println(s3.intern() == s4.intern());//true
    }
}


```


### Flyweight结合composite（组合性享元）

![Flyweight2](7AAA3FD1CAEE493DBB3BC99BCF45A65E)

有一些组合性的享元，有单个的A、B小对象,但是还有一些由AB组成的对象
这样是不是跟Composite很像，有叶子节点A、B；也有枝节点ABABBA

**在GOF的设计模式画的图是这个图，所以你可能看不懂，是有组合性的享元**

    举例：画PPT时，画了一个正方形，又画了一个圆
    现在把正方形和圆组合起来，形成一个新的图形：图A
    以后直接复制这个图A拿来用，就是一种组合享元，这个图A就是由正方形和圆组成的

### 答疑

### 1.什么是线程安全 -》线程安全就是多个线程访问一份数据时，这份数据不会被破坏
    
    
### 2.String、StringBuffer
    
    String就是普通的字符串，里面用的都是字符串常量池
    
    StringBuffer是可变串，
    
    最大的区别就是：String线程不安全，StringBuffer是线程安全的
    
    
### ==面试就是7分运气 + 3分技术 + 90分的颜值；如果面试官看你顺眼，面面就过去了！==