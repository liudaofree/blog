# 代理模式
    为其他对象提供一种代理，用以控制对这个对象的访问。
    场景：想做访问一些类时，做一些控制
# 静态代理
    被代理对象与代理对象一起实现相同的接口或者继承父类
```java
public interface Account {
    void pay();
}
```
```java
public class WxPay implements Account {
    @Override
    public void pay() {
        System.out.println("wx支付");
    }
}
```
```java
public class WxPayProxy implements Account {
    private WxPay wxPay;

    public WxPayProxy(WxPay wxPay) {
        this.wxPay = wxPay;
    }

    private void check(){
        System.out.println("支付前检查");
    }
    private void afterCheck(){
        System.out.println("支付后检查");
    }
    @Override
    public void pay() {
        check();
        wxPay.pay();
        afterCheck();
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("===================代理模式");
        Account wxPayProxy = new WxPayProxy(new WxPay());
        wxPayProxy.pay();
    }
}

```
# jdk动态代理
    不需要实现接口，通过JDK的api 动态的在内存中构建代理对象
```java
public interface FileScan {
    void load();
}
```
```java
public class QuickFileScan implements FileScan{
    @Override
    public void load() {
        System.out.println("快速扫描文件");
    }
}
```
```java
public class DynamicProxy {
    public Object createProxy(Object obj){
        ClassLoader loader = obj.getClass().getClassLoader();
        Class<?>[] interfaces = obj.getClass().getInterfaces();
        return Proxy.newProxyInstance(loader, interfaces, (proxy, method, args)-> {
            checkFile();
            Object invoke = method.invoke(obj, args);
            closeResource();
            return invoke;
        });
    }
    private void checkFile(){
        System.out.println("检查文件大小");
    }
    private void closeResource(){
        System.out.println("关闭各种资源");
    }
}

```
```java
public class Client {
    public static void main(String[] args) {
        DynamicProxy proxy = new DynamicProxy();
        FileScan fileScan = (FileScan)proxy.createProxy(new QuickFileScan());
        fileScan.load();
    }
}
```
# cglib代理
    cglib代理也叫作子类代理，在内存中构建一个子类对象从而实现对目标对象的功能扩展
```java
public class Worker {
    int work(int num){
        return num;
    }
}
```
```java
public class ProxyFactory implements MethodInterceptor {

    private Object obj;

    public ProxyFactory(Object obj) {
        this.obj = obj;
    }

    public Object getProxyInstance(){
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(obj.getClass());
        enhancer.setCallback(this);
        return enhancer.create();
    }

    @Override
    public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        System.out.println("cglib动态代理");
        Object returnValue = method.invoke(obj, args);
        System.out.println("工作时长是："+returnValue);
        return returnValue;
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        ProxyFactory factory = new ProxyFactory(new Worker());
        Worker proxyInstance = (Worker)factory.getProxyInstance();
        proxyInstance.work(10);
    }
}
```
# 代理模式与装饰器模式的思考
    代理模式侧重的对象是某一个功能的调用和控制
    装饰器模式的本身是对象的功能进行一个增强，而不考虑外部的调用过程
    设计模式本身是一种为了提升代码的可扩展性的经验总结，不需要生搬硬套，照本宣科。