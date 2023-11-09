# 备忘录模式
    保存一个对象的某个状态，以便在适当的时候恢复对象。
    备忘录模式属于行为型模式。
    场景：需要一个对象的内部状态能够在对象之外进行保存
#实现
```java
public class Memento {
    //需要保持的状态
    private String state;

    public Memento(String state) {
        this.state = state;
    }

    public void setState(String state) {
        this.state = state;
    }

    public String getState() {
        return state;
    }
}
```
```java
public class MementoManager {
    private List<Memento> list = new ArrayList<>();
    public void add(Memento state){
        list.add(state);
    }

    public Memento get(int index){
        return list.get(index);
    }
}

```
```java
public class Origin {
    private String state;

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
    }

    public Memento backUp(){
        return new Memento(state);
    }
    public void restore(Memento memento){
        state = memento.getState();
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        MementoManager mementoManager = new MementoManager();
        Origin origin = new Origin();
        origin.setState("version 1");
        origin.setState("version 2");
        origin.setState("version 3");
        origin.setState("tag");
        System.out.println("这是一个重要版本需要tag记录");
        mementoManager.add(origin.backUp());
        origin.setState("version 4");
        origin.setState("version 5");
        System.out.println("当前的版本"+origin.getState());
        System.out.println("需要恢复tag版本");
        origin.restore(mementoManager.get(0));
        System.out.println("当前的版本"+origin.getState());
    }
}

```