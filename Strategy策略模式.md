[TOC]

### 1.Comparatable  不是策略模式

int	==compareTo==​(T o)	Compares this object with the specified object for order.
    
```
package com.mashibing.dp.strategy.mycode.camparable;

/**
 * @Description: 自定义一个Comparable接口，使用泛型
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
//如果只有一个抽象方法，可以不加注解
//@FunctionalInterface
public interface Comparable<T> {
    int compareTo(T t);
    default void m(){
        System.out.println("test");
    }
}


package com.mashibing.dp.strategy.mycode.camparable;

/**
 * @ClassName Cat
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Cat implements Comparable<Cat>{
    private int weight,height;

    public Cat(int weight, int height) {
        this.weight = weight;
        this.height = height;
    }

    @Override
    public String toString() {
        return "Cat{" +
                "weight=" + weight +
                ", height=" + height +
                '}';
    }

    @Override
    public int compareTo(Cat c) {
        if(this.weight < c.weight) return -1;
        else if(this.weight > c.weight) return 1;
        else return 0;
    }
}

package com.mashibing.dp.strategy.mycode.camparable;

/**
 * @ClassName Dog
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/6/17
 * @Version V1.0
 **/
public class Dog implements Comparable<Dog> {
    private int food;

    public Dog(int food) {
        this.food = food;
    }

    @Override
    public int compareTo(Dog dog) {
        if(this.food>dog.food) return 1;
        else if(this.food<dog.food) return -1;
        else return 0;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "food=" + food +
                '}';
    }
}


package com.mashibing.dp.strategy.mycode.camparable;

/**
 * @ClassName Sort
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Sort {

    public void sort(Comparable[] arr){
        for (int i = 0; i < arr.length-1; i++) {
            int min =i;
            for (int j = i+1; j < arr.length; j++) {
                min = arr[j].compareTo(arr[min])== -1?j:min;
            }
            swap(arr,i,min);
        }
    }

    public void swap(Comparable[] arr,int i,int j){
        Comparable temp = arr[i];
        arr[i]=arr[j];
        arr[j]=temp;
    }

}


package com.mashibing.dp.strategy.mycode.camparable;

import java.util.Arrays;

/**
 * @ClassName Main
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Main {
    public static void main(String[] args) {
        Cat[] arr = {new Cat(3,3),new Cat(5,5),new Cat(1,1)};
        Sort sort = new Sort();
        sort.sort(arr);
        System.out.println(Arrays.toString(arr));

        Dog[] dogs = {new Dog(3),new Dog(5),new Dog(1)};
        sort.sort(dogs);
        System.out.println(Arrays.toString(dogs));
    }
}


```

![002](6F24D69E815048548611077DE1055C08)
    
### 2.Comparator  策略模式

    https://docs.oracle.com/en/java/javase/13/docs/api/java.base/java/util/Comparator.html
    
int	==compare==​(T o1, T o2) Compares its two arguments for order.

boolean	==equals==​(Object obj)	 Indicates whether some other object is "equal to" this comparator.
    
==**开闭原则：对修改关闭，对扩展开放**==


```
package com.mashibing.dp.strategy.mycode.camparator;

/**
 * @ClassName Comparator
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
//如果只有一个抽象方法，可以不加注解，另外可以使用lambda表达式
//函数式接口
//@FunctionalInterface
public interface Comparator<T> {
    int compare(T t1,T t2);

    //1.8以后，可以写方法实现;是为了兼容1.8之前的版本
    default void m(){
        System.out.println("m");
    }
}


package com.mashibing.dp.strategy.mycode.camparator;

public class Cat {
    int weight,height;

    public Cat(int weight, int height) {
        this.weight = weight;
        this.height = height;
    }

    @Override
    public String toString() {
        return "Cat{" +
                "weight=" + weight +
                ", height=" + height +
                '}';
    }
}


package com.mashibing.dp.strategy.mycode.camparator;

/**
 * @ClassName CatWeightComparator
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class CatHeightComparator implements Comparator<Cat> {

    @Override
    public int compare(Cat t1, Cat t2) {
        if(t1.weight > t2.weight) return -1;
        else if(t1.weight <t2.weight) return 1;
        else return 0;
    }
}


package com.mashibing.dp.strategy.mycode.camparator;

/**
 * @ClassName CatWeightComparator
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class CatWeightComparator implements Comparator<Cat> {

    @Override
    public int compare(Cat t1, Cat t2) {
        if(t1.weight < t2.weight) return -1;
        else if(t1.weight >t2.weight) return 1;
        else return 0;
    }
}


package com.mashibing.dp.strategy.mycode.camparator;

/**
 * @ClassName Sorter
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Sorter<T> {
    public void sort(T[] arr,Comparator<T> comparator){
        for (int i = 0; i < arr.length-1; i++) {
            int min = i;
            for (int j = i; j < arr.length; j++) {
                min = comparator.compare(arr[j],arr[min])==-1? j:min;
            }
            swap(arr,i,min);
        }
    }

    public void swap(T[] arr,int i,int j){
        T temp = arr[i];
        arr[i]=arr[j];
        arr[j]=temp;
    }
}


package com.mashibing.dp.strategy.mycode.camparator;

import java.util.Arrays;

/**
 * @ClassName Main
 * @Description: TODO
 * @Author 李淼
 * @Date 2020/3/20
 * @Version V1.0
 **/
public class Main {
    public static void main(String[] args) {
        Cat[] arr = {new Cat(3,3),new Cat(5,5),new Cat(1,1)};
        Sorter<Cat> sorter = new Sorter<>();
        sorter.sort(arr,new CatWeightComparator());
        System.out.println(Arrays.toString(arr));

        System.out.println("==============");
        sorter.sort(arr,new CatHeightComparator());
        System.out.println(Arrays.toString(arr));

        System.out.println("==============");
        //lambda表达式  函数式接口
        sorter.sort(arr,(o1,o2)->{
            if(o1.weight<o2.weight) return -1;
            else if(o1.weight >o2.weight) return 1;
            else return 0;
        });
        System.out.println(Arrays.toString(arr));
    }
}


```


策略模式应用到：Tank.fire

    策略1：默认策略 一颗子弹
    策略2：四个方向同时打出子弹
    策略3：打核弹
    .....