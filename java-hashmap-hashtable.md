Title: java HashMap和HashTable的爱恨情仇
Date: 2013-11-26 00:51
Author: fuqiang
Category: 技术流
Tags: hashmap, hashtab
Slug: java-hashmap-hashtable

大家经常使用HashMap和HashTable，但是很少人对这两个集合的原理有一个清楚的理解，我前面翻译了这两个类的类描述，如果没有看过，先看看那撇脚的翻译。

一，对于HashMap和HashTable，最直观的区别有两点：

1.HashMap的key和value可以为null,HashTable的不能（抛出NullPointerException异常）

2.HashMap不是线程安全的，HashTable是线程安全的。即多个线程同时操作实体时，HashMap的实例有可能会出现未知的异常数据，而HashTable会按照预期给出结果。

其实还有一个很重要的一点，就是HashTable（虽然HashMap类没有明确写出来）的泛型必须是实现了equals和hashCode方法，java.lang.\*中的包装类都已经实现，因此可以直接使用，但是如果是自己定义的实体，那就必须实现这两个方法。在这里，我用简单的代码作为测试例子。如果不实现上述的两个方法，那么相同id的实例并不相等，但是实现之后，则会相等。测试代码如下：

    public class AA {
    private int id;
    public AA(int id) {
    this.id = id;
    }
    public int getId() {
    return id;
    }
    public void setId(int id) {
    this.id = id;
    }
    public String toString() {
    return "[" + id + "]相等";
    }
    // @Override
    // public int hashCode() {
    // final int prime = 31;
    // int result = 1;
    // result = prime * result + id;
    // return result;
    // }
    // @Override
    // public boolean equals(Object obj) {
    // if (this == obj)
    // return true;
    // if (obj == null)
    // return false;
    // if (getClass() != obj.getClass())
    // return false;
    // AA other = (AA) obj;
    // if (id != other.id)
    // return false;
    // return true;
    // }

    }

    import java.util.HashMap;
    import java.util.Hashtable;
    import java.util.Map;
    import java.util.Map.Entry;

    public class Test {

    public static void main(String[] args) {
    // TODO Auto-generated method stub
    Hashtable<AA, AA> ht = new Hashtable<AA, AA>();
    ht.put(new AA(1), new AA(1));
    ht.put(new AA(2), new AA(3));
    ht.put(new AA(3), new AA(3));
    for(Entry<AA, AA> entry : ht.entrySet()) {
    if(entry.getKey().equals(entry.getValue())) {
    System.out.println(entry.getKey().toString() +", " + entry.getValue().getId());
    }
    if(ht.containsKey(entry.getValue())) {
    System.out.println("key : " + entry.getKey().toString() +entry.getValue().getId());
    }
    }
    ht.clear();

    Map<AA, AA> map = new HashMap<AA, AA>();
    map.put(new AA(1), new AA(1));
    map.put(new AA(2), new AA(3));
    map.put(new AA(3), new AA(3));
    for(Entry<AA, AA> entry : map.entrySet()) {
    if(entry.getKey().equals(entry.getValue())) {
    System.out.println("map" + entry.getKey().toString() +", " + entry.getValue().getId());
    }
    if(map.containsKey(entry.getValue())) {
    System.out.println("map key : " + entry.getKey().toString() + entry.getValue().getId());
    }
    }
    }

    }

<!--more-->

在两个类中，方法containsKey方法和get方法以及put方法是使用对象的hashCode值和equals方法比较两个对象，如果两个条件同时成立，则说明相等，但是在containsValue方法中，只会判断两个对象是否相等即可。这是因为在key中，需要通过hashcode值来寻址对象的地址，因此必须要加上此值的判断。

二，在HashMap的描述中，建议尽量不要依赖于迭代器的同步异常，因为这个异常只是正对于这个实例内部而言的，对于这个实例整体来说，它并不能保证元素的同步。

三，如下图所示，这两个类的keySet的实现也是完全不同，一个使用集合同步框架实现，一个是一般的实现，当然entrySet方法也一样。一切尽在不言中了.....

![image](http://img.blog.csdn.net/20131126003357546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmdmdWxsbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

四，初始化集合类时，尽量做到准确定位数量并且初始化，因为如果对初始容量设置的不合理，将会一直影响集合的性能，具体原因可以去看看我的翻译文章。

五，现在写的还不全，等以后更深入的再追加吧
