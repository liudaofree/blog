# 装饰者模式
    动态的将新功能附加到对象上，属于结构型设计模式
    场景:适用于在需要对类进行功能扩展的时候
# 实现
定义了车的接口
```java
public interface Car {
    void Driving();
}

```
传统车的实现
```java
public class TraditionCar implements Car{
    @Override
    public void Driving() {
        System.out.println("=====汽车开始行驶=====");
    }
}
```
对车的接口进行抽象装饰
```java
public abstract class CarDecorator implements Car {
    protected  Car car;

    public CarDecorator(Car car) {
        this.car = car;
    }

    @Override
    public void Driving() {
        car.Driving();
    }
}
```
智能车的升级
```java
public class AiCarDecorator extends CarDecorator{
    public AiCarDecorator(Car car) {
        super(car);
    }

    @Override
    public void Driving() {
        System.out.println("智能AI启动");
        car.Driving();
        System.out.println("智能AI辅助驾驶");
    }
}
```
智能车的升级
```java
public class Client {
    public static void main(String[] args) {
        CarDecorator aiCarDecorator = new AiCarDecorator(new TraditionCar());
        aiCarDecorator.Driving();
    }
}
```