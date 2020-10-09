[TOC]


# Iterator迭代器

==用于容器与容器遍历==

## 构建动态扩展的容器

### 容器的物理结构：数组Array VS 链表LinkedList

插入(中间)：链表快

添加尾部：链表快

删除:链表快

随机访问：数组快

扩展：链表快

### V1:数组的实现

ArrrayList


```
package com.mashibing.dp.Iterator.v1;

/**
 * 构建一个容器，可以添加对象
 */

public class Main {
    public static void main(String[] args) {
        ArrayList_ list = new ArrayList_();
        for(int i=0; i<15; i++) {
            list.add(new String("s" + i));
        }
        System.out.println(list.size());
    }
}


/**
 * 这个容器考虑边界问题
 */
class ArrayList_ {
    Object[] objects = new Object[10];
    //objects中下一个空的位置在哪儿,或者说，目前容器中有多少个元素
    private int index = 0;
    public void add(Object o) {
        if(index == objects.length) {
            Object[] newObjects = new Object[objects.length*2];
            //Object src,  int  srcPos,Object dest, int destPos,int length
            System.arraycopy(objects, 0, newObjects, 0, objects.length);
            objects = newObjects;
        }

        objects[index] = o;
        index ++;
    }

    public int size() {
        return index;
    }
}
```


### V2:链表的实现 

LinkedList


```
package com.mashibing.dp.Iterator.v2;

/**
 * v1:构建一个容器，可以添加对象
 * v2:用链表来实现一个容器
 */

public class Main {
    public static void main(String[] args) {
        LinkedList_ list = new LinkedList_();
        for(int i=0; i<15; i++) {
            list.add(new String("s" + i));
        }
        System.out.println(list.size());
    }
}


/**
 * 相比数组，这个容器不用考虑边界问题，可以动态扩展
 */
class LinkedList_ {
    Node head = null;
    Node tail = null;
    //目前容器中有多少个元素
    private int size = 0;

    public void add(Object o) {
        Node n = new Node(o);
        n.next = null;

        if(head == null) {
            head = n;
            tail = n;
        }

        tail.next = n;
        tail = n;
        size++;
    }

    private class Node {
        private Object o;
        Node next;

        public Node(Object o) {
            this.o = o;
        }
    }

    public int size() {
        return size;
    }
}
```


### V3.添加容器的共同接口，实现容器的替换

ArrayList和LinkedList实现Collection接口



```
package com.mashibing.dp.Iterator.v3;

public interface Collection_ {
    void add(Object o);
    int size();
}
```


```
package com.mashibing.dp.Iterator.v3;

/**
 * 这个容器考虑边界问题
 */
class ArrayList_ implements Collection_ {
    Object[] objects = new Object[10];
    //objects中下一个空的位置在哪儿,或者说，目前容器中有多少个元素
    private int index = 0;
    public void add(Object o) {
        if(index == objects.length) {
            Object[] newObjects = new Object[objects.length*2];
            System.arraycopy(objects, 0, newObjects, 0, objects.length);
            objects = newObjects;
        }

        objects[index] = o;
        index ++;
    }

    public int size() {
        return index;
    }
}
```


```
package com.mashibing.dp.Iterator.v3;

/**
 * 相比数组，这个容器不用考虑边界问题，可以动态扩展
 */
class LinkedList_ implements Collection_ {
    Node head = null;
    Node tail = null;
    //目前容器中有多少个元素
    private int size = 0;

    public void add(Object o) {
        Node n = new Node(o);
        n.next = null;

        if(head == null) {
            head = n;
            tail = n;
        }

        tail.next = n;
        tail = n;
        size++;
    }

    private class Node {
        private Object o;
        Node next;

        public Node(Object o) {
            this.o = o;
        }
    }

    public int size() {
        return size;
    }
}

```


```
package com.mashibing.dp.Iterator.v3;

/**
 * v1:构建一个容器，可以添加对象
 * v2:用链表来实现一个容器
 * v3:添加容器的共同接口，实现容器的替换
 */

public class Main {
    public static void main(String[] args) {
        Collection_ list = new ArrayList_();
        for(int i=0; i<15; i++) {
            list.add(new String("s" + i));
        }
        System.out.println(list.size());
    }
}
```


### V4.如何对容器遍历？-for循环（不好）





```
package com.mashibing.dp.Iterator.v4;

/**
 * v1:构建一个容器，可以添加对象
 * v2:用链表来实现一个容器
 * v3:添加容器的共同接口，实现容器的替换
 * v4:如何对容器遍历呢？
 */

public class Main {
    public static void main(String[] args) {
        Collection_ list = new ArrayList_();
        for(int i=0; i<15; i++) {
            list.add(new String("s" + i));
        }
        System.out.println(list.size());


        ArrayList_ al = (ArrayList_)list;
        for(int i=0; i<al.size(); i++) {
            //如果用这种遍历方式，就不能实现通用了
        }
    }
}

```


### V5.对容器遍历-迭代器


每个容器实现自己的遍历

ArrayList和LinkedList实现Iterator接口

==作业：实现LinkedList的Iterator==

```
package com.mashibing.dp.Iterator.v5;

public interface Iterator_ {
    boolean hasNext();

    Object next();
}
```
```
package com.mashibing.dp.Iterator.v5;

public interface Collection_ {
    void add(Object o);
    int size();

    Iterator_ iterator();
}
```



```
package com.mashibing.dp.Iterator.v5;

class ArrayList_ implements Collection_ {
    Object[] objects = new Object[10];
    //objects中下一个空的位置在哪儿,或者说，目前容器中有多少个元素
    private int index = 0;
    public void add(Object o) {
        if(index == objects.length) {
            Object[] newObjects = new Object[objects.length*2];
            System.arraycopy(objects, 0, newObjects, 0, objects.length);
            objects = newObjects;
        }

        objects[index] = o;
        index ++;
    }

    public int size() {
        return index;
    }

    @Override
    public Iterator_ iterator() {
        return new ArrayListIterator();
    }

    private class ArrayListIterator implements Iterator_{

        private int currentIndex = 0;

        @Override
        public boolean hasNext() {
            if(currentIndex >= index) return false;
            return true;
        }

        @Override
        public Object next() {
            Object o = objects[currentIndex];
            currentIndex ++;
            return o;
        }
    }


}
```


```
package com.mashibing.dp.Iterator.v5;

/**
 * 相比数组，这个容器不用考虑边界问题，可以动态扩展
 */
class LinkedList_ implements Collection_ {
    Node head = null;
    Node tail = null;
    //目前容器中有多少个元素
    private int size = 0;

    public void add(Object o) {
        Node n = new Node(o);
        n.next = null;

        if(head == null) {
            head = n;
            tail = n;
        }

        tail.next = n;
        tail = n;
        size++;
    }

    private class Node {
        private Object o;
        Node next;

        public Node(Object o) {
            this.o = o;
        }
    }

    public int size() {
        return size;
    }

    @Override
    public Iterator_ iterator() {
        return null;
        //李淼自己的大概思路：一个属性：node，size-》index==size;  next-> hasNext(),node.next;
    }
}

```

```
package com.mashibing.dp.Iterator.v5;

import java.util.Iterator;

/**
 * v1:构建一个容器，可以添加对象
 * v2:用链表来实现一个容器
 * v3:添加容器的共同接口，实现容器的替换
 * v4:如何对容器遍历呢？
 * v4:用一种统一的遍历方式，要求每一个容器都要提供Iterator的实现类
 *    作业：实现LinkedList的Iterator
 */

public class Main {
    public static void main(String[] args) {
        Collection_ list = new ArrayList_();
        for(int i=0; i<15; i++) {
            list.add(new String("s" + i));
        }
        System.out.println(list.size());

        //这个接口的调用方式：
        Iterator_ it = list.iterator();
        while(it.hasNext()) {
            Object o = it.next();
            System.out.println(o);
        }
    }
}
```



### V6.JDK的容器的Iterator


```
package com.mashibing.dp.Iterator.v6;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

/**
 * v1:构建一个容器，可以添加对象
 * v2:用链表来实现一个容器
 * v3:添加容器的共同接口，实现容器的替换
 * v4:如何对容器遍历呢？
 * v4:用一种统一的遍历方式，要求每一个容器都要提供Iterator的实现类
 *    作业：实现LinkedList的Iterator
 * v6:JDK的容器的Iterator
 */

public class Main {
    public static void main(String[] args) {
        Collection c = new ArrayList();
        for(int i=0; i<15; i++) {
            c.add(new String("s" + i));
        }

        Iterator it = c.iterator();
        while(it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

```
JDK-Iterator的源码

//ArrayList.Iterator
private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        // prevent creating a synthetic constructor
        Itr() {}

        public boolean hasNext() {
            return cursor != size;
        }

        @SuppressWarnings("unchecked")
        public E next() {
            checkForComodification();
            int i = cursor;
            if (i >= size)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }
    。。。。    

LinkedList.Iterator
private class ListItr implements ListIterator<E> {
        private Node<E> lastReturned;
        private Node<E> next;
        private int nextIndex;
        private int expectedModCount = modCount;

        ListItr(int index) {
            // assert isPositionIndex(index);
            next = (index == size) ? null : node(index);
            nextIndex = index;
        }

        public boolean hasNext() {
            return nextIndex < size;
        }

        public E next() {
            checkForComodification();
            if (!hasNext())
                throw new NoSuchElementException();

            lastReturned = next;
            next = next.next;
            nextIndex++;
            return lastReturned.item;
        }
    ....
  
```

==迭代器模式就是多态的应用==

到现在为止，可以看一下Iterator的类图

![Iterator](1E33211CEFED409591950025CD9612DD)

### V7:实现泛型版本

泛型的字母可以随意起，一般的有：E(Element)、T(Type)、V(Value)、K(Key)


```
package com.mashibing.dp.Iterator.v7;

public interface Iterator_<E> { //Element //Type //K //Value V Tank
    boolean hasNext();

    E next(); //Tank next() Iterator_<Tank> it = ... Tank t = it.next();
}

```


```
package com.mashibing.dp.Iterator.v7;

public interface Collection_<E> {
    void add(E o);
    int size();

    Iterator_ iterator();
}

```

```
package com.mashibing.dp.Iterator.v7;


/**
 * 相比数组，这个容器不用考虑边界问题，可以动态扩展
 */
class ArrayList_<E> implements Collection_<E> {
    E[] objects = (E[])new Object[10];
    //objects中下一个空的位置在哪儿,或者说，目前容器中有多少个元素
    private int index = 0;
    public void add(E o) {
        if(index == objects.length) {
            E[] newObjects = (E[])new Object[objects.length*2];
            System.arraycopy(objects, 0, newObjects, 0, objects.length);
            objects = newObjects;
        }

        objects[index] = o;
        index ++;
    }

    public int size() {
        return index;
    }

    @Override
    public Iterator_<E> iterator() {
        return new ArrayListIterator();
    }

    private class ArrayListIterator<E> implements Iterator_<E> {

        private int currentIndex = 0;

        @Override
        public boolean hasNext() {
            if(currentIndex >= index) return false;
            return true;
        }

        @Override
        public E next() {
            E o = (E)objects[currentIndex];
            currentIndex ++;
            return o;
        }
    }


}
```

```
package com.mashibing.dp.Iterator.v7;

/**
 * 相比数组，这个容器不用考虑边界问题，可以动态扩展
 */
class LinkedList_ implements Collection_ {
    Node head = null;
    Node tail = null;
    //目前容器中有多少个元素
    private int size = 0;

    public void add(Object o) {
        Node n = new Node(o);
        n.next = null;

        if(head == null) {
            head = n;
            tail = n;
        }

        tail.next = n;
        tail = n;
        size++;
    }

    private class Node {
        private Object o;
        Node next;

        public Node(Object o) {
            this.o = o;
        }
    }

    public int size() {
        return size;
    }

    @Override
    public Iterator_ iterator() {
        return null;
    }
}

```

```
package com.mashibing.dp.Iterator.v7;

/**
 * v1:构建一个容器，可以添加对象
 * v2:用链表来实现一个容器
 * v3:添加容器的共同接口，实现容器的替换
 * v4:如何对容器遍历呢？
 * v4:用一种统一的遍历方式，要求每一个容器都要提供Iterator的实现类
 *    作业：实现LinkedList的Iterator
 * v6:JDK的容器实现
 * v7:实现泛型版本
 */

public class Main {
    public static void main(String[] args) {
        Collection_<String> list = new ArrayList_<>();
        for(int i=0; i<15; i++) {
            list.add(new String("s" + i));
        }
        System.out.println(list.size());

        //这个接口的调用方式：
        Iterator_<String> it = list.iterator();
        while(it.hasNext()) {
            String o = it.next();
            System.out.println(o);
        }
    }
}



```


# Visitor访问者



**在结构不变的情况下动态改变对于内部元素的动作**

项目中一般用不动

==应用在1.Compiler(编译器)上面【需要不停访问AST（abstract syntax tree 抽象语法树）】==

==2.XML的文件解析==

案例：模拟电脑组装，由cpu、内存、主板三部分组成，如果是个人用户过来组装，折扣小一些；如果是企业用户组装，折扣大一些

**重点就是有一个accept(Vistor),Vistor内部有visitOperator()**

就是因为有accept(Vistor),所有Computer的代码不用变

==适合内部结构固定的情况，例如电脑就是由cpu、内存、主板三部分组成的，并且不会变==

所有的设计模式都来源于GOF四人帮《设计模式》，包括《大话设计模式》、《Head first设计模式》（讲的不是很全，也不是很好）

==模板方法跟访问者模式非常像==


```
package com.mashibing.dp.visitor;

public class Computer {
    ComputerPart cpu = new CPU();
    ComputerPart memory = new Memory();
    ComputerPart board = new Board();

    public void acccept(Visitor v) {
        this.cpu.accept(v);
        this.memory.accept(v);
        this.board.accept(v);
    }

    public static void main(String[] args) {
        PersonelVisitor p = new PersonelVisitor();
        new Computer().acccept(p);
        System.out.println(p.totalPrice);
    }
}

abstract class ComputerPart {
    abstract void accept(Visitor v);
    //some other operations eg:getName getBrand
    abstract double getPrice();
}

class CPU extends ComputerPart {

    @Override
    void accept(Visitor v) {
        v.visitCpu(this);
    }

    @Override
    double getPrice() {
        return 500;
    }
}

class Memory extends ComputerPart {

    @Override
    void accept(Visitor v) {
        v.visitMemory(this);
    }

    @Override
    double getPrice() {
        return 300;
    }
}

class Board extends ComputerPart {

    @Override
    void accept(Visitor v) {
        v.visitBoard(this);
    }

    @Override
    double getPrice() {
        return 200;
    }
}

interface Visitor {
    void visitCpu(CPU cpu);
    void visitMemory(Memory memory);
    void visitBoard(Board board);
}

class PersonelVisitor implements Visitor {
    double totalPrice = 0.0;

    @Override
    public void visitCpu(CPU cpu) {
        totalPrice += cpu.getPrice()*0.9;
    }

    @Override
    public void visitMemory(Memory memory) {
        totalPrice += memory.getPrice()*0.85;
    }

    @Override
    public void visitBoard(Board board) {
        totalPrice += board.getPrice()*0.95;
    }
}

class CorpVisitor implements Visitor {
    double totalPrice = 0.0;

    @Override
    public void visitCpu(CPU cpu) {
        totalPrice += cpu.getPrice()*0.6;
    }

    @Override
    public void visitMemory(Memory memory) {
        totalPrice += memory.getPrice()*0.75;
    }

    @Override
    public void visitBoard(Board board) {
        totalPrice += board.getPrice()*0.75;
    }
}

```


![Visitor](610127B9CB594EE68B4EB0AB73A29A09)

# ASM ? (JVM细节 class类文件的格式）繁琐 + 枯燥

iterator + vistor + chainofresposibility + Adapter

ASM transformer使用了Adapter模式

iterator(树状结构的遍历)：把一个二进制文件，看成一个node，有各种子节点：filed、method、constantPool

Vistor：ClassVistor类


ASM:AsSeMbly 汇编语言，这里专门指的是java的汇编语言，也就是bytecode二进制码这个语言

ASM:对字节码进行加工


## 如何学一个新知识

==学一个新东西的方法：1.找最原始的网站（http:/asm.ow2.io）==

==2.找官方的API文档==，

==3.文档阅读先看目录，哪个目录比较重要，就看哪个；不重要的直接略过==
    
    ASM文档从core API开始看

==4.大多数的文档，看第一句话就能知道干什么的，如果你觉得用不上就略过==

5.看例子程序，例如2.2.2 Parsing classes

ClassVistor  ClassReader

怎么看java的字节码文件：

1.JClassLib-IDEA插件之一

2.javap  javap -v class文件路径 （可以反编译class文件）

## 通过ASM来读class文件，利用程序来进行反编译

文档例子：2.2.2 Parsing classes

通过Vistor模式理解就不难了

有一个Vistor：ClassVistor

让ClassReader去访问class文件，让ClassPrinter去打印，访问一个打印一个

```
package com.mashibing.dp.ASM;

/**
 * 光标必须位于类体内，View-Show ByteCode
 */

public class T1 {
    int i = 0;
    public void m() {
        int j=1;
    }
}

```

```
package com.mashibing.dp.ASM;

import org.objectweb.asm.ClassReader;
import org.objectweb.asm.ClassVisitor;
import org.objectweb.asm.FieldVisitor;
import org.objectweb.asm.MethodVisitor;

import java.io.IOException;

import static org.objectweb.asm.Opcodes.ASM4;

/**
 * 通过ASM来读class文件，利用程序来进行反编译
 * 通过Vistor模式理解就不难了
 * 有一个Vistor：ClassVistor
 * 让ClassReader去访问class文件，让ClassPrinter去打印，访问一个打印一个
 */
public class ClassPrinter extends ClassVisitor {
    public ClassPrinter() {
        super(ASM4);
    }

    @Override
    public void visit(int version, int access, String name, String signature, String superName, String[] interfaces) {
        System.out.println(name + " extends " + superName + "{" );
    }

    @Override
    public FieldVisitor visitField(int access, String name, String descriptor, String signature, Object value) {
        System.out.println("    " + name);
        return null;
    }

    @Override
    public MethodVisitor visitMethod(int access, String name, String descriptor, String signature, String[] exceptions) {
        System.out.println("    " + name + "()");
        return null;
    }

    @Override
    public void visitEnd() {

        System.out.println("}");
    }

    public static void main(String[] args) throws IOException {
        ClassPrinter cp = new ClassPrinter();
        //ClassReader cr = new ClassReader("java.lang.Runnable");
        ClassReader cr = new ClassReader(
                ClassPrinter.class.getClassLoader().getResourceAsStream("com/mashibing/dp/ASM/T1.class"));


        cr.accept(cp, 0);
    }
}
运行结果：

com/mashibing/dp/ASM/T1 extends java/lang/Object{
    i
    <init>()
    m()
}

```

## 生成class文件

文档例子：2.2.3 Generating classes

==如果有一门新的语言想编译成class文件，怎么做呢，用ASM就可以实现==

ClassWriter也是一个Vistor


```
package com.mashibing.dp.ASM;

import org.objectweb.asm.ClassWriter;

import static org.objectweb.asm.Opcodes.*;

/**
 * 生成class文件
 */
public class ClassWriteTest {
    public static void main(String[] args) {
        ClassWriter cw = new ClassWriter(0);
        cw.visit(V1_5, ACC_PUBLIC + ACC_ABSTRACT + ACC_INTERFACE,
                "pkg/Comparable", null, "java/lang/Object",
                null);
        cw.visitField(ACC_PUBLIC + ACC_FINAL + ACC_STATIC, "LESS", "I",
                null, -1).visitEnd();
        cw.visitField(ACC_PUBLIC + ACC_FINAL + ACC_STATIC, "EQUAL", "I",
                null, 0).visitEnd();
        cw.visitField(ACC_PUBLIC + ACC_FINAL + ACC_STATIC, "GREATER", "I",
                null, 1).visitEnd();
        cw.visitMethod(ACC_PUBLIC + ACC_ABSTRACT, "compareTo",
                "(Ljava/lang/Object;)I", null, null).visitEnd();
        cw.visitEnd();
        byte[] b = cw.toByteArray();

        MyClassLoader myClassLoader = new MyClassLoader();
        Class c = myClassLoader.defineClass("pkg.Comparable", b);
        System.out.println(c.getMethods()[0].getName());
    }
}

运行结果：compareTo
```


## ASM生成动态代理

文档例子：2.2.4 Transforming classes

ClassTransformerTest

**如果只使用了ClassWriter，那么就是把之前的class文件拷贝了一份**

**ClassWriter传给了ClassVisitor,ClassReader接受了ClassVisitor,从而形成了一个链条**；

**就是相当于ClassReader读出原来的class文件，ClassVisitor通过MethodVisitor修改了methed内容（相当于Visitor套Visitor），最后ClassVisitor再把修改的class内容交给ClassWriter**

```
package com.mashibing.dp.ASM;

public class Tank {
    public void move(){
        System.out.println("Tank Moving ClaClaCla ...");
    }
}
```

```
public class TimeProxy {

    public static void before() {
        System.out.println("before ...");
    }
}
```


```
package com.mashibing.dp.ASM;

class MyClassLoader extends ClassLoader {
    public Class defineClass(String name, byte[] b) {
        return defineClass(name, b, 0, b.length);
    }
}

```

```
package com.mashibing.dp.ASM;

import org.objectweb.asm.*;

import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStream;

import static org.objectweb.asm.Opcodes.*;

public class ClassTransformerTest {
    public static void main(String[] args) throws Exception {
        ClassReader cr = new ClassReader(
                ClassPrinter.class.getClassLoader().getResourceAsStream("com/mashibing/dp/ASM/Tank.class"));

        ClassWriter cw = new ClassWriter(0);
        ClassVisitor cv = new ClassVisitor(ASM4, cw) {
            @Override
            public MethodVisitor visitMethod(int access, String name, String descriptor, String signature, String[] exceptions) {
                MethodVisitor mv = super.visitMethod(access, name, descriptor, signature, exceptions);
                //return mv;
                return new MethodVisitor(ASM4, mv) {
                    @Override
                    public void visitCode() {
                        visitMethodInsn(INVOKESTATIC, "com/mashibing/dp/ASM/TimeProxy","before", "()V", false);
                        super.visitCode();
                    }
                };
            }
        };

//        cr.accept(cw,0);//如果只使用了ClassWriter，那么就是把之前的class文件拷贝了一份

        //ClassReader读出原来的class文件，ClassVisitor通过MethodVisitor修改了methed内容，最后ClassVisitor再把修改的class内容交给ClassWriter
        byte[] b2 = cw.toByteArray();
        cr.accept(cv, 0);

        MyClassLoader cl = new MyClassLoader();
        //Class c = cl.loadClass("com.mashibing.dp.ASM.Tank");
        cl.loadClass("com.mashibing.dp.ASM.TimeProxy");
        Class c2 = cl.defineClass("com.mashibing.dp.ASM.Tank", b2);
        c2.getConstructor().newInstance();


        String path = (String)System.getProperties().get("user.dir");
        File f = new File(path + "/com/mashibing/dp/ASM/");
        f.mkdirs();

        FileOutputStream fos = new FileOutputStream(new File(path + "/com/mashibing/dp/ASM/Tank_0.class"));
        fos.write(b2);
        fos.flush();
        fos.close();

    }
}
运行结果：运行有问题
```


# 作业：实现LinkedList的Iterator