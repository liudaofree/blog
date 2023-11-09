# 解释器模式
    提供了评估语言的语法或表达式的方式，它属于行为型模式。
    场景：用于符号处理引擎，Sql解析等。
# 实现
```java
public interface Expression {
    default String[] getResult(String info){
        return new String[0];
    }
     boolean interpret(String info);
}

```
```java
public class NoTerminalExpression implements Expression {
    private Expression company = null;
    private Expression product = null;

    public NoTerminalExpression(Expression company, Expression product) {
        this.company = company;
        this.product = product;
    }

    public boolean interpret(String info) {
        String[] result = getResult(info);
        return company.interpret(result[0]) && product.interpret(result[1]);
    }

    @Override
    public String[] getResult(String info) {
        String str[] = info.split("of");
        return str;
    }
}
```
```java
public class TerminalExpression implements Expression {
    private Set<String> set = new HashSet<>();

    public TerminalExpression(String[] data) {
        for (int i = 0; i < data.length; i++) {
            set.add(data[i]);
        }
    }

    public boolean interpret(String info) {
        if (set.contains(info)) {
            return true;
        }
        return false;
    }
}
```
```java
class Context {
    private String[] company = {" TX", "Ali", "Byt"};
    private String[] product = {"Chat", "Shop", "ShortVideo"};
    private Expression check;

    public Context() {
        Expression companys = new TerminalExpression(company);
        Expression products = new TerminalExpression(product);
        check = new NoTerminalExpression(companys, products);
    }

    public void checkProduct(String info) {
        boolean ok = check.interpret(info);
        String[] str = check.getResult(info);
        if (ok) System.out.println("该产品" + str[0]+ "是的"+ str[1]+"顶级产品");
        else System.out.println(str[0] + "不是"+str[1]+"顶级产品");
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("======================解释器模式");
        Context con = new Context();
        con.checkProduct("ShopofAli");
        con.checkProduct("CarofTx");
    }
}
```