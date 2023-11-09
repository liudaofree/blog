# 观察者模式
    当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知依赖它的对象。
    观察者模式属于行为型模式。
    场景：一个对象必须通知其他对象，而并不知道这些对象是谁。
# 实现
```java
public class Subject {
    private List<Observer> observerList = new ArrayList<>();

    private String message;

    public void setMessage(String message) {
        this.message = message;
        notifyAllInstance();
    }

    public String getMessage() {
        return message;
    }

    void register(Observer observer){
        observerList.add(observer);
    }
    void unRegister(Observer observer){
        observerList.remove(observer);
    }
    private void notifyAllInstance(){
        for (Observer observer : observerList) {
            observer.update();
        }
    }

}
```
```java
public interface Observer {
    void update();
}

```
```java
public class WxMessage implements Observer{
    private Subject subject;

    public WxMessage(Subject subject) {
        this.subject = subject;
    }

    @Override
    public void update() {
        System.out.println("最新的消息是"+subject.getMessage());
    }
}
```
```java
public class SmsMessage implements Observer{
    private Subject subject;

    public SmsMessage(Subject subject) {
        this.subject = subject;
    }

    @Override
    public void update() {
        System.out.println("最新的短信是"+subject.getMessage());
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("============================观察者模式");
        Subject subject = new Subject();
        SmsMessage smsMessage = new SmsMessage(subject);
        WxMessage wxMessage = new WxMessage(subject);
        subject.register(smsMessage);
        subject.register(wxMessage);

        subject.setMessage("prepare");
        System.out.println("======================");
        subject.setMessage("doing");
        System.out.println("======================");
        subject.unRegister(smsMessage);
        subject.setMessage("end");

    }
}
```