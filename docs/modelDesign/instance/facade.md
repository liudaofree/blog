# 外观模式
    外观模式也叫过程模式 外观模式为子系统的一组接口提供一个一致的界面，此模式定义了一个高层接口，屏蔽内部子系统的实现细节
# 实现
```java
public interface Chef {
    void cook();
}

```
```java
public class Cooking {
    public static Cooking cooking = new Cooking();
    private Cooking(){

    }
    public void pre(){
        System.out.println("点火");
    }
    public void handle(){
        System.out.println("烹饪");
    }
    public void after(){
        System.out.println("关火");
    }
}

```
```java
public class Ingredient {
    public static Ingredient ingredient = new Ingredient();
    private Ingredient(){

    }
    public void pre(){
        System.out.println("准备食材");
    }
    public void handle(){
        System.out.println("处理食材");
    }
}
```
```java
public class TopChef implements Chef{

    /**
     * 隐藏内部的实现细节，对外表现就只有烹饪完成这一个结果
     */
    @Override
    public void cook() {
        Cooking cooking = Cooking.cooking;
        Ingredient ingredient = Ingredient.ingredient;
        ingredient.pre();
        ingredient.handle();

        cooking.pre();
        cooking.handle();
        cooking.after();
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("=======================外观模式=================");
            Chef chef = new TopChef();
            chef.cook();
    }
}
```