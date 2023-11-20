# java-注解

- 注解
  - Java 注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。

- 注解的场景

  - 生成文档，通过代码里标识的元数据生成javadoc文档。

  - 编译检查，通过代码里标识的元数据让编译器在编译期间进行检查验证。

  - 编译时动态处理，编译时通过代码里标识的元数据动态处理，例如动态生成代码。

  - 运行时动态处理，运行时通过代码里标识的元数据动态处理，例如使用反射注入实例

- 注解的分类

  - **Java自带的标准注解**，

  - @Override 标明重写某个方法、

  - @Deprecated 分别用于标明某个类或方法过时

  - @SuppressWarnings，、标明要忽略的警告，用这些注解标明后编译器就会进行检查。

- **元注解**，元注解是用于定义注解的注解，包括@Retention、@Target、@Inherited、@Documented，

  - @Retention用于标明注解被保留的阶段，

  - @Target用于标明注解使用的范围，

  - @Inherited用于标明注解可继承，

  - @Documented用于标明是否生成javadoc文档。

- **自定义注解**，可以根据自己的需求定义注解，并可用元注解对自定义注解进行注解。

  - Java 1.5开始自带的标准注解，

  - @Override：表示当前的方法定义将覆盖父类中的方法

  - @Deprecated：表示代码被弃用，如果使用了被@Deprecated注解的代码则编译器将发出警告

  - @SuppressWarnings：表示关闭编译器警告信息

- 元注解：

  - @Target，

  - @Retention，

  - @Documented，

  - @Inherited, 

- 在JDK 1.8中提供了两个元注解 @Repeatable和@Native。

  - @Repeatable,指向存储注解Authoritie

  - @Native 注解修饰成员变量，则表示这个变量可以被本地代码引用，常常被代码生成工具使用。

- Spring提供的注解

  - @AliasFor
    - @AliasFor是Spring框架的一个注解，用于声明注解属性的别名。使元注解有了继承的形态

  - @RestController

  - 互为别名

  - @RequestMapping

  - 组合注解

- @SpringBootApplication

  - 注解与反射接口

  - 定义注解后，如何获取注解中的内容呢？反射包java.lang.reflect下的AnnotatedElement接口提供这些方法。这里注意：只有注解被定义为RUNTIME后，该注解才能是运行时可见，当class文件被装载时被保存在class文件中的Annotation才会被虚拟机读取。