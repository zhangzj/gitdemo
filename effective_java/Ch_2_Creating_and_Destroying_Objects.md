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

  public NutritionFacts(int servingSize, int servings, int calories, int fat,
    int sodium) {
      this(servingSize, servings, calories, fat, sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat,
      int sodium, int carbohydrate) {
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
