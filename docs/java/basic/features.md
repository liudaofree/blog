# Java1.8 新特性

#### 概念

- 函数式编程是对行为的抽象，核心思想，使用不可变值和函数，函数对一个值进行处理，映射成另一个值。

#### 要素

##### lambda表达式

- stream()和parallelStream()

- stream：顺序执行

- parallelStream：并行执行

   ```java
       private static void stream(){
           long start1 = System.currentTimeMillis();
           int[] ints = IntStream.range(0, 1000000).filter(p -> p % 3 == 0).toArray();
           long st1 = System.currentTimeMillis();
           int[] int2 = IntStream.range(0, 1000000).parallel().filter(p -> p % 3 == 0).toArray();
           long st2 = System.currentTimeMillis();
           System.out.println("parallelStream:" + (st2-st1));
           System.out.println("Stream:" + (st1-start1));
       }
   ```
   
   

- 常用方法
  - filter()

  - findAny(),findFirst()

  - sort

  - forEach

  - map(),reduce()

  - flatMap() 将多个Stream连接成一个Stream

  - collect(Collectors.toList())


##### FunctionalInterface	

- 概念

- 函数式接口

- 被这个注解注释的接口只能有一个抽象方法，有两种例外

- 第一是接口允许有实现的方法，这种实现的方法是通过default关键字来标记的。

- 第二是如果声明的方法和java.lang.Object中的某个方法一样，他可以不当作为实现的方法，不违背，一个被它注解的接口只能有一个抽象接口

- 如果一个类型被这个注解修饰，那么这个类型必须满足如下条件

- 这个类型必须是一个interface，而不是其它的注解类型，枚举enum，或者类class

- 这个类型必须满足function interface 的所有要求，如果包含两个抽象方法的接口增加这个注解，会有编译错误。

- 自定义函数接口

```java
/**
* 自定义函数式接口
**/
@FunctionalInterface
public interface MyDesignInterface {
    void test();
}
public class FunctionnalInterFaceMain {
    public static void main(String[] args) {
        MyDesignInterface designInterface = ()->{
            System.out.println("hello void");
        };
        designInterface.test();
    }
}
```



##### 内置函数接口

- 消费型接口：有参数无返回值得抽象方法
- Consumer<T> void accept(T t);

```java
    public static void innerConsumer(){
        Consumer consumer = t->{
            System.out.println(t);
        };
        consumer.accept("hello");
    }
```



- 供给型接口：无参数有返回值的抽象方法
- Supplier<T> get（)

```java
    public static void innerSupplier(){
        Supplier supplier = ()->{
            return "myword";
        };
        System.out.println("supplier = " + supplier.get());
    }
```



- 断定型接口：有参数，返回值类型是固定的boolean
- Predicate<T> boolean test(T t);

```java
    public static void innerPredict(){
        Predicate<Integer> pre = t->{
            return t%2==0;
        };
        System.out.println("pre.test(1) = " + pre.test(1));
        System.out.println("pre.test(2) = " + pre.test(2));
    }
```



- 函数型接口：有参数 有返回值
- Function<T,R> R apply(T t)

```java
    public static void innerFunction(){
        Function<Integer,String> function = t->{
            return "我是返回值"+t;
        };
        System.out.println(function.apply(1101));
    }
```



##### Optional类

- 构建

- of() 

- 为非null值提供一个Optional实例,

- ofNullable()

- 为指定的值创建一个Optional，如果指定的值为null，则返回一个空的Optional。 

```java
        // 报错
        Optional<Object> o = Optional.of(null);
        // 不报错
        Optional<Object> o1 = Optional.ofNullable(null);
```



- 取值

- isPresent()

- 如果存在值返回true，否转返回false

- get()

- 如果Optional有值则将其返回，否则抛出NoSuchElementException。

- ifPresent()

- 如果Optional有值则将其返回，否则抛出NoSuchElementException。

- orElse()

- 如果有值则将其返回，否则返回指定的其它值。

- orElseGet()

- orElseGet与orElse方法类似，区别在于得到的默认值。orElse方法将传入的字符串作为默认值，orElseGet方法可以接受Supplier接口的实现用来生成默认值。

- orElseThrow()

- 如果有值则将其返回，否则抛出supplier接口创建的异常。

- 操作

- map（）

- 如果有值，则对其执行调用mapping函数得到返回值。如果返回值不为null，则创建包含mapping返回值的Optional作为map方法返回值，否则返回空Optional。

- flatMap

- 如果有值，为其执行mapping函数返回Optional类型返回值，否则返回空Optional。flatMap与map(Funtion)方法类似，区别在于flatMap中的mapper返回值必须是Optional。调用结束时，flatMap不会对结果用Optional封装。

- filter

- 如果有值并且满足断言条件返回包含该值的Optional，否则返回空Optional。

```java
public static void getOptional(){
        Optional<String> o1 = Optional.ofNullable("hello");
        Optional<String> o2 = Optional.ofNullable(null);
        String s = o1.get();
        System.out.println("s = " + s);
        boolean present = o1.isPresent();
        System.out.println("present = " + present);
        o1.ifPresent(t-> System.out.println(t));
        String i_am_empty = o2.orElse("i am empty");
        System.out.println(i_am_empty);
        String s1 = o2.orElseGet(() -> "I am empty");
        System.out.println("s1 = " + s1);
        Optional<String> s2 = o1.map(v -> v.toUpperCase());
        System.out.println("s2 = " + s2);
        Optional<String> s3 = o1.flatMap(v -> Optional.of(v.toUpperCase()));
        System.out.println("s3 = " + s3);
    }

```



##### 默认方法

- 概念

- 就是接口可以有实现方法，而且不需要实现类去实现其方法。只需在方法名前面加个default关键字即可。

- 接口和抽象类的区别

| 相同点                                                       | 不同点                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 都是抽象类型                                                 | 都可以不需要实现类或者继承者去实现所有方法，(以前不行，现在接口中默认方法不需要实现者实现) |
| 都可以有实现方法(以前接口不行)                               | 抽象类和接口所反映出的设计理念不同。其实抽象类表示的是”is-a”关系，接口表示的是”like-a”关系 |
| 都可以不需要实现类或者继承者去实现所有方法，(以前不行，现在接口中默认方法不需要实现者实现) | 接口中定义的变量默认是public static final 型，且必须给其初值，所以实现类中不能改变其值；抽象类中的变量默认是 friendly 型，其值可以在子类中重新定义，也可以重新赋值。 |

```java
    public static void main(String[] args) {
        MyDesignInterface my = new MyDesignInterface() {

        };
        my.hello2();
    }
public interface MyDesignInterface {
    default void hello(){
        System.out.println("hello world");
    }
    default void hello2(){
        System.out.println("hello world");
    }
}
```



#### Q&A