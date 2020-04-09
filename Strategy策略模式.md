![image](https://raw.githubusercontent.com/musictaste/DesignPatterns/master/image/002.png)

#### Comparatable

int	==compareTo==​(T o)	Compares this object with the specified object for order.
    
```
//如果只有一个抽象方法，可以不加注解
//@FunctionalInterface
public interface Comparable<T> {
    int compareTo(T t);
    default void m(){
        System.out.println("test");
    }
}

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

public class Main {
    public static void main(String[] args) {
        Cat[] arr = {new Cat(3,3),new Cat(5,5),new Cat(1,1)};
        Sort sort = new Sort();
        sort.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}

```

    
#### Comparator

    https://docs.oracle.com/en/java/javase/13/docs/api/java.base/java/util/Comparator.html
    
int	==compare==​(T o1, T o2) Compares its two arguments for order.

boolean	==equals==​(Object obj)	 Indicates whether some other object is "equal to" this comparator.
    
==**开闭原则：对修改关闭，对扩展开放**==


```
//如果只有一个抽象方法，可以不加注解，另外可以使用lambda表达式
//@FunctionalInterface
public interface Comparator<T> {
    int compare(T t1,T t2);
}

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

public class CatHeightComparator implements Comparator<Cat> {

    @Override
    public int compare(Cat t1, Cat t2) {
        if(t1.weight > t2.weight) return -1;
        else if(t1.weight <t2.weight) return 1;
        else return 0;
    }
}

public class CatWeightComparator implements Comparator<Cat> {

    @Override
    public int compare(Cat t1, Cat t2) {
        if(t1.weight < t2.weight) return -1;
        else if(t1.weight >t2.weight) return 1;
        else return 0;
    }
}

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
        //lambda表达式
        sorter.sort(arr,(o1,o2)->{
            if(o1.weight<o2.weight) return -1;
            else if(o1.weight >o2.weight) return 1;
            else return 0;
        });
        System.out.println(Arrays.toString(arr));
    }
}

```
