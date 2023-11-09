# 责任链模式
    将有请求的处理者通过前一对象记住其下一个对象的引用而连成一条链；当有请求发生时，可将请求沿着这条链传递，直到有对象处理它为止
    属于行为型设计模式
    场景:请求的发送者和请求的处理者解耦 SpringSec源码
# 实现
```java
public abstract class Log {
   public static final String INFO = "0";
   public static final String DEBUG = "1";
   public static final String WARN = "2";
   public static final String ERROR = "-1";
   abstract void log(String level,String message);
   abstract void setNextLog(Log log);
}
```
```java
public class InfoLog extends Log{
    private Log nextLog;
    @Override
    void log(String level, String message) {
        if((Log.INFO.equals(level))){
            System.out.println("日志级别INFO");
            System.out.println("日志内容"+message);
        }
        nextLog.log(level,message);
    }

    @Override
    void setNextLog(Log log) {
        this.nextLog =log;
    }
}
```
```java
public class Debug extends Log{
    private Log nextLog;

    @Override
    void log(String level, String message) {
        if(level.equals(Log.DEBUG)){
            System.out.println("日志级别Debug");
            System.out.println("日志内容"+message);
        }else {
            nextLog.log(level, message);
        }
    }

    @Override
    void setNextLog(Log log) {
        this.nextLog=log;
    }
}
```
```java
public class Warn extends Log{
    private Log nextLog;

    @Override
    void log(String level, String message) {
        if(level.equals(Log.WARN)){
            System.out.println("日志级别WARN");
            System.out.println("日志内容"+message);
        }else {
            nextLog.log(level, message);
        }
    }

    @Override
    void setNextLog(Log log) {
        this.nextLog=log;
    }
}
```
```java
public class Error extends Log{
    private Log nextLog;

    @Override
    void log(String level, String message) {
        if(level.equals(Log.ERROR)){
            System.out.println("日志级别ERROR");
            System.out.println("日志内容"+message);
        }else {
            nextLog.log(level, message);
        }
    }

    @Override
    void setNextLog(Log log) {
        this.nextLog=log;
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("==================责任链设计模式");
        InfoLog infoLog = new InfoLog();
        Debug debug = new Debug();
        Error error = new Error();
        Warn warn = new Warn();
        infoLog.setNextLog(debug);
        debug.setNextLog(error);
        error.setNextLog(warn);
        warn.setNextLog(infoLog);

        infoLog.log(Log.DEBUG, "调试中");
        System.out.println("==================");
        infoLog.log(Log.ERROR, "出现错误");
        System.out.println("==================");
        infoLog.log(Log.WARN, "出现警告啦");
    }
}
```