
    钩子函数、回调函数胡
    
    解析：
        其实，你一直在用
            paint(Graphics g)
            WindowListener
                windowClosing
                windowXXX
            ASM
                ClassVisitor
            
    定义一个方法，然后系统帮我们自动调用的

![image](https://raw.githubusercontent.com/musictaste/DesignPatterns/master/image/001.png)


```
public class Main {
    public static void main(String[] args) {
        F f = new C1();
        f.m();
    }

}

abstract class F {
    public void m() {
        op1();
        op2();
    }

    abstract void op1();
    abstract void op2();
}

class C1 extends F {

    @Override
    void op1() {
        System.out.println("op1");
    }

    @Override
    void op2() {
        System.out.println("op2");
    }
}
```
