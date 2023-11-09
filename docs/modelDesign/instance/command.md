# 命令模式
    请求以命令的形式包裹在对象中，并传递给调用对象。
    这种设计方式称为行为型设计模式。
    场景：命令调度和行为实现需要进行解耦
# 实现
```java
public interface Order {
    // 命令接口需要执行的命令
    void execute();
}
```
```java
public class LogisticsOrder implements Order{
    private Goods goods;

    public LogisticsOrder(Goods goods) {
        this.goods = goods;
    }

    @Override
    public void execute() {
        goods.logisticsOrder();
    }
}

```
```java
public class PurchaseOrder implements Order {
    private Goods goods;

    public PurchaseOrder(Goods goods) {
        this.goods = goods;
    }

    @Override
    public void execute() {
        goods.addOrder();
    }
}
```
```java
public class Goods {
    public void addOrder(){
        System.out.println("商品下单了");
    }
    public void logisticsOrder(){
        System.out.println("商品进入物流了");
    }
    public void cancelOrder(){
        System.out.println("商品取消了");
    }
}
```
```java
public class Invoker {
    private List<Order> orderList = new ArrayList<>();

    public void addOrder(Order order){
        orderList.add(order);
    }

    public void execute(){
        if(!orderList.isEmpty()){
            for (Order order : orderList) {
                order.execute();
            }
        }
    }

}
```
```java
public class Command {
    public static void main(String[] args) {
        System.out.println("=================命令模式");
        //命令执行对象
        Goods goods = new Goods();
        // 命令请求作为对象
        Order logisticsOrder = new LogisticsOrder(goods);
        Order purchaseOrder = new PurchaseOrder(goods);
        //命令对象的入口
        Invoker invoker = new Invoker();
        invoker.addOrder(purchaseOrder);
        invoker.addOrder(logisticsOrder);
        invoker.execute();

    }
}
```