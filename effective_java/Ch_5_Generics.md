## Ch5 泛型

### Item23 不要在新代码中使用原生态类型

一些术语：参数化类型，泛型类或者泛型接口，比如一个列表，List<E>，就是表示E类型的列表；还有多个参数化类型的；所谓原生态类型，就是没有任何泛型信息的泛型，List；

如果使用原生态类型，就失去了泛型在安全性和表述性方面的所有优势

![](http://ww1.sinaimg.cn/large/8d6a2535gw1f9sue2koq7j20v10chjvg.jpg)

### 24 消除非受检警告

尽可能消除每一个警告，确保代码是类型安全的

### 25 列表优先于数组

使用列表List来代替数组

### 26  优先考虑带泛型的类

编写自己的泛型；

对比简单的堆栈Stack实现和泛型实现

```
// Object-based collection - a prime candidate for generics
public class Stack {
  private Object[] elements;
  private int size = 0;
  private static final int DEFAULT_INITIAL_CAPACITY = 16;
  public Stack() {
    elements = new Object[DEFAULT_INITIAL_CAPACITY];
  }
  public void push(Object e) {
    ensureCapacity();
    elements[size++] = e;
  }
  public Object pop() {
    if (size == 0)
    throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference
    return result;
  }
  public boolean isEmpty() {
    return size == 0;
  }
  private void ensureCapacity() {
    if (elements.length == size)
    elements = Arrays.copyOf(elements, 2 * size + 1);
  }
}
```

泛型第一版，编译不会过，在新建E对象的时候会出错，因为数组是不能创建不可具体化的类型的，可以采用一些方法压制，@SuppressWarnings

```
// Initial attempt to generify Stack = won’t compile!
public class Stack<E> {
  private E[] elements;
  private int size = 0;
  private static final int DEFAULT_INITIAL_CAPACITY = 16;
  public Stack() {
    elements = new E[DEFAULT_INITIAL_CAPACITY];
  }
  public void push(E e) {
    ensureCapacity();
    elements[size++] = e;
  }
  public E pop() {
    if (size==0)
    throw new EmptyStackException();
    E result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference
    return result;
  }
  ... // no changes in isEmpty or ensureCapacity
}
```

### 27 优先考虑泛型方法

类可以从泛型中收益，方法也是一样，静态工具方法尤其适合泛型化；

### 28 利用有限制通配符来提升API的灵活性

### 29 优先考虑类型安全的异构容器

自己带上类型信息，将key键参数化，而不是将容器本身参数化；
