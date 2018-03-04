### Java抽象类  

抽象方法，是指没有方法体的方法，同时抽象方法还必须使用关键字abstract做修饰。
而拥有抽象方法的类就是抽象类，抽象类要使用abstract关键字声明。

抽象类的使用原则如下  

* 抽象方法必须为public或者protected（因为如果为private，则不能被子类继承，子类便无法实现该方法），缺省情况下默认为public；  
* 抽象类不能直接实例化，需要依靠子类采用向上转型的方式处理；  
* 抽象类必须有子类，使用extends继承，一个子类只能继承一个抽象类；  
* 子类（如果不是抽象类）则必须覆写抽象类之中的全部抽象方法（如果子类没有实现父类的抽象方法，则必须将子类也定义为为abstract类。）；


### Java面试题

```
public class Main {


    static  int arr[] = new int[10];
    public static void main(String[] bbbbbbbbbbb) {

       System.out.println(arr[0]);
    }
}

能编译通过，输出0
```