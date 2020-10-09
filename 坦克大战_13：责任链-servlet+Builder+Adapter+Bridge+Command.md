[TOC]

# COR的作业

    模拟Servlet的FilterChain
    
## V1:模拟Servlet的责任链

B站预习课的代码,最初版本


```
package com.mashibing.dp.cor.servlet.v1;

import java.util.ArrayList;
import java.util.List;

public class Servlet_Main {
    public static void main(String[] args) {
        Request request = new Request();
        request.str = "大家好:)，<script>，欢迎访问 mashibing.com ，大家都是996 ";
        Response response = new Response();
        response.str = "";

        FilterChain chain = new FilterChain();
        chain.add(new HTMLFilter()).add(new SensitiveFilter());
        chain.doFilter(request, response);
        System.out.println(request.str);

    }
}

interface Filter {
    boolean doFilter(Request request,Response response);
}

class HTMLFilter implements Filter {
    @Override
    public boolean doFilter(Request request,Response response) {
        request.str = request.str.replaceAll("<", "[").replaceAll(">", "]");
        return true;
    }
}

class Request {
    String str;
}

class Response {
    String str;
}

class SensitiveFilter implements Filter {
    @Override
    public boolean doFilter(Request request,Response response) {
        request.str = request.str.replaceAll("996", "955");
        return true;
    }
}



class FilterChain implements Filter {
    List<Filter> filters = new ArrayList<>();

    public FilterChain add(Filter f) {
        filters.add(f);
        return this;
    }

    public boolean doFilter(Request request,Response response) {

        for(Filter f : filters ){
            f.doFilter(request, response);
        }
        return true;
    }
}
```


## V2：直接将response的处理放在request的下面（不可行）

用最直观的方式处理response
直接将response的处理放在request的下面


```
package com.mashibing.dp.cor.servlet.v2;

import java.util.ArrayList;
import java.util.List;

public class Servlet_Main {
    public static void main(String[] args) {
        Request request = new Request();
        request.str = "大家好:)，<script>，欢迎访问 mashibing.com ，大家都是996 ";
        Response response = new Response();
        response.str = "response";

        FilterChain chain = new FilterChain();
        chain.add(new HTMLFilter()).add(new SensitiveFilter());
        chain.doFilter(request, response);
        System.out.println(request.str);
        System.out.println(response.str);

    }
}

interface Filter {
    boolean doFilter(Request request, Response response);
}

class HTMLFilter implements Filter {
    @Override
    public boolean doFilter(Request request, Response response) {
        request.str = request.str.replaceAll("<", "[").replaceAll(">", "]");
        response.str += "--HTMLFilter()";
        return true;
    }
}

class Request {
    String str;
}

class Response {
    String str;
}

class SensitiveFilter implements Filter {
    @Override
    public boolean doFilter(Request request, Response response) {
        request.str = request.str.replaceAll("996", "955");
        response.str += "--SensitiveFilter()";
        return true;
    }
}


class FilterChain implements Filter {
    List<Filter> filters = new ArrayList<>();

    public FilterChain add(Filter f) {
        filters.add(f);
        return this;
    }

    public boolean doFilter(Request request, Response response) {

        for(Filter f : filters ){
            f.doFilter(request, response);
        }
        return true;
    }
}
```


## V3:Filter中加入第三个参数FilterChain

==doFilter方法的返回类型为boolean==

==FilterChain责任链的doFilter方法中，f.doFilter(request, response, chain);第三个参数为chain==

在filterChain中处理加入位置的记录

同时在filter中加入第三个参数

==要注意理解调用过程==


```
package com.mashibing.dp.cor.servlet.v3;

import java.util.ArrayList;
import java.util.List;

public class Servlet_Main {
    public static void main(String[] args) {
        Request request = new Request();
        request.str = "大家好:)，<script>，欢迎访问 mashibing.com ，大家都是996 ";
        Response response = new Response();
        response.str = "response";

        FilterChain chain = new FilterChain();
        chain.add(new HTMLFilter()).add(new SensitiveFilter());
        chain.doFilter(request, response, chain);
        System.out.println(request.str);
        System.out.println(response.str);

    }
}

interface Filter {
    boolean doFilter(Request request, Response response, FilterChain chain);
}

class HTMLFilter implements Filter {
    @Override
    public boolean doFilter(Request request, Response response, FilterChain chain) {
        request.str = request.str.replaceAll("<", "[").replaceAll(">", "]") + "HTMLFilter()";
        chain.doFilter(request, response, chain);
        response.str += "--HTMLFilter()";
        return true;
    }
}

class Request {
    String str;
}

class Response {
    String str;
}

class SensitiveFilter implements Filter {
    @Override
    public boolean doFilter(Request request, Response response, FilterChain chain) {
        request.str = request.str.replaceAll("996", "955") + " SensitiveFilter()";
        chain.doFilter(request, response, chain);
        response.str += "--SensitiveFilter()";
        return true;
    }
}


class FilterChain implements Filter {
    List<Filter> filters = new ArrayList<>();
    int index = 0;

    public FilterChain add(Filter f) {
        filters.add(f);
        return this;
    }

    public boolean doFilter(Request request, Response response, FilterChain chain) {
        if(index == filters.size()) return false;
        Filter f = filters.get(index);
        index ++;

        return f.doFilter(request, response, chain);
    }
}
```


### 作业：把调用过程画图

## V4:完全模拟Serverlet中的FilterChain

FilterChain没有实现Filter接口

FilterChain的doFilter方法的返回类型为void

FilterChain的doFilter方法中，f.doFilter(request, response, chain);第三个参数为this

==Servlet、Strusts2拦截器、Spring拦截器(只不过不是Filter，而是Interceptor)都是用的责任链模式==


```
package com.mashibing.dp.cor.servlet.v4;

import java.util.ArrayList;
import java.util.List;

public class Servlet_Main {
    public static void main(String[] args) {
        Request request = new Request();
        request.str = "大家好:)，<script>，欢迎访问 mashibing.com ，大家都是996 ";
        Response response = new Response();
        response.str = "response";

        FilterChain chain = new FilterChain();
        chain.add(new HTMLFilter()).add(new SensitiveFilter());
        chain.doFilter(request, response);
        System.out.println(request.str);
        System.out.println(response.str);

    }
}

interface Filter {
    void doFilter(Request request, Response response, FilterChain chain);
}

class HTMLFilter implements Filter {
    @Override
    public void doFilter(Request request, Response response, FilterChain chain) {
        request.str = request.str.replaceAll("<", "[").replaceAll(">", "]") + "HTMLFilter()";
        chain.doFilter(request, response);
        response.str += "--HTMLFilter()";

    }
}

class Request {
    String str;
}

class Response {
    String str;
}

class SensitiveFilter implements Filter {
    @Override
    public void doFilter(Request request, Response response, FilterChain chain) {
        request.str = request.str.replaceAll("996", "955") + " SensitiveFilter()";
        chain.doFilter(request, response);
        response.str += "--SensitiveFilter()";

    }
}

//FilterChain没有实现Filter接口
class FilterChain {
    List<Filter> filters = new ArrayList<>();
    int index = 0;

    public FilterChain add(Filter f) {
        filters.add(f);
        return this;
    }

    public void doFilter(Request request, Response response) {
        if(index == filters.size()) return;
        Filter f = filters.get(index);
        index ++;

        //传入的责任链条是当前链条
        f.doFilter(request, response, this);
    }
}
```


# 今天的设计模式要么不重要，要么简单

## Builder构建器

应用：构建复杂对象

分离复杂对象的构建和表示

同样的构建过程可以创建不同的表示

无需记忆，自然使用；

了解了面对对象的概念，自然就会使用

==这个跟模板方法非常像，只不过构建器强调的是对象构建，而模板方法强调的是方法执行==

Terrain地形，地形会很复杂，有墙，有暗堡，有地雷

现在要构建一个很复杂的地形，可以使用不同的构建器

==注意：TerrainBuilder接口返回的类型是TerrainBuilder，方便编程；Builder模式经常使用这样链条式的编程==


```
Terrain t = builder.buildFort().buildMine().buildWall().build();

public interface TerrainBuilder {
    TerrainBuilder buildWall();
    TerrainBuilder buildFort();
    TerrainBuilder buildMine();
    Terrain build();
}
```

《Effective Java》书中推荐，如果要构建复杂对象，建议用Builder模式

例如Person，有二三十个属性或者更多

Person的构造方法为private

PersonBuilder静态内部类

build方法返回Person对象

==这个例子叫静态工厂也没问题==

==学习设计模式，读开源源码会非常方便，要么会比较晦涩==


![Builder](DBF61D0CAACE4F57BA68C3799B746F65)


```
package com.mashibing.dp.builder;

public class Person {
    int id;
    String name;
    int age;
    double weight;
    int score;
    Location loc;

    private Person() {}

    public static class PersonBuilder {
        Person p = new Person();

        public PersonBuilder basicInfo(int id, String name, int age) {
            p.id = id;
            p.name = name;
            p.age = age;
            return this;
        }

        public PersonBuilder weight(double weight) {
            p.weight = weight;
            return this;
        }

        public PersonBuilder score(int score) {
            p.score = score;
            return this;
        }

        public PersonBuilder loc(String street, String roomNo) {
            p.loc = new Location(street, roomNo);
            return this;
        }

        public Person build() {
            return p;
        }
    }
}

class Location {
    String street;
    String roomNo;

    public Location(String street, String roomNo) {
        this.street = street;
        this.roomNo = roomNo;
    }
}

```

```
package com.mashibing.dp.builder;

public class Main {
    public static void main(String[] args) {
        TerrainBuilder builder = new ComplexTerrainBuilder();
        Terrain t = builder.buildFort().buildMine().buildWall().build();
        //new Terrain(Wall w, Fort f, Mine m)
        //Effective Java

        Person p = new Person.PersonBuilder()
                .basicInfo(1, "zhangsan", 18)
                //.score(20)
                .weight(200)
                //.loc("bj", "23")
                .build();
    }
}

```

```
package com.mashibing.dp.builder;

public class Terrain {
    Wall w;
    Fort f;
    Mine m;
}

class Wall {
    int x, y, w, h;

    public Wall(int x, int y, int w, int h) {
        this.x = x;
        this.y = y;
        this.w = w;
        this.h = h;
    }
}

class Fort {
    int x, y, w, h;

    public Fort(int x, int y, int w, int h) {
        this.x = x;
        this.y = y;
        this.w = w;
        this.h = h;
    }

}

class Mine {
    int x, y, w, h;

    public Mine(int x, int y, int w, int h) {
        this.x = x;
        this.y = y;
        this.w = w;
        this.h = h;
    }
}

```

```
package com.mashibing.dp.builder;

public interface TerrainBuilder {
    TerrainBuilder buildWall();
    TerrainBuilder buildFort();
    TerrainBuilder buildMine();
    Terrain build();
}

```

```
package com.mashibing.dp.builder;

public class ComplexTerrainBuilder implements TerrainBuilder {
    Terrain terrain = new Terrain();

    @Override
    public TerrainBuilder buildWall() {
        terrain.w = new Wall(10, 10, 50, 50);
        return this;
    }

    @Override
    public TerrainBuilder buildFort() {
        terrain.f = new Fort(10, 10, 50, 50);
        return this;
    }

    @Override
    public TerrainBuilder buildMine() {
        terrain.m = new Mine(10, 10, 50, 50);
        return this;
    }

    @Override
    public Terrain build() {
        return terrain;
    }
}

```


## Adapter（Wrapper）转换器（包装器）或适配器模式

如果一个类不能直接访问另一个类，中间加一个转换就叫Adapter


类似：电压转接头

应用：

1.java.io：InputStreamReader和OutputStreamWriter
    
    读一个文件，想按行读出来，最好用BufferedReader,
    但是BufferedReader只能传Reader，不能传InputStream
    这时候需要将FileInputStream转换为InputStreamReader
    所以InputStreamReader和OutputStreamWriter就是Adapter模式

```
package com.mashibing.dp.adapter;

import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.InputStreamReader;


public class Main {
    public static void main(String[] args) throws Exception {
        FileInputStream fis = new FileInputStream("c:/test.text");
        InputStreamReader isr = new InputStreamReader(fis);
        BufferedReader br = new BufferedReader(isr);
        String line = br.readLine();
        while (line != null && !line.equals("")) {
            System.out.println(line);
        }
        br.close();


    }
}

```
   

2.jdbc-odbc bridge(不是桥接模式)

    SQL-Server数据库,微软产品，微软产品暴露的接口是odbc
    而java只能访问jdbc
    这时候来一个中间驱动：jdbc-odbc bridge
    
3.==ASM transformer==

![Adapter](1274C97D61974D7F8B53468B33451278)

常见的误区：

1.==常见的Adapter类反而不是Adapter模式==,它什么模式也不是，只是一种比较方便的编程方式

WindowAdapter、KeyAdapter

> java.awt.event.WindowListener接口中有很多方法
> 
> 当你要添加一个WindowListener时，你需要实现WindowListener中的所有接口，
> 
> 但是呢，你只需要windowClosing方法，这样写就太麻烦了
> 
> 我们一般是添加一个WindowAdapter，然后重写windowClosing就可以了
> 
> ==WindowAdapter是一个抽象类，它实现了WindowListener接口（都是空方法），这时候你需要重写你关心的那个方法就可以了==
> 


```
public class Test extends WindowAdapter {

    @Override
    public void windowClosing(WindowEvent e) {
        super.windowClosing(e);
    }

    public static void main(String[] args) {
        Test t = new Test();
//        Frame f = new Frame();
//        f.addWindowListener();
    }
}
```


## Bridge桥接模式

==双维度扩展==

了解即可，实际用到很少

分离抽象与具体

**用聚合方式(桥)链接抽象与具体**

### V1:送姑娘一个礼物：花、书

```
public abstract class Gift {}

public class Book extends Gift {
}
public class Flower extends Gift {
}

public class MM {
    String name;
}

public class GG {
    public void chase(MM mm) {
        Gift g = new Book();
        give(mm, g);
    }

    public void give(MM mm, Gift g) {

    }


}
```


### V2:如果礼物分为温柔的礼物和狂野的礼物

如果礼物分为温柔的礼物和狂野的礼物

WarmGift WildGift

```
public class WildGift extends Gift {
}
public class WarmGift extends Gift {
}
public class GG {
    public void chase(MM mm) {
        Gift g = new Book();
        give(mm, g);
    }

    public void give(MM mm, Gift g) {

    }
}
```

### V3：再送别的礼物，产生类爆炸

    如果礼物分为温柔的礼物和狂野的礼物
    WarmGift WildGift
    这时Flower应该分为
    WarmFlower WildFlower
    WarmBook WildBook
    
    如果再有别的礼物，比如抽象类型：ToughGift ColdGift
    或者具体的某种实现：Ring Car
    
    就会产生类的爆炸
    WarmCar ColdRing WildCar WildFlower ...

### V4:使用桥接模式

**使用桥接模式：分离抽象与具体实现，让他们可以独自发**展
    
Gift -> WarmGift ColdGift WildGift

GiftImpl -> Flower Ring Car
    
**抽象类GiftImpl**

**Gift聚合一个GiftImpl**

桥接模式：用聚合代替继承，来解决类爆炸

![Bridge](5F27AB6D0F80490EB9AAC6E79FC1839A)

==作业：下载《设计模式》的PDF书==

==简历：精通设计模式==

==前面的设计模式必须掌握，后面的设计模式了解即可==


```
public class MM {
    String name;
}

public class GiftImpl {
}

public class Book extends GiftImpl {
}

public class Flower extends GiftImpl {
}

public abstract class Gift {
    GiftImpl impl;
}

public class WarmGift extends Gift {
    public WarmGift(GiftImpl impl) {
        this.impl = impl;
    }
}

public class WildGift extends Gift {
    public WildGift(GiftImpl impl) {
        this.impl = impl;
    }
}

public class GG {
    public void chase(MM mm) {
        Gift g = new WarmGift(new Flower());
        give(mm, g);
    }

    public void give(MM mm, Gift g) {
        System.out.println(g + "gived!");
    }

}
```


## Command 命令模式

**应用：封装命令**

### 结合cor实现undo（撤回）功能

Command封装命令，有执行方法：do 和undo

本例为GOF《设计模式》的例子

InsertCommand插入命令、CopyCommand拷贝命令、DeleteCommand删除命令

```
public class Content {
    String msg = "hello everybody ";

}

public abstract class Command {
    public abstract void doit(); //exec run
    public abstract void undo();
}

public class CopyCommand extends Command {
    Content c;
    public CopyCommand(Content c) {
        this.c = c;
    }

    @Override
    public void doit() {
        c.msg = c.msg + c.msg;
    }

    @Override
    public void undo() {
        c.msg = c.msg.substring(0, c.msg.length()/2);
    }
}

public class DeleteCommand extends Command {
    Content c;
    String deleted;
    public DeleteCommand(Content c) {
        this.c = c;
    }

    @Override
    public void doit() {
        deleted = c.msg.substring(0, 5);
        c.msg = c.msg.substring(5, c.msg.length());
    }

    @Override
    public void undo() {
        c.msg = deleted + c.msg;
    }
}

public class InsertCommand extends Command {
    Content c;
    String strToInsert = "http://www.mashibing.com";
    public InsertCommand(Content c) {
        this.c = c;
    }

    @Override
    public void doit() {
        c.msg = c.msg + strToInsert;
    }

    @Override
    public void undo() {
        c.msg = c.msg.substring(0, c.msg.length()-strToInsert.length());
    }
}

public class Main {
    public static void main(String[] args) {
        Content c = new Content();

        Command insertCommand = new InsertCommand(c);
        insertCommand.doit();
        insertCommand.undo();

        Command copyCommand = new CopyCommand(c);
        insertCommand.doit();
        insertCommand.undo();

        Command deleteCommand = new DeleteCommand(c);
        deleteCommand.doit();
        deleteCommand.undo();

        List<Command> commands = new ArrayList<>();
        commands.add(new InsertCommand(c));
        commands.add(new CopyCommand(c));
        commands.add(new DeleteCommand(c));

        for(Command comm : commands) {
            comm.doit();
        }

        System.out.println(c.msg);

        for(int i= commands.size()-1; i>=0; i--) {
            commands.get(i).undo();
        }

        System.out.println(c.msg);
    }
}

```

![Command](62E18ECF7E4A4F45B073941335BCC812)


### 进一步了解Command模式

别名：Action/Transaction

**宏命令：command与Composite组合模式**

    宏命令：由很多个命令组成
    树状结构
    
**多次undo：command与chainOfResposition责任链模式**

    多次undo：执行一连串的undo
    所有执行过的Command，放到一个容器里，当你要执行undo时，迭代容器中Command中的undo方法

transaction回滚：command与？记忆模式

# 作业：command + cor

讲网络版之前，IO不了解，看之前网络编程的视频

公开课：BIO、NIO、AIO 到Netty