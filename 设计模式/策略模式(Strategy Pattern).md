### 策略模式(Strategy Pattern)

#### 一、先从简单的模拟鸭子应用做起

假设现在有一个鸭子类，里面封装了swim和shout方法，各种鸭子游泳喊叫。因此可以通过设计一个鸭子超类，封装需要的所有方法，然后让子类来继承该父类。

#### 二、现在我们得让鸭子能飞

有一天，发现**一些**鸭子能飞，因此可以在父类中加上fly方法。问题就解决了？然而并非所有的子类都有飞的方法，这种设计显然不合理。那么如何解决这个问题呢？ 

### 三、利用接口如何？

如此，可以将fly方法提取出来，放进一个Flyable接口中。这么一来，只有会飞的鸭子才实现此接口。同样的方式，也可以用来设计一个Quackable接口，因为不是所有的鸭子都会叫。

你觉得这个设计如何？虽然Flyable与Quackable可以解决“一部分”问题（不会再有会飞的橡皮鸭），但是却造成代码无法复用，这只能算是从一个噩梦跳进另一个噩梦。（继承、组合都能达到代码复用的效果，但用组合能使代码耦合度更低）具体来说，如果MallardDuck和RubberDuck的quack()实现是一样呢？目前这种接口的设计就需要在MallardDuck和RubberDuck中重复实现一遍quack()。同理，如果MallardDuck和RedheadDuck的fly()实现是一样的呢？同样需要重复实现一遍fly()。即：用接口的方式无法实现代码复用。

![img](https://upload-images.jianshu.io/upload_images/9571610-829c8bd93072c568.png?imageMogr2/auto-orient/strip|imageView2/2/w/857/format/webp)

#### 四、按照我以前的思维，我可能会想到这样设计

![img](https://upload-images.jianshu.io/upload_images/9571610-e017dda8f5857b59.png?imageMogr2/auto-orient/strip|imageView2/2/w/702/format/webp)

从现在看是糟糕的设计，问题如下：

- 继承层级太多，不可控。如有需求变动，可能需要调整继承关系才能适应新的需求
- QuackableDuck和FlyableDuck是“虚拟”的Duck，严格来讲不是IS－A Duck
- 如果MallardDuck和RubberDuck的quack()实现是一样呢？MallardDuck和RubberDuck是兄弟关系，不是继承关系，所以无法复用，需要在MallardDuck和RubberDuck中重复实现一遍quack()。即：虽然用了一堆继承，但这种方式不能完全实现代码复用。

#### 五、把问题归零

现在我们知道使用继承并不能很好地解决问题，因为鸭子的行为在子类里不断地改变，并且让所有的子类都有这些行为是不恰当的。Flyable与Quackable接口一开始似乎还挺不错，解决了问题（只有会飞的鸭子才继承Flyable），但是Java接口不具有实现代码（Java 8开始接口有默认实现），所以继承（实现）接口无法达到代码的复用。有一个设计原则，恰好用于此状况：**找出应用中可能需要变化之处，把它们独立出来，不要和那些不需要变化的代码混在一起**，该是把鸭子的行为从Duck类中取出来的时候了！



#### 六、分开变化和不会变化的部分

我们知道Duck类内的fly()和quack() 会随着鸭子的不同而改变。为了要把这两个行为从Duck类中分开，我们将把它们从Duck类中取出来，建立一组新类来代表每个行为。

#### 七、设计鸭子的行为

新的设计我们将遵循一个设计原则：**针对接口编程，而不是针对实现编程**

我们利用接口代表每个行为，比方说，FlyBehavior与QuackBehavior，而行为的每个实现都将实现其中的一个接口。所以这次鸭子类不会负责实现Flyable与Quackable接口，反而是由我们制造一组其他类专门实现FlyBehavior与QuackBehavior，这就称为“行为”类。

#### 八、实现鸭子的行为

在此，我们有两个接口，FlyBehavior和QuackBehavior，还有它们对应的类，负责实现具体的行为。

![img](https://upload-images.jianshu.io/upload_images/9571610-c2a06379d2fec0c3.png?imageMogr2/auto-orient/strip|imageView2/2/w/909/format/webp)

这样的设计，可以让飞行和呱呱叫的动作被其他的对象复用，因为这些行为已经与鸭子类无关了。而我们可以新增一些行为，不会影响到既有的行为类。



1、FlyBehavior与行为实现类



```java
public interface FlyBehavior {
    public void fly();
}

public class FlyWithWings implements FlyBehavior{
    @Override
    public void fly() {
        System.out.println("用翅膀飞");
    }
}

public class FlyNoWay implements FlyBehavior {
    @Override
    public void fly() {
        System.out.println("不会飞");
    }
}
```

2、QuackBehavior与行为实现类



```java
public interface QuackBehaivor {
    public void quack();
}

public class Quack implements QuackBehaivor {
    @Override
    public void quack() {
        System.out.println("呱呱叫");
    }
}

public class MuteQuack implements QuackBehaivor {
    @Override
    public void quack() {
        System.out.println("不会叫");
    }
}
```

3、Duck与子类



```csharp
public abstract class Duck {

   FlyBehavior flyBehavior;
   QuackBehaivor quackBehavior;
   
   public abstract void display();
   
   public void performFly() {
       flyBehavior.fly();
   }
   
   public void performQuack() {
       quackBehavior.quack();
   }
   
   public void swim() {
       System.out.println("所有的鸭子都会漂浮");
   }

   // 动态设置飞行行为
   public void setFlyBehavior(FlyBehavior flyBehavior) {
       this.flyBehavior = flyBehavior;
   }

   //动态设置叫的行为
   public void setQuackBehavior(QuackBehaivor quackBehavior) {
       this.quackBehavior = quackBehavior;
   }
}
```



```java
public class MallardDuck extends Duck {
    @Override
    public void display() {
        System.out.println("我是绿头鸭");
    }
}
```













