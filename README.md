# my-design-patterns-notes
Notes on Design Patterns

### Strategy pattern

Design principles:
- Identify the aspects of your application that vary and separate them from what stays the same.
  - Take parts that vary and encapsulate them, so that later you can alter or extend the parts that vary without affecting those that don't.
- Program to an interface (or in other words, supertype), not to an implementation.
  - We should not take interface that literally, the point is to exploit polymorphism by programming to a supertype so that the actual runtime object isn't locked into the code.
  - With the Strategy pattern, each object would delegate specific behavior to a behavior class (which implements a behavior interface). The object behavior relationship is a HAS-A relation instead of an IS-A (which you get if you directly inherit certain behavior, and cannot dynamically change this during runtime).
  - The HAS-A relationship makes sure that the object gets the correct behavior by being composed with the right behavior object. 
- The above is a technique which is the third design principle: Favor composition above inheritance. This not only allows us to encapsulate a family of algorithms into an own set of classes, but lets us change behavior at runtime. 

```java
public class MiniDuckSimulator {

    public static void main(String[] args) {
        Duck modelDuck = new ModelDuck();
        modelDuck.setQuackBehavior(new Squeak());
        modelDuck.setFlyBehavior(new FlyWithWings());

        modelDuck.performQuack();
        modelDuck.performFly();
    }
}
```

```java
public class ModelDuck extends Duck {

    public ModelDuck() {
        flyBehavior = new FlyNoWay();
        quackBehavior = new MuteQuack();
    }

    @Override
    public void display() {
        System.out.println("I am a ModelDuck!");
    }
}

```


```java
public abstract class Duck {
    FlyBehavior flyBehavior;
    QuackBehavior quackBehavior;

    public Duck() {}

    public abstract void display();

    public void performFly() {
        flyBehavior.fly();
    }

    public void performQuack() {
        quackBehavior.quack();
    }

    public void swim() {
        System.out.println("All ducks fly even decoys!");
    }

    public void setFlyBehavior(FlyBehavior fb) {
        flyBehavior = fb;
    }

    public void setQuackBehavior(QuackBehavior qb) {
        quackBehavior = qb;
    }
}
```

```java
public interface FlyBehavior {
    public void fly();
}


public class FlyNoWay implements FlyBehavior {
  @Override
  public void fly() {
    System.out.println("I can't fly!");
  }
}

public class FlyWithWings implements FlyBehavior {
  @Override
  public void fly() {
    System.out.println("I am flying!");
  }
}
```

```java
public interface QuackBehavior {
    public void quack();
}


public class MuteQuack implements QuackBehavior {
  @Override
  public void quack() {
    System.out.println("<<Silence>>");
  }
}

public class Quack implements QuackBehavior {
  @Override
  public void quack() {
    System.out.println("Quack");
  }
}

public class Squeak implements QuackBehavior {
  @Override
  public void quack() {
    System.out.println("Squeak");
  }
}
```
