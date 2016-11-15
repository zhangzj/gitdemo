## Ch3 对于所有对象都通用的方法

### Item8 覆盖equals时遵守通用约定

equals方法不被覆盖是最好的结果，实际上系统默认的行为是：每一个实例都是唯一的，所以也只会与实例自己本身相等；那么什么时候在需要覆盖equals来改变默认行为？需要实现逻辑相等的时候，比如两个Integer对象，虽然是不同的实例，但是内部的值时一样的，那就相等；

覆盖equals方法的时候必须要注意的一些点：也就是通用约定

1. 自反性，reflexive，对于任何非null的引用值x；x.equals(x)必须返回true，自己等于自己必须为真
2. 对称性，symmetric，对于任何非null的引用值x，y；当且仅当y.equals(x)返回true的时候，x.equals(y)返回true，两个实例互为相等
3. 传递性，transitive，对于任何非null的引用值x，y，z；x等于y，y等于z，那么x等于z
4. 一致性，consistent，只要上下文没有修改，与时间无关，无论调用多少次，相等的判断结果都不会改变
5. 对于任意非null的x，和null的相等结果必须为false

如何实现高质量的equals方法？

1. 使用==操作符先判断是否是这个对象的引用
2. 使用instanceof检查参数是不是正确的类型
3. 把参数转换成正确的类型，转换之前进行instanceof测试，确保转换成功
4. 对于类中的每一个关键域，检查参数中的域是不是与该对象中的域相匹配
5. 写完equals之后，要问一下，是不是满足了三大特性，堆成，传递，一致？

一些其他建议

1. 覆盖equals方法时总要覆盖hashCode方法
2. 不要企图让equals方法过于智能
3. 不要将equals中的Object对象替换为其他类型（使用@Override注解防微杜渐）

### Item9 覆盖equals方法时总要覆盖hashCode方法

hashCode 的通用约定中，如果覆盖equals但是没有覆盖hashCode，会导致一些类没有办法工作，HashMap，HashSet，HashTable

HashCode方法的Object约定

1. 在 Java 应用程序执行期间，在同一对象上多次调用 hashCode 方法时，必须一致地返回相同的整数，前提是对象上 equals 比较中所用的信息没有被修改。从某一应用程序的一次执行到同一应用程序的另一次执行，该整数无需保持一致。

2. 如果根据 equals(Object) 方法，两个对象是相等的，那么在两个对象中的每个对象上调用 hashCode 方法都必须生成相同的整数结果。

3. 以下情况不 是必需的：如果根据 equals(java.lang.Object) 方法，两个对象不相等，那么在两个对象中的任一对象上调用 hashCode 方法必定会生成不同的整数结果。但是，程序员应该知道，为不相等的对象生成不同整数结果可以提高哈希表的性能。

当equals方法被重写时，通常有必要重写 hashCode 方法，以维护 hashCode 方法的常规协定，该协定声明相等对象必须具有相等的哈希码

**上面两条的意思还是很了解的，暂时遇到equals和hashCode还是先绕道走，不要想着覆盖了**

### Item10 始终要覆盖toString

toString的约定还是比较简单，就是返回一个可读性好，合适的字符串就好，默认实现是返回一个字符串，由类名和hashCode组成；覆盖的toString需不需要自带格式有待商榷，但是要是有格式那就要贯彻这个风格；toString返回的信息，最好是要包含这个对象中所有有用的信息，并且这个对象应该提供可以访问这些信息的方法接口

### Item11 谨慎覆盖clone

不要管这个Cloneable接口，也别BB这个clone了，不覆盖，不管~~

### Item12 考虑实现Comparable接口

Comparable的意义在表示这个类的实例有可排序性，具有内在的排序关系，要实现的方法也只有一个compareTo；好处多多，可以跟许多泛型算法和集合实现进行交互；

```
public interface Comparable<T> {
  int compareTo(T t);
}
```

通用约定和equals相似，在对象与指定对象进行比较，当对象小于，等于，大于指定对象的时候，分别返回一个负整数，零或者整数
