# 桥接模式
    将实现与抽象放在两个不同的类层次中，是两个层次可以独立改变，属于结构型的设计模式
# 实现
    将car的行为和car的种类进行拆分 这样在扩展的时候可以进行独立的扩展
```java
/// car 的功能
public interface CarFunction {
    void charge();
    void driving();
}
```
```java
// car的种类抽象
public abstract class AbstractCar {
    private CarFunction carFunction;

    public AbstractCar(CarFunction carFunction) {
        this.carFunction = carFunction;
    }

    protected  void charge(){
        carFunction.charge();
    }

    protected  void driving(){
        carFunction.driving();
    }
}
```
```java
// car 功能的实现
public class ElecCarFunction implements CarFunction{
    @Override
    public void charge() {
        System.out.println("新能源充电中。。。。。");
    }

    @Override
    public void driving() {
        System.out.println("新能源行驶中。。。。。");
    }
}
```
```java
public class OilCarFunction implements CarFunction{
    @Override
    public void charge() {
        System.out.println("燃油汽车加注燃料中。。。。。");
    }

    @Override
    public void driving() {
        System.out.println("燃油汽车行驶中。。。。。");
    }
}
```
```java
// car 种类的扩展
public class SuvCar extends AbstractCar{
    public SuvCar(CarFunction carFunction) {
        super(carFunction);
    }

    @Override
    protected void charge() {
        System.out.println("Suv=========");
        super.charge();
    }

    @Override
    protected void driving() {
        System.out.println("Suv=========");
        super.driving();
    }
}
```
```java
public class SedanCar extends AbstractCar{
    public SedanCar(CarFunction carFunction) {
        super(carFunction);
    }

    @Override
    protected void charge() {
        System.out.println("轿车=========");
        super.charge();
    }

    @Override
    protected void driving() {
        System.out.println("轿车=========");
        super.driving();
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        SedanCar sedanCar = new SedanCar(new ElecCarFunction());
        sedanCar.charge();
        sedanCar.driving();
    }
}
```