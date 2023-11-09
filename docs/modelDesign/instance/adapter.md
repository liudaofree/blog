# 适配器模式
    将某个类的接口转换成客户端期望的另一个类的接口表示 主要目的是兼容接口 属于结构型的设计模式
## 实现
    这里通过实现低压电接口 将高压电转换为了低压电 对客户端而言 只是使用低压电的接口
```java
// 高压电接口
public interface HighVoltageInterface {
    Integer voltageHigh();
}

```
```java
// 高压电实现
public class HighVoltage implements HighVoltageInterface {
    public Integer voltageHigh(){
        System.out.println("高压电1500V");
        return 1500;
    }
}
```
```java
// 低压电接口
public interface LowVoltageInterface {
    Integer lowVoltage();
}
```
```java
public class LowVoltageAdapter implements LowVoltageInterface{
    private HighVoltageInterface highVoltage;


    public void setHighVoltage(HighVoltage highVoltage) {
        this.highVoltage = highVoltage;
    }

    @Override
    public Integer lowVoltage() {
        int power = 0;
        if(highVoltage!=null){
            power = highVoltage.voltageHigh()/10;
        }
        System.out.println("低压电" + power);
        return power;
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        LowVoltageAdapter lowVoltageAdapter = new LowVoltageAdapter();
        lowVoltageAdapter.setHighVoltage(new HighVoltage());
        lowVoltageAdapter.lowVoltage();
    }
}
```