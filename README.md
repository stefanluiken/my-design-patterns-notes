# my-design-patterns-notes
Notes on Design Patterns

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

### Chapter 2 - Observer pattern

Design principles:

- The observer pattern defines a one-to-many relationship (dependency) between objects, so that when one object changes state, all of its dependents are notified and updated automatically.
  - The subject and observers define the above relationship. The observers are dependent on the subject, when the subject's state changes, the observers are notified.
  - Loosely coupled systems means that they are flexible and can handle change because they minimize the interdependency between objects. For instance, if I want to add a new observer I should be able to do that without having to change the subject.
  - Observers are loosely coupled in that the subject knows nothing about them, other than that they implement the Observer interface. 
- We should strive for loosely coupled designs between objects that interact.
- See example below:

```java
public class CurrentConditionsDisplay implements Observer, DisplayElement {

    private float temperature;
    private float humidity;
    private WeatherData weatherData;

    public CurrentConditionsDisplay(WeatherData weatherData) {
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
public interface Observer {
    public void update();
}
```

```java
public interface Subject {
    public void registerObserver(Observer o);
    public void removeObserver(Observer o);
    public void notifyObservers();
}
```

```java
public interface DisplayElement {
    public void display();
}
```

```java
import java.util.ArrayList;
import java.util.List;

public class WeatherData implements Subject {

    private List<Observer> observers;
    private float temperature;
    private float humidity;
    private float pressure;

    public WeatherData() {
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
public class CurrentConditionsDisplay implements Observer, DisplayElement {

    private float temperature;
    private float humidity;
    private WeatherData weatherData;

    public CurrentConditionsDisplay(WeatherData weatherData) {
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