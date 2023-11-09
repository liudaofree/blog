# 状态模式
    对象是有状态的，不同的状态对象具有不同的判断逻辑，允许状态对象在其内部状态发生改变时改变其行为。
    归属于行为设计模式
    场景：对象的行为可以抽象成不同的状态
# 实现
```java
public abstract class State {
    public abstract void Handle(Context context);
}
```
```java
public class ConcreteUnPayState extends State{
    @Override
    public void Handle(Context context) {
        System.out.println("当前状态是未支付");
        context.setState(new ConcretePayState());
    }
}
```
```java
public class ConcretePayState extends State{
    @Override
    public void Handle(Context context) {
        System.out.println("当前状态是已支付");
        context.setState(new ConcreteCancelState());
    }
}
```
```java
public class ConcreteCancelState extends State{
    @Override
    public void Handle(Context context) {
        System.out.println("当前状态是已取消");
    }
}
```
```java
public class Context {
    private State state;

    public Context() {
        this.state = new ConcreteUnPayState();
    }

    public State getState() {
        return state;
    }

    public void setState(State state) {
        this.state = state;
    }
    public void handle(){
        state.Handle(this);
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("========================状态模式");
        Context context = new Context();
        context.handle();
        System.out.println("用户发起支付");
        context.handle();
        System.out.println("用户取消订单");
        context.handle();
    }
}

```