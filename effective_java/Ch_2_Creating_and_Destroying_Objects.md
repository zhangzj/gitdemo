## Chapter 2 Creating and Destroying Objects

如何正确地创建对象，什么事推荐的创建方式，什么是不推荐的，确保对象的合理销毁，如何管理销毁前的清理操作

### Item1 使用静态工厂方法来代替构造器

使用包装器来演示一个静态工厂方法的例子，Boolean接受一个基本类型的布尔，返回的是包装过后的Boolean对象，TRUE或者FALSE

```
public static Boolean valueOf(boolean b){
  return b ? Boolean.TRUE : Boolean.FALSE;
}
```

相比public的构造器来说，静态工厂方法有一些优势，同时也有一定的劣势

1. 静态工厂方法可以提供更加具有描述性的名字，构造器仅仅是额类名一样的名字
2. 构造器每一次被调用的时候都会构造一个全新的对象，但是静态工厂方法不会
3. 静态工厂方法可以返回返回类型的任何子类型，更加地灵活指定返回的对象类型
4. 静态工厂方法降低创建参数化类实例的冗余，更方便

1. 缺点也有，只有静态工厂方法的类，如果没有public或者protected的构造器的话，也就不能被继承，被子类化
2. 第二个缺点是静态工厂方法和其他的静态方法看上去区别不明显，没有让人一看就看出来是干什么的方便

在静态工厂方法的命名中，有一些约定俗成的名字来表示特定的含义；

- valueof；返回一个和参数有同样的是值的对象，类似于类型转换方法的作用
- of；等同于valueOf，在enumSet中使用
- getInstance；返回一个参数描述的实例，没有参数的时候用在返回单例上
- newInstance；类似于getInstance，返回的每一个实例都是新的对象
- getType；会返回一个type类型的对象
- newType；新建一个Type类型的对象返回

### Item2 在构造器参数很多的时候考虑使用Builder来替代

在这个类的参数特别多的时候，一般是会考虑使用重叠构造器来解决，大家调用参数最多的那个构造器，多的参数就传入默认的值就好；这样子在新建对象的时候需要传入很多没有必要的参数，重叠构造器可以解决，但是在客户端代码使用的时候跟难写，也很难理解

```
//Telescoping constructor pattern - does not scale well!
public class NutritionFacts {

  private final int servingSize; // (mL) required
  private final int servings; // (per container) required
  private final int calories; // optional
  private final int fat; // (g) optional
  private final int sodium; // (mg) optional
  private final int carbohydrate; // (g) optional

  public NutritionFacts(int servingSize, int servings) {
    this(servingSize, servings, 0);
  }

  public NutritionFacts(int servingSize, int servings, int calories) {
    this(servingSize, servings, calories, 0);
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat) {
    this(servingSize, servings, calories, fat, 0);
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
    this(servingSize, servings, calories, fat, sodium, 0);
  }

  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = calories;
    this.fat = fat;
    this.sodium = sodium;
    this.carbohydrate = carbohydrate;
  }
}
```

第二个解决方案是是使用JavaBeans的方式，先新建一个对象，然后是setter方法来设定各个属性；看上去是比上一个重叠构造器的方式好了很多，但是要想新建一个对象那就麻烦了，一大串的属性需要设定;并且javabeans的模式还带来了一些隐患，在对象构造过程中，因为被分解成了好多条语句，有可能会有属性状态前后不一致的问题；这个模式基本上也封杀了对象不可修改的可能性，随时可以改变属性的状态，造成安全隐患

```
NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
cocaCola.setSodium(35);
cocaCola.setCarbohydrate(27);
```

```
// JavaBeans Pattern - allows inconsistency, mandates mutability
public class NutritionFacts {
  // Parameters initialized to default values (if any)
  private int servingSize = -1; // Required; no default value
  private int servings = -1; // " " " "
  private int calories = 0;
  private int fat = 0;
  private int sodium = 0;
  private int carbohydrate = 0;
  public NutritionFacts() { }

  public void setServingSize(int val) { servingSize = val; }
  public void setServings(int val) { servings = val; }
  public void setCalories(int val) { calories = val; }
  public void setFat(int val) { fat = val; }
  public void setSodium(int val) { sodium = val; }
  public void setCarbohydrate(int val) { carbohydrate = val; }
}
```

还好，还有第三种选择，Builder模式，首先客户端代码调用构造器或者静态方法来获得一个Builder对象，之后调用类似builder中的setter的方法来设定参数，最后调用build方法来冻结对象使得不能修改。实现上，Builder是一个静态内部类来实现

```
// Builder Pattern
public class NutritionFacts {
  private final int servingSize;
  private final int servings;
  private final int calories;
  private final int fat;
  private final int sodium;
  private final int carbohydrate;
  public static class Builder {
    // Required parameters
    private final int servingSize;
    private final int servings;
    // Optional parameters - initialized to default values
    private int calories = 0;
    private int fat = 0;
    private int carbohydrate = 0;
    private int sodium = 0;
    public Builder(int servingSize, int servings) {
      this.servingSize = servingSize;
      this.servings = servings;
    }
    public Builder calories(int val)
    { calories = val; return this; }
    public Builder fat(int val)
    { fat = val; return this; }
    public Builder carbohydrate(int val)
    { carbohydrate = val; return this; }
    public Builder sodium(int val)
    { sodium = val; return this; }
    public NutritionFacts build() {
      return new NutritionFacts(this);
    }
  }
  private NutritionFacts(Builder builder) {
    servingSize = builder.servingSize;
    servings = builder.servings;
    calories = builder.calories;
    fat = builder.fat;
    sodium = builder.sodium;
    carbohydrate = builder.carbohydrate;
  }
}
```

客户端代码调用Builder进行构造

```
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8).
calories(100).sodium(35).carbohydrate(27).build();
```

Builder模式的适用场景：如果类的构造器或者静态工厂中具有多个参数，设计这种类的时候，Builder模式就是不错的选择，特别是当大多数参数都是可选的时候。

### Item3 用私有构造器或者美剧类型强化Singleton属性

第一种实现方法，吧构造器保持私有，导出公有的静态成员，客户端就可以访问唯一的实例，第一种方法，公有的静态成员是final域

```
// Singleton with public final field
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() { ... }
  public void leaveTheBuilding() { ... }
}
```

第二种实现方法，把静态私有域实例私有化，然后公开一个静态工厂方法获取实例引用

```
// Singleton with static factory
public class Elvis {
  private static final Elvis INSTANCE = new Elvis(); private Elvis() { ... }
  public static Elvis getInstance() { return INSTANCE; }
  public void leaveTheBuilding() { ... }
}
```

java1.5之后，还有第三种实现，使用氮元素的枚举类型实现，这已经成为实现Singleton的最佳方法，更简洁，无偿提供序列化机制

```
// Enum singleton - the preferred approach
public enum Elvis {
  INSTANCE;
  public void leaveTheBuilding() { ... }
}
```

### Item4 通过私有化构造器强化不可实例化的能力

要保证一个类，无论是被继承还是怎样，都不能被实例化，只要私有化构造器就好，对于一些不需要实例化的类，比如Math和ArrayUtil等工具类中的函数，这种防止实例化的方式可用

```
// Noninstantiable utility class
public class UtilityClass {
  // Suppress default constructor for noninstantiability
  private UtilityClass() {
    throw new AssertionError();
  }
  ...  // Remainder omitted
}
```

### Item5 避免创建不必要的对象

对于一些不会被修改的对象，不管是不可修改，还是本身可修改但是不准备修改，都可以采用一些技巧来避免性能的浪费；比如一个Person类，来表示一个人的生日等，其中一个方法isBabyBoomer来判断这个人是不是婴儿潮出生的，条件就是判断生日是不是在某个时间区间内，一般首先的实现是这样

```
public class Person {
  private final Date birthDate;
  // Other fields, methods, and constructor omitted
  // DON'T DO THIS!
  public boolean isBabyBoomer() {
    // Unnecessary allocation of expensive object
    Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
    Date boomStart = gmtCal.getTime();
    gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
    Date boomEnd = gmtCal.getTime();

    return birthDate.compareTo(boomStart) >= 0 &&
    birthDate.compareTo(boomEnd)   <  0;
  }
}
```

很明显，在方法每一被调用的时候，都会新创建几个Calendar对象来表示时间，但是这是一种浪费，下一个版本会使用一个静态初始化器来避免这种浪费

```
class Person {
  private final Date birthDate;
  // Other fields, methods, and constructor omitted
  /**
  * The starting and ending dates of the baby boom.
  */
  private static final Date BOOM_START;
  private static final Date BOOM_END;
  static {
    Calendar gmtCal =
    Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
    BOOM_START = gmtCal.getTime();
    gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
    BOOM_END = gmtCal.getTime();
  }
  public boolean isBabyBoomer() {
    return birthDate.compareTo(BOOM_START) >= 0 &&
    birthDate.compareTo(BOOM_END)   <  0;
  }
}
```

Java中的自动装箱的特性也会造成创建不必要的对象，一般来说要优先考虑使用基本类型，而不是装箱基本类型，当心无意识的自动装箱

```
// Hideously slow program! Can you spot the object creation?
public static void main(String[] args) {
  Long sum = 0L;
  for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += i;
  }
  System.out.println(sum);
}
```

### Item6 消除过期的对象引用

虽然相比C或者C++这种需要程序员自己管理内存的语言而言，Java的垃圾回收机制带来了巨大的方便，但是也不是说就不用再考虑内存管理的事情了，考虑下面的简单的栈实现

```
```
