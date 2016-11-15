## Ch 6 枚举和注解

1.5之后加了两个新的引用类型，一个是枚举类型，还有一个新的接口类型叫做注解

### 30 用enum代替int常量

### 31 用实例域代替序数

### 32 用EnumSet代替位域

### 33 用EnumMap代替序数索引

### 34 用接口模拟可伸缩的枚举

### 35 注解优先于命名模式

在注解之前，一般是通过命名模式，naming pattern来表明某些程序元素组要通过某种工具或者框架进行特殊处理，比如junit框架要求方法的名字一定是test开头；命名模式缺点蛮多的，比如没有警告，写错就写错了；不灵活，所有的test开头的，不管是不是有意的都会被junit处理

注解横空出世，定义注解类型来指定简单的测试

```
// Marker annotation type declaration
import java.lang.annotation.*;
/**
* Indicates that the annotated method is a test method.
* Use only on parameterless static methods.
*/
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Test {
}
```

有了注解之后，就不用再使用命名模式了

### 36 坚持使用Override注解

### 37 用标记接口定义类型

标机接口，marker interface，是没有包含方法声明的接口，只是指明一个梨实现了具有某种属性的接口，表明这个类可以做到什么事情，比如Serializable，表示这个类可以被序列化
