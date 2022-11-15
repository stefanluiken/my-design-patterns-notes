# my-design-patterns-notes
Notes on Design Patterns

![](design_patterns.jpg)

### Chapter 1 - Strategy pattern

Design principles:
- Identify the aspects of your application that vary and separate them from what stays the same.
  - Take parts that vary and encapsulate them, so that later you can alter or extend the parts that vary without affecting those that don't.
- Program to an interface (or in other words, supertype), not to an implementation.
  - We should not take interface that literally, the point is to exploit polymorphism by programming to a supertype so that the actual runtime object isn't locked into the code.
  - With the Strategy pattern, each object would delegate specific behavior to a behavior class (which implements a behavior interface). The object behavior relationship is a HAS-A relation instead of an IS-A (which you get if you directly inherit certain behavior, and cannot dynamically change this during runtime).
  - The HAS-A relationship makes sure that the object gets the correct behavior by being composed with the right behavior object. 
- The above is a technique which is the third design principle: Favor composition above inheritance. This not only allows us to encapsulate a family of algorithms into an own set of classes, but lets us change behavior at runtime. 
- See example below:

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

### Chapter 2 - Observer.Observer pattern

Design principles:

- The observer pattern defines a one-to-many relationship (dependency) between objects, so that when one object changes state, all of its dependents are notified and updated automatically.
  - The subject and observers define the above relationship. The observers are dependent on the subject, when the subject's state changes, the observers are notified.
  - Loosely coupled systems means that they are flexible and can handle change because they minimize the interdependency between objects. For instance, if I want to add a new observer I should be able to do that without having to change the subject.
  - Observers are loosely coupled in that the subject knows nothing about them, other than that they implement the Observer.Observer interface. 
- We should strive for loosely coupled designs between objects that interact.
- See example below:

```java
import Observer.DisplayElement;
import Observer.Observer;
import Observer.WeatherData;

public class CurrentConditionsDisplay implements Observer, DisplayElement {

  private float temperature;
  private float humidity;
  private WeatherData weatherData;

  public Observer.CurrentConditionsDisplay(WeatherData weatherData) {
    this.weatherData = weatherData;
    weatherData.registerObserver(this);
  }

  @Override
  public void update() {
    this.temperature = weatherData.getTemperature();
    this.humidity = weatherData.getHumidity();
    display();
  }

  @Override
  public void display() {
    System.out.println("Current conditions: " + temperature + "C degrees and " + humidity + "% humidity");
  }

}
```

```java
public interface Observer.Observer {
    public void update();
}
```

```java
import Observer.Observer;

public interface Subject {
  public void registerObserver(Observer o);

  public void removeObserver(Observer o);

  public void notifyObservers();
}
```

```java
public interface Observer.DisplayElement {
    public void display();
}
```

```java
import Observer.Observer;

import java.util.ArrayList;
import java.util.List;

public class Observer.WeatherData implements Subject {

  private List<Observer> observers;
  private float temperature;
  private float humidity;
  private float pressure;

  public Observer.WeatherData() {
    observers = new ArrayList<>();
  }

  @Override
  public void registerObserver(Observer o) {
    observers.add(o);
  }

  @Override
  public void removeObserver(Observer o) {
    observers.remove(o);
  }

  @Override
  public void notifyObservers() {
    for (Observer observer : observers) {
      observer.update();
    }
  }

  public void measurementsChanged() {
    notifyObservers();
  }

  public void setMeasurements(float temperature, float humidity, float pressure) {
    this.temperature = temperature;
    this.humidity = humidity;
    this.pressure = pressure;
    measurementsChanged();
  }

  public float getTemperature() {
    return temperature;
  }

  public float getHumidity() {
    return humidity;
  }

  public float getPressure() {
    return pressure;
  }
}
```

```java
import Observer.Observer;
import Observer.WeatherData;

public class CurrentConditionsDisplay implements Observer, DisplayElement {

  private float temperature;
  private float humidity;
  private WeatherData weatherData;

  public Observer.CurrentConditionsDisplay(WeatherData weatherData) {
    this.weatherData = weatherData;
    weatherData.registerObserver(this);
  }

  @Override
  public void update() {
    this.temperature = weatherData.getTemperature();
    this.humidity = weatherData.getHumidity();
    display();
  }

  @Override
  public void display() {
    System.out.println("Current conditions: " + temperature + "C degrees and " + humidity + "% humidity");
  }

}
```

### Chapter 3 - Decorator pattern

Design principles:

- The decorator pattern attaches additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.
- Classes should be open for extension, but closed for modification. 
- Decorators have the same supertype as the object they decorate, and we can use one or more decorators to wrap an object. 
- Given that the decorator has the same supertype as the object it decorates, we can pass around a decorated object in place of the original (wrapped) object.
- The decorator adds its own behavior before and/or after delegating to the object it decorates to do the rest of the job.


```java
public class CoffeeShopSimulation {

    public static void main(String[] args) {
        // Espresso
        Beverage espresso = new Espresso();
        System.out.println(espresso.getDescription() + " : " + espresso.cost());

        // Cappuccino
        Beverage espresso2 = new Espresso();
        espresso2 = new Milk(espresso2);
        System.out.println(espresso2.getDescription() + " : " + espresso2.cost());

        // Cappuccino with caramel
        Beverage espresso3 = new Espresso();
        espresso3 = new Milk(espresso3);
        espresso3 = new Caramel(espresso3);
        System.out.println(espresso3.getDescription() + " : " + espresso3.cost());
    }
}
```

```java
public abstract class Beverage {
    String description = "Unknown Beverage";

    public String getDescription() {
        return description;
    }

    public abstract double cost();
}
```

```java
public abstract class CondimentDecorator extends Beverage {
    Beverage beverage;
    public abstract String getDescription();
}
```

```java
public class Espresso extends Beverage {

    public Espresso() {
        description = "Espresso";
    }

    @Override
    public double cost() {
        return 1.99;
    }
}
```

```java
public class Milk extends CondimentDecorator {

    public Milk(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Milk";
    }
    @Override
    public double cost() {
        return beverage.cost() + 0.20;
    }

}
```

```java
public class Caramel extends CondimentDecorator {

    public Caramel(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Caramel";
    }
    @Override
    public double cost() {
        return beverage.cost() + 0.10;
    }

}
```

### Chapter 4 - Factory Method pattern
Factories handle the details of object creation.
A factory method handles object creation and encapsulates it in a subclass. This decouples the client code in the superclass from the object creation code in the sublcass.

With the Factory Method pattern, we have Creator classes and Product classes:
- Creator classes, often have an abstract creator class that defines an abstract factory method, that subclasses (factory methods) implement to produce a product.
- Product classes, all the products (objects) that are produced by the creator classes.


Design principles:

### Chapter 5 - Singleton pattern
The Singleton Pattern ensures a class has only one instance, and provides a global point of access to it: 

```java
public class Singleton {

    private volatile static Singleton uniqueInstance;

    private Singleton() {}

    // By adding the synchronized keyword, we force every thread to wait its turn before it can enter the method.
    // We only do this during the first run!
    public static synchronized Singleton getInstance() {
        if (uniqueInstance == null) {
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }

}
```

Or in Enum form:

```java
public enum SingletonEnum {
    UNIQUE_INSTANCE;
}

public class SingletonClient {
    public void main(String[] args) {
        SingletonEnum singletonEnum = SingletonEnum.UNIQUE_INSTANCE;
    }
}
```