# 中介者模式
    用一个中介对象来封装一系列的对象交互。中介可以使各个对象不需要显式的相互引用，从而使其松耦合。
    中介者模式属于行为型模式。
    场景：需要将多个类的交互，形成网状结构分离为星型结构
```java
public interface HouseMediator {
    void register(Colleague colleague);
    void remove(Colleague colleague);
    void handler(Colleague colleague);
}
```
```java
public class ConcreteMediator implements HouseMediator {
    private List<Colleague> list = new ArrayList<>();

    @Override
    public void register(Colleague colleague) {
        list.add(colleague);
        colleague.setMediator(this);
    }

    @Override
    public void remove(Colleague colleague) {
        list.remove(colleague);
    }

    @Override
    public void handler(Colleague colleague) {
        for (Colleague colleague1 : list) {
            if(!colleague1.equals(colleague)){
                colleague1.receive();
            }
        }
    }
}
```
```java
public abstract class Colleague {
    protected HouseMediator houseMediator;
    void setMediator(HouseMediator houseMediator){
        this.houseMediator =houseMediator;
    }
    abstract void send();
    abstract void receive();
}
```
```java
public class BuyerColleague extends Colleague{
    @Override
    void send() {
        System.out.println("买家发消息");
        houseMediator.handler(this);
    }

    @Override
    void receive() {
        System.out.println("我是买家，我收到消息");
    }
}
```
```java
public class SellerColleague extends Colleague{
    @Override
    void send() {
        System.out.println("卖家发消息");
        houseMediator.handler(this);
    }

    @Override
    void receive() {
        System.out.println("我是卖家，我收到消息");
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("===================中介者模式");
        Colleague c2 = new SellerColleague();
        BuyerColleague c1 = new BuyerColleague();
        HouseMediator mediator = new ConcreteMediator();
        mediator.register(c2);
        mediator.register(c1);
        c2.send();
    }
}
```