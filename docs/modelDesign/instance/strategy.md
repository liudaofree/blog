# 策略模式
    类的行为或其算法可以在运行时更改。
    这种类型的设计模式属于行为型模式。
    场景：目标存在多种策略可供选择
# 实现
```java
public interface Algorithm {
     void method(Integer a,Integer b);
}
```
```java
public class CompareAlgorithm implements Algorithm {
    @Override
    public void method(Integer a, Integer b) {

        if(a.compareTo(b)>=0){
            System.out.println("两数中较大的是"+a);
        }
        System.out.println("两数中较大的是"+b);
    }
}
```
```java
public class AddAlgorithm implements Algorithm {
    @Override
    public void method(Integer a, Integer b) {
        System.out.println("两数的和是" + (a + b));
    }
}

```
```java
public class Context {
    private Algorithm algorithm;

    public Context(Algorithm algorithm) {
        this.algorithm = algorithm;
    }

    public void setAlgorithm(Algorithm algorithm) {
        this.algorithm = algorithm;
    }

    public void doStrategy(Integer a, Integer b){
        algorithm.method(a,b);
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("=================策略模式");
        System.out.println("比较a和b的大小===========");
        Algorithm compareAlgorithm = new CompareAlgorithm();
        Algorithm AddAlgorithm = new AddAlgorithm();
        Context context = new Context(compareAlgorithm);
        context.doStrategy(11,13);
        System.out.println("求a和b的和===========");
        context.setAlgorithm(AddAlgorithm);
        context.doStrategy(11,13);
        System.out.println("自定义算法====");
        context.setAlgorithm((a, b) -> {
            System.out.println("两数的差是"+(a-b));
        });
        context.doStrategy(11,13);
    }
}
```