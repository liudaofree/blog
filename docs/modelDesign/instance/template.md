# 模板方法模式
    在一个抽象类中公开定义了它的方法的模板，子类可以按需要重写方法，但方法的调用过程是按照抽象类中的定义方式进行的。
    这种设计方式称为行为型设计模式。
    场景：有多个子类的调用逻辑方法是想通的。
# 实现
```java
public abstract class Game {
    abstract void downloadResource();
    abstract void roleCheck();
    abstract void onPlay();
    abstract void endPlay();

    final void  playGame(){
        downloadResource();
        roleCheck();
        onPlay();
        endPlay();
    }
}
```
```java
public class Moba extends Game {
    @Override
    void downloadResource() {
        System.out.println("下载Moba游戏资源");
    }

    @Override
    void roleCheck() {
        System.out.println("是否允许玩Moba游戏");
    }

    @Override
    void onPlay() {
        System.out.println("开始玩Moba游戏");
    }

    @Override
    void endPlay() {
        System.out.println("关闭Moba游戏");
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("模板方法");
        Game game = new Moba();
        game.playGame();
    }
}
```