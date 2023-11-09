# 迭代器模式
    顺序访问集合对象的元素，不需要知道集合对象的底层表示。
    属于行为型的设计模式
    场景：需要隐藏集合对象的底层表示
# 实现
```java
// 此处Iterator 来自于java.util.Iterator;
public class Array implements Iterator {
    private Object[] obj;

    int index = 0;
    public Array(Object[] obj) {
        this.obj = obj;
    }

    @Override
    public boolean hasNext() {
        if(index >=obj.length){
            return false;
        }
        return true;
    }

    @Override
    public Object next() {
        Object target = obj[index];
        index = index +1;
        return target;
    }
}
```
```java
// 此处Iterator 来自于java.util.Iterator;
public class Collection implements Iterator {
    List<Object> list;
    private Integer index = -1;
    public Collection(List<Object> list) {
        this.list = list;
        index=0;
    }

    @Override
    public boolean hasNext() {
        if(list.isEmpty()||index>=list.size()){
            return false;
        }
        return true;
    }

    @Override
    public Object next() {
        Object target = list.get(index);
        index=index+1;
        return target;
    }
}
```
```java
public interface IteratorFactory {
    Iterator getIterator();
}
```
```java
public class IteratorImpl implements IteratorFactory{
    private Object[] objects;
    private List obj;
    private Iterator iterator;
    public IteratorImpl(Object[] objects) {
        this.objects = objects;
        iterator = new Array(objects);
    }

    public IteratorImpl(List objects) {
        this.obj = obj;
        iterator = new Collection(objects);
    }

    @Override
    public Iterator getIterator() {
        return iterator;
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        System.out.println("=========================迭代器模式");
        Integer[] num={1,2,3,4,5,6};
        String[] letter={"a","b","c","d"};
        List<String> keys = new ArrayList<>();
        keys.add("A");
        keys.add("B");
        keys.add("E");
        keys.add("C");
        keys.add("N");
        IteratorImpl it1 = new IteratorImpl(num);
        IteratorImpl it2 = new IteratorImpl(letter);
        IteratorImpl it3 = new IteratorImpl(keys);
        iterator(it1.getIterator());
        System.out.println();
        iterator(it2.getIterator());
        System.out.println();
        iterator(it3.getIterator());
        System.out.println();
    }
   static void iterator(Iterator iterator){
        while (iterator.hasNext()){
            System.out.print(iterator.next());
        }
    }
}
```