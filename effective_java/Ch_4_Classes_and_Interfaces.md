## Ch4 类和接口

### Item13 使得类和成员的额可访问性最小化

一个好的模块会遵守软件设计的基本原则，也就是信息隐藏和封装，尽量少的暴露自己的细节，只通过API进行通信；这样可以有效地达到解耦合的效果

实体的可访问性受到两个因素的影响：一个是实体所在的位置和访问修饰符共同决定；一个类定义的访问权限，等不public，就不要public，包里面默认是包私有，那就默认；确定需要公开的类再公开，一旦公开了这个类或者接口，那就需要一直支持兼容下去，这是程序员的责任~

实例域，也就是对象的成员变量绝对不能是public，原则其实很明了，就是死命降低可访问性，公开的越少越好；仔细设计最小的public API之后，要防止把任何散乱的借口或者成员变成API的一部分，除了public static final的域这种特殊情形之外，public的class都不能有任何public的域，并且要确保public staticfinal的域所引用的对象都是不可变的

### Item14 在公有类中使用访问方法而非公有域

坚持使用私有域加上公有访问方法的方式

```
// Encapsulation of data by accessor methods and mutators
class Point {
  private double x;
  private double y;
  public Point(double x, double y) {
    this.x = x;
    this.y = y;
  }
  public double getX() { return x; }
  public double getY() { return y; }
  public void setX(double x) { this.x = x; }
  public void setY(double y) { this.y = y; }
}
```

### Item15 使得可变性最小化

不可变，意味着对象是不能被修改的，在这整个生命周期中，所有的信息在创建的时候指定OK，一直到结束；Java中有这种不可变的，比如String，BigInteger，BigDecimal；不可变类的好处蛮多的，更加容易实现和设计，不容易出错，更安全；

不可变类的五条规则：
1. 不提供任何会修改对象状态的方法
2. 保证类不会被扩展，防止子类化被继承，class上使用final修饰符，还有其他的办法；比如静态工厂私有化构造器
3. 所有的域都变成私有的，防止客户端代码访问
4. 所有的域都是final的，通过系统强制来确保不可变
5. 确保对于任何可变组件的互斥访问；如果有域是指向可变的对象，确保客户端代码不会拿到这个引用，不要使用客户端提供的对象来初始化，也不要返回任何对象引用

```
public final class Complex {
  private final double re;
  private final double im;
  public Complex(double re, double im) {
    this.re = re;
    this.im = im;
  }
  // Accessors with no corresponding mutators
  public double realPart() { return re; }
  public double imaginaryPart() { return im; }
  public Complex add(Complex c) {
    return new Complex(re + c.re, im + c.im);
  }
  public Complex subtract(Complex c) {
    return new Complex(re - c.re, im - c.im);
  }
  public Complex multiply(Complex c) {
  return new Complex(re * c.re - im * c.im,
    re * c.im + im * c.re);
  }
  public Complex divide(Complex c) {
    double tmp = c.re * c.re + c.im * c.im;
    return new Complex((re * c.re + im * c.im) / tmp,
    (im * c.re - re * c.im) / tmp);
  }
  @Override public boolean equals(Object o) {
    if (o == this)
    return true;
    if (!(o instanceof Complex))
    return false;
    Complex c = (Complex) o;
    // See page 43 to find out why we use compare instead of ==
    return Double.compare(re, c.re) == 0 &&
    Double.compare(im, c.im) == 0;
  }
  @Override public int hashCode() {
    int result = 17 + hashDouble(re);
    result = 31 * result + hashDouble(im);
    return result;
  }
  private int hashDouble(double val) {
    long longBits = Double.doubleToLongBits(re);
    return (int) (longBits ^ (longBits >>> 32));
  }
  @Override public String toString() {
      return "(" + re + " + " + im + "i)";
    }
}
```

### Item16 复合优先于继承

继承，inheritance，实现继承，表示一个类扩展另一个类；还有接口继承，一个接口扩展其他接口；子类打破了封装性，依赖于超类当中的特定功能和细节；

复合，composition，在新的类中增加一个私有域，所有的消息通过这个私有域进行转发，这样就比较安全，实现一个转发类，来被其他的类继承，这样子就比较安全，对于未知的类，不要继承，也不要覆盖，组合还是可以用的

```
// Wrapper class - uses composition in place of inheritance
public class InstrumentedSet<E> extends ForwardingSet<E> {
  private int addCount = 0;
  public InstrumentedSet(Set<E> s) {
    super(s);
  }
  @Override public boolean add(E e) {
    addCount++;
    return super.add(e);
  }
  @Override public boolean addAll(Collection<? extends E> c) {
    addCount += c.size();
    return super.addAll(c);
  }
  public int getAddCount() {
    return addCount;
  }
}
// Reusable forwarding class
public class ForwardingSet<E> implements Set<E> {
  private final Set<E> s;
  public ForwardingSet(Set<E> s) { this.s = s; }
  public void clear() { s.clear(); }
  public boolean contains(Object o) { return s.contains(o); }
  public boolean isEmpty() { return s.isEmpty(); }
  public int size() { return s.size(); }
  public Iterator<E> iterator() { return s.iterator(); }
  public boolean add(E e) { return s.add(e); }
  public boolean remove(Object o) { return s.remove(o); }
  public boolean containsAll(Collection<?> c)
  { return s.containsAll(c); }
  public boolean addAll(Collection<? extends E> c)
  { return s.addAll(c); }
  public boolean removeAll(Collection<?> c)
  { return s.removeAll(c); }
  public boolean retainAll(Collection<?> c)
  { return s.retainAll(c); }
  public Object[] toArray() { return s.toArray(); }
  public <T> T[] toArray(T[] a) { return s.toArray(a); }
  @Override public boolean equals(Object o)
  { return s.equals(o); }
  @Override public int hashCode() { return s.hashCode(); }
  @Override public String toString() { return s.toString(); }
}
```

### Item17 要么为继承而设计，并提供文档说明，要么就禁止继承

### Item18 接口优于抽象类

接口和抽象类的区别在于，抽象类允许包含某些方法的实现，但是接口完全没有实现

接口有一些优势；现有的类可以容易被更新，实现新的接口；接口是定义mixin的理想选择；接口允许我们构造非层次机构的类型框架；

### Item19 接口只用于定义类型

常量接口，在接口里面不定义任何方法，而是定义静态的final域，每一个域到处一个常量，这样子不可取，这是对接口的不良使用

接口只用来定义类型，不应该用来到处常量

### Item20 类层次优于标签类

### Item21 用函数对象表示策略

### Item22 优先考虑静态成员类

嵌套类是被定义在另一个类内部的类，分成四种：静态内部类，非静态成员类，匿名类，局部类；后面三种称为内部类

静态内部类就是一个类定义，只是恰好在一个类的内部；
类的迭代器一般是非静态内部类来实现的，这个内部类的实例绑定在外部类的实例上，单独创建是不可能的；
