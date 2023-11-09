# 单例模式
    定义：某个类在系统中只能存在一个对象实例，并且该类只提供一个取得其对象实例的方法(静态方法)
    场景: 需要频繁的进行创建和销毁的对象，创建对象耗时过多。但又会经常用到的对象比如数据源 工厂等
##  1.饿汉式(静态常量)
```java
class SingleTest1{
    // 1.内部创建实例
    private final static SingleTest1 single1 = new SingleTest1();
    // 2.私有化构造方法
    private SingleTest1(){
    }
    // 3.提供对外访问
    public static SingleTest1 getInstance(){
        return single1;
    }
}
```
  方式可用，但没有懒加载的效果，可能会造成内存浪费
##  2.饿汉式(静态代码块)
```java
class SingleTest2{
    // 使用静态代码块的方式创建
    static {
        single2 = new SingleTest2();
    }
    private final static SingleTest2 single2;
    private SingleTest2(){
    }
    public static SingleTest2 getInstance(){
        return single2;
    }
}
```
方式可用，但没有懒加载的效果，可能会造成内存浪费
##  3.懒汉式(线程不安全)
```java
class SingleTest3{
    private SingleTest3(){
    }
    private static SingleTest3 single3;

    // 需要使用实例的时候才开始创建 懒加载
    public static SingleTest3 getInstance(){
        // 此处线程不安全
        if(single3==null){
            single3=new SingleTest3();
        }
        return single3;
    }
}
```
在实际开发中不要使用 因为线程不安全 无法保证单例的效果
##  4.懒汉式(线程安全，同步方法)
```java
class SingleTest4{
    private SingleTest4(){
    }
    private static SingleTest4 single4;

    // 需要使用实例的时候才开始创建 懒加载 使用监视器锁 保证线程安全
    public static synchronized SingleTest4 getInstance(){
        // 此处线程不安全
        if(single4==null){
            single4=new SingleTest4();
        }
        return single4;
    }
}
```
解决了线程安全的问题 但是效率过低 因为多线程竞争的时候都会发生加锁和释放。而真正需要同步的只是创建对象的过程。
##  5.懒汉式(线程安全，同步代码块)
```java
class SingleTest5{
    private SingleTest5(){
    }
    private static SingleTest5 single5;

    // 需要使用实例的时候才开始创建 懒加载 使用监视器锁 保证线程安全
    public static  SingleTest5 getInstance(){
        // 此处线程不安全
        if(single5==null){
            // 此处使用类锁
            synchronized (SingleTest5.class) {
                single5 = new SingleTest5();
            }
        }
        return single5;
    }
}
```
线程不安全 在判断是否为空的时候，会同时判断成功。从进入方法，虽然synchronized锁住了创建的对象的过程，但是会有多个线程创建对象
##  6.双重检查
```java
class SingleTest6{
    private SingleTest6(){
    }
    // volatile 一个线程创建实例成功之后可以马上被感知到
    private static volatile SingleTest6 single6;

    // 需要使用实例的时候才开始创建 懒加载 使用监视器锁 保证线程安全
    public static  SingleTest6 getInstance(){
        // 此处线程可以提高判断的效率 不用每次都加锁之后判断
        if(single6==null){
            // 此处线程安全
            synchronized (SingleTest6.class) {
                if(single6==null) {
                    single6 = new SingleTest6();
                }
            }
        }
        return single6;
    }
}
```
类的创建相对比较复杂 但是可以稳固的保证线程安全和较高的效率
##  7.静态内部类
```java
class SingleTest7{
    private SingleTest7(){
    }
    ///静态内部类不会立即被装载
    private static class SingleTonInstance{
        private static  final SingleTest7 single7 = new SingleTest7();
    }
    /// 调用的时候被装载 但只会被装载一次 装载过程线程安全
    public static SingleTest7 getInstance(){
        return SingleTonInstance.single7;
    }
}
```
使用类加载的机制 保证了线程安全，也提供了懒加载的方式 节约内存
##  8.枚举
```java
enum SingleTest8{
    INSTANCE;
    private String a;

    public String getA() {
        return a;
    }

    public void setA(String a) {
        this.a = a;
    }

    public void sayHello(){
       
    }
}
```
线程安全 可以防止反序列化重新创建新的对象