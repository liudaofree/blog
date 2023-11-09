# 访问者模式
    封装一些作用于某种数据结构的各种元素的操作，在不改变数据结构的前提下定义新的操作
    解决数据结果和操作耦合性的问题 属于行为型设计模式
    场景：需要对一个对象结构中的对象进行很多种不同的操作 操作之间彼此无关
# 实例
```java
//访问者
public interface MemberVisitor {
    void visit(AccountNormal account);
    void visit(AccountVip account);
}
```
```java
public class MemberVisitorImpl implements MemberVisitor {
    @Override
    public void visit(AccountNormal account) {
        account.sayHello();
    }

    @Override
    public void visit(AccountVip account) {
        account.sayVip();
    }
}
```
```java
public interface Account {
    void accept(MemberVisitor memberVisitor);
}
```
```java
public class AccountNormal implements Account{
    @Override
    public void accept(MemberVisitor memberVisitor) {
        memberVisitor.visit(this);
    }
    public void sayHello(){
        System.out.println("欢迎您访问");
    }
}
```
```java
public class AccountVip implements Account{
    @Override
    public void accept(MemberVisitor memberVisitor) {
        memberVisitor.visit(this);
    }
    public void sayVip(){
        System.out.println("欢迎vip用户的访问");
    }
}

```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("=============================访问者模式");
        Account accountNormal = new AccountNormal();
        Account accountVip = new AccountVip();
        MemberVisitor memberVisitor = new MemberVisitorImpl();
        accountNormal.accept(memberVisitor);
        accountVip.accept(memberVisitor);
    }
}
```