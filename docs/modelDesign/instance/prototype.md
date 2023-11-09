# 原型模式
    用一个已经创建的实例作为原型，通过复制该原型对象来创建一个和原型相同或相似的新对象。用于创建重复的对象，同时又能保证性能
    原型模式是一种创建型设计模式，
```java
public class Animal implements Cloneable {
    private int age;
    private Boolean sex;
    private Animal sub;

    //浅拷贝
    @Override
    protected Object clone() {
        try {
            return super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return null;
    }

    public Animal(int age, Boolean sex) {
        this.age = age;
        this.sex = sex;
    }

    public Animal getSub() {
        return sub;
    }

    public void setSub(Animal sub) {
        this.sub = sub;
    }

    @Override
    public String toString() {
        return "Animal{" +
                "age=" + age +
                ", sex=" + sex +
                ", sub=" + sub +
                '}';
    }
}
```

    通过实现Cloneable接口可以实现类的克隆 但这里使用的是类的浅拷贝

### 1.1浅拷贝

    在对象的复制过程中，对于数据类型是引用类型的成员变量，进行引用传递，将该成员变量的引用值复制一份给新的对象。

### 1.2深拷贝

    在对象的复制过程中，对于数据类型是引用类型的成员变量，会为引用数据类型的成员变量申请内存空间，并复制每个引用数据类型成员变量所引用的对象。

### 1.3深拷贝的实现

    主要是指针对引用对象的创建和复制，这里使用序列化和反序列化的方式提供一种思考方向
    注意transient 关键字会使一些属性不会被序列化。
```java
    public Animal deepClone() {
        Animal animal = null;
        try (
                ByteArrayOutputStream byteArray = new ByteArrayOutputStream();
                ObjectOutputStream outputStream = new ObjectOutputStream(byteArray)) {
            // 序列化
            outputStream.writeObject(this);
            try (
                    ByteArrayInputStream byteArrayIn = new ByteArrayInputStream(byteArray.toByteArray());
                    ObjectInputStream inputStream = new ObjectInputStream(byteArrayIn)) {
                // 反序列化
                animal = (Animal) inputStream.readObject();
                return animal;
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return animal;
    }
```