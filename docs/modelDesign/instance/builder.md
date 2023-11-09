# 生成器模式
[[toc]]
## 1.定义
    将类的构造过程进行封装，允许用户按步骤创建对象。
    场景：在需要灵活使用参数构建对象的时候。 
## 2.建造者模式的角色
    - Product：具体的产品对象
    - Builder：定义构建Product需要的接口
    - ConcreteBuilder：实现Builder的接口
    - Director：构建一个使用Builder的对象，创建一个复杂的对象。
## 3.示例
    在较为简单的类中，可以使用lombok的@Builder注解，这样会自动生成静态内部生成器类。
```java
@Builder
public class RobotV2 {
    private String code;
    private String appearance;
    private String power;
}
```
使用反编译后的结果是
```java
public class RobotV2 {
    private String code;
    private String appearance;
    private String power;

    RobotV2(final String code, final String appearance, final String power) {
        this.code = code;
        this.appearance = appearance;
        this.power = power;
    }

    public static RobotV2.RobotV2Builder builder() {
        return new RobotV2.RobotV2Builder();
    }

    // 内部生成器类
    public static class RobotV2Builder {
        private String code;
        private String appearance;
        private String power;

        RobotV2Builder() {
        }

        public RobotV2.RobotV2Builder code(final String code) {
            this.code = code;
            return this;
        }

        public RobotV2.RobotV2Builder appearance(final String appearance) {
            this.appearance = appearance;
            return this;
        }

        public RobotV2.RobotV2Builder power(final String power) {
            this.power = power;
            return this;
        }

        public RobotV2 build() {
            return new RobotV2(this.code, this.appearance, this.power);
        }

        public String toString() {
            return "RobotV2.RobotV2Builder(code=" + this.code + ", appearance=" + this.appearance + ", power=" + this.power + ")";
        }
    }
}

```