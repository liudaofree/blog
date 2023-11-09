# 组合模式
    是用于把一组相似的对象当作一个单一的对象。将对象组合成树形结构，用来表示部分以及整体层次。
    这种类型的设计模式属于结构型模式。
    场景：这样的结构适用于树形组织类的需求
# 实现
```java
public abstract class Organization {
    /**
     * 组织名称
     */
    private String organizationName;


    protected void add(Organization organization) {
        throw new UnsupportedOperationException();
    }

    protected void remove(Organization organization) {
        throw new UnsupportedOperationException();
    }
    abstract void print();
}
```
```java
public class Group extends Organization{
    private String name;

    public Group(String name) {
        this.name = name;
    }
    private final List<Organization> organizations = new ArrayList<>();
    @Override
    protected void add(Organization organization) {
        organizations.add(organization);
    }

    @Override
    protected void remove(Organization organization) {
        organizations.remove(organization);
    }

    @Override
    void print() {
        System.out.println("-"+this.name);
        if(!organizations.isEmpty()){
            for (Organization organization : organizations) {
                organization.print();
            }
        }
    }
}
```

```java
public class Company extends Organization{
    private String name;

    public Company(String name) {
        this.name = name;
    }
    private final List<Organization> organizations = new ArrayList<>();
    @Override
    protected void add(Organization organization) {
        organizations.add(organization);
    }

    @Override
    protected void remove(Organization organization) {
        organizations.remove(organization);
    }

    @Override
    void print() {
        System.out.println("--"+this.name);
        if(!organizations.isEmpty()){
            for (Organization organization : organizations) {
                organization.print();
            }
        }
    }
}

```
```java
public class Department extends Organization {
    private String name;

    public Department(String name) {
        this.name = name;
    }

    @Override
    void print() {
        System.out.println("----"+this.name);
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        Organization development = new Department("研发部");
        Organization personnel = new Department("人事部");
        Organization finance = new Department("财务部");
        Organization company1 = new Company("子公司1");
        Organization company2 = new Company("子公司2");
        Organization group = new Group("集团");
        company1.add(development);
        company1.add(personnel);
        company1.add(finance);
        company2.add(development);
        company2.add(personnel);
        company2.add(finance);
        group.add(company1);
        group.add(company2);
        group.print();
    }
}
```