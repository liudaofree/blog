# JVM的类加载机制

##### 一、类的加载流程

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/a59583c8-26da-4507-9004-013e0537ffe7/resources/3r8WroExbbDO6VSi26JOVeffdMTOkOYcAAuQR7rmOow.svg?token=W._3Hl739Fq9XUsJxZx_0eQWxQBVlHI6pDJfHqF54UjGM49tfFlVfJV0qMNbvu1JM)

##### 二、类的声明周期

- 加载

- jvm外部的class文件加载jvm中，将类的数据映射到jvm方法区中，生成对应的数据结构，同时在堆中生成该类的唯一java.lang.class对象 

- 验证

- 验证class 文件的魔数 版本 语法 引用之类的

- 准备

- 开辟类变量的内存 赋值为类型的默认值

- 解析

- 将类中的符号饮用转化为直接应用 就是将字面量转换为指针或者内存地址

- 初始化

- 为类变量进行真正的赋值

- 使用new class.forname 访问静态变量都会初始化

- 使用

- 类访问方法区中的数据结构

- 卸载

- 程序正常结束

##### 三、类加载器

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/a59583c8-26da-4507-9004-013e0537ffe7/resources/kB9v1JPjpI5XwgMyjLB8TNidLa2_72BOQDzZo5XrEwQ.svg?token=W._3Hl739Fq9XUsJxZx_0eQWxQBVlHI6pDJfHqF54UjGM49tfFlVfJV0qMNbvu1JM)

- 启动类加载器 (BootStrapClassLoader):

- 负责加载存放在JDK\jre\lib(JDK代表JDK的安装目录，下同)下，或被-Xbootclasspath参数指定的路径中的，并且能被虚拟机识别的类库(如rt.jar，所有的java.*开头的类均被Bootstrap ClassLoader加载)。启动类加载器是无法被Java程序直接引用的。

- 扩展类加载器 (ExtClassLoader):

- 该加载器由sun.misc.Launcher$ExtClassLoader实现，它负责加载JDK\jre\lib\ext目录中，或者由java.ext.dirs系统变量指定的路径中的所有类库(如javax.*开头的类)，开发者可以直接使用扩展类加载器。

- 应用程序类加载器(AppClassLoader):

- 该类加载器由sun.misc.Launcher$AppClassLoader来实现，它负责加载用户类路径(ClassPath)所指定的类，开发者可以直接使用该类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。

- 自定义类加载器(User classLoader):

- 在执行非置信代码之前，自动验证数字签名。

- 动态地创建符合用户特定需要的定制化构建类。

- 从特定的场所取得java class，例如数据库中和网络中。

- jvm：

- C++实现的启动类加载器 在jvm内部

- 由java实现，继承自classLoader的类加载器

- 程序员：

- 启动类加载器 Bootstrap ClassLoader

- 加载jre包下的类

- 扩展类加载器

- 加载jre/ext包下类

- app加载器

- 加载当前类

##### 四、类加载的方式

- jvm 加载

- class.forname

- classLoader

- 区别

- Class.forName(): 将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块；

- ClassLoader.loadClass(): 只干一件事情，就是将.class文件加载到jvm中，不会执行static中的内容,只有在newInstance才会去执行static块。

- Class.forName(name, initialize, loader)带参函数也可控制是否加载static块。并且只有调用了newInstance()方法采用调用构造函数，创建类的对象 。

##### 五、jvm类加载机制

- 全盘负责：

- 当一个类加载器负责加载某个Class时，该Class所依赖的和引用的其他Class也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入

- 父类委托

- 先让父类加载器试图加载该类，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类

- 缓存

- 缓存机制将会保证所有加载过的Class都会被缓存，当程序中需要使用某个Class时，类加载器先从缓存区寻找该Class，只有缓存区不存在，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓存区。这就是为什么修改了Class后，必须重启JVM，程序的修改才会生效

- 双亲委派

- 如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。

- 具体流程

- AppclassLoader加载一个类时，它不会首先加载这个类而是将这个类的加载交给它的父类 extClassLoader。只有当extClassLoader 加载失败时，才会自己开始加载这个类

- extClassLoader 加载一个类时，它也不会首先加载这个类而是将这个类的加载交给它的父类bootStrapClassLoader。只有当bootStrapClassLoader 加载失败时，才会自己开始加载这个类。

- bootStrapClassLoader 加载这个类时会从$JAVA_HOME/jre/lib里未查找到该class)，会使用ExtClassLoader来尝试加载。

- 优势

- 系统类防止内存中出现多份同样的字节码

- 保证Java程序安全稳定运行