**Design Patterns with examples in Java**

![picture](https://img.shields.io/badge/-***%20Design%20Patterns%20***-brightgreen)
![picture](https://img.shields.io/badge/Design%20Patterns-Singleton-%238D75FF)
![picture](https://img.shields.io/badge/Design%20Patterns-Builder-%238D75FF)
![picture](https://img.shields.io/badge/Design%20Patterns-Static%20factory-%238D75FF)
![picture](https://img.shields.io/badge/Design%20Patterns-Observer-%238D75FF)
![picture](https://img.shields.io/badge/Design%20Patterns-Strategy-%238D75FF)
![picture](https://img.shields.io/badge/Design%20Patterns-Adapter-%238D75FF)
![picture](https://img.shields.io/badge/Design%20Patterns-Facade-%238D75FF)
![picture](https://img.shields.io/badge/Design%20Patterns-Decorator-%238D75FF)




Table of contents.

1. Creational design patterns

    a) Singleton
        
    b) Builder
    
    c) Static factory
    

2. Behavioral

    a) Observer
    
    b) Strategy

3. Structural
    
    a) Adapter

    b) Facade
    
    c) Decorator
    


---

## Creational design patterns


#### Singleton 

Singleton can be used if we want to have only 1 and the same object/instance for the whole progam. For example if we want to create `Logger` object and 
we need only 1 instance of this object, no matter how many times and were we want to use this object. Also, singleton should be used in case if 
our object's state does not depend on place where we use it. Typical singleton uses are e.g. *loggers, repositories, cache system objects* etc.

* **Weak, non-thread-safe singleton implementation:**

```java
    // example of bad singleton design
    
    public class Logger {
    
        private static Logger instance;
    
        private Logger() {}
    
        public static Logger getInstance() {
        
            if (this.instance == null) {
            
                /* 
                    here we have a weakness
                    
                    if the program uses many threads and if one of these threads has already checked above condition, 
                    but haven't createt new Logger object yes (so if our thread is exactly at this position in the code)
                    and also, if another thread is on exactly the same position as well (because none of our both threads 
                    haven't created Logger instance yet) - then, both of threads may create 2 different Logger instances
                */
                instance = new Logger();
                
                return instance;
                
            } else {
                return instance;
            }
        }
    }
```

* **Approach 2 - *Singleton Holder* (Bill Pugh's singleton solution).**

```java
    
    // Proper, thread - safe singleton 
    
    public class Logger {
    
        // private constructor to block typical instantiation via new Logger()
        private Logger() {}
    
        public static Logger getInstance() {
            return SingletonHolder.INSTANCE;
        }
    
        // any method
        public void log() {}
    
        /*
            private, static, final inner class - will be available in outer class at all time
            this class will be loaded only if method Logger.getInstance will be called
            and then SingletonHolder.INSTANCE will instantiate Logger object
        */
        private static class SingletonHolder {
            private static final Logger INSTANCE = new Logger();
        }
    }
```

Usage:

```java
    public class Main {
    
        public static void main(String[] args) {
        
            Logger.getInstance();
            
            Logger.getInstance().log();
        }
    }
```	

* **Approach 3 - *Enum Singleton* (singleton based on Enum)**

```java    
    // Proper, thread - safe singleton 
    public enum Logger {
    
        INSTANCE;
        
        // any method
        public void log() {}
        
    }
```
    
Usage:

```java
    public class Main {
    
        public static void main(String[] args) {
    
            Logger.INSTANCE;
            
            Logger.INSTANCE.log();
        }
    }
```

* * *
*Other singleton examples in Java API:* `java.lang.Runtime.getRuntime, java.awt.Desktop.getDesktop`

---


#### Builder pattern

**Builder pattern** can be used when we have class with multiple parameters in the constructor (especially parameters of the same type) and if we use this constructor especially for instantiate object's properties.

Lets consider below example.

```java

// badly designed class

public class House {

    String adress;
    Integer floorsNumer;
    Integer doorsNumber;

    public House(String adress, Integer floorsNumer, Integer doorsNumber) {
        this.adress = adress;
        this.floorsNumer = floorsNumer;
        this.doorsNumber = doorsNumber;
    }
}
                
public class Main {

    public static void main(String[] args) throws InterruptedException {
        
        // class instantiation [ bad style ]
        
        House house = new House("New York Street", 5, 7);
        
    }
}
```

In this example we have 3 parameters of the same type in the constructor and we use this constructor to set object's properties.

In case if we would have e.g. 5 or 10 parameters, then constructor would be very long and developer would need to pass all these
parameters to the constructor even if we would like to have some of them initialised with default values.

Here we can use **Builder Pattern** and design this class as below.

```java

// class with private constructor and builder as an inner class inside

public class House {

    String adress;
    Integer floorsNumer;
    Integer doorsNumber;

    // private constructor to disallow developer to instantiate class via this constructor
    // this constructor will be used only internally, in the self::build() method

    private House(String adress, Integer floorsNumer, Integer doorsNumber) {
        this.adress = adress;
        this.floorsNumer = floorsNumer;
        this.doorsNumber = doorsNumber;
    }

    // Builder as an inner class inside our outer class

    public static class HouseBuilder {

        // some default values (recommended)
        // here we can set up default values we want to have if they are not settled

        private String adress;
        private Integer floorsNumer = 1;
        private Integer doorsNumber = 1;

        public HouseBuilder setAdress(String adress) {
            this.adress = adress;
            return this;
        }

        public HouseBuilder setFloorsNumer(Integer floorsNumer) {
            this.floorsNumer = floorsNumer;
            return this;
        }

        public HouseBuilder setDoorsNumber(Integer doorsNumber) {
            this.doorsNumber = doorsNumber;
            return this;
        }


        public House build() {

            // Inner class HouseBuilder has an access to provate constructor
            // from outer class House, so we can instantiate House object here

            return new House(adress, floorsNumer, doorsNumber);
        }
    }
}

```

Now we cannot create `House` object via normal contructor because all arguments constructor is private.

```java
public class Main {

    public static void main(String[] args) {
        // this doesn't work
        House house = new House("Address abc", 3, 4);
    }
    
}
```

But we can create new `House` object via Builder as below.

```java
public class Main {

    public static void main(String[] args) {
        
        House house = new House.HouseBuilder()
            .setAdress("New York Street")
            .setDoorsNumber(3)
            .setWindowsNumer(10)
            .build();

    }
}
```

* * *

*Other builder examples in Java API:* `StringBuilder, CallendarBuilder`

---

#### Static factory

**Static Factory** pattern can be used if we want to create any object from another object or just convert any object to another one - with more specific type. 

For exmple, if we have object `Employee` and object `Manager`, then, sometimes we may need to promote normal `Employee` to be a `Manager`, so we could use 
static factory method `Manager.fromEmployee(Employee e)` to be able to easily create `Manager` from `Employee` object.

Another example is if we want to convert elements of 2 types, e.g. XML and JSON, then we could create Static Factory methods in both of these classes, 
for example `XmlElement.from(JsonElement json)` or `JsonElement.from(XmlElement xml)`

Usually we use words *"from", "of", "valueOf", "instanceOf"* to create Static Factory Methods. 


Example:

```java

// class House 

public class House {

    private String adress;
    private Integer floorsNumer;
    private Integer doorsNumber;

    private House(String adress, Integer floorsNumer, Integer doorsNumber) {
        this.adress = adress;
        this.floorsNumer = floorsNumer;
        this.doorsNumber = doorsNumber;
    }

    public static class HouseBuilder {
        // ...
    }

    public String getAdress() {
        return adress;
    }

    public Integer getFloorsNumer() {
        return floorsNumer;
    }

    public Integer getDoorsNumber() {
        return doorsNumber;
    }

}


// class FamilyHouse
// here we have Static Factory Method so we can easily 
// create object of this class based od object of class House

public class FamilyHouse {

    String adress;
    Integer floorsNumer;

    public FamilyHouse(String adress, Integer floorsNumer) {
        this.adress = adress;
        this.floorsNumer = floorsNumer;
    }

    // this is Static Factory Method
    // here we can easily creare object FamilyHouse from object House
    
    public static FamilyHouse from(House house) {
        return new FamilyHouse(house.getAdress(), house.getFloorsNumer());
    }
}
```

Now, if we have object class `House` and we want to have object class `FamilyHouse`, then 
we can easily create such object via **static factory method** in the class `FamilyHouse` as below.

```java
public class Main {

    public static void main(String[] args) {

        FamilyHouse familyHouse = FamilyHouse.from(house);
    }
}
```

* * *
*Other builder examples in Java API:* `BigDecimal.valueOf, Bolean.valueOf`

---

## Behavioral design patterns

#### Observer

Observer can be used everytime when we want to track and react on any event in the application. For example if we have 
button and we want to handle click on that button (and delegate action handling to another class - action handler etc.) or if we have a client and we want to take an action on clien't balance 
change (e.g. log every change on client's balance) 
or if we have any sensor and we want to delegate this sensor event to other place in the system.

In Observer Pattern we usually have a few *observed objects* and one *observer*. In observer pattern we **should not ask observed objects about their state's change** (e.g. in any loop etc.)
but **we should wait for any sign from any observed object** if any state change. For example if we have 10 buttons (observed objects) and 1 observer ("on click" handler class), 
then each from our buttons, should notify observer on every state's change. So observer should be always notified by observed objects.


To implement Observer we can use `Observable` and `Observer` classes from `java.util`.

* **Pseudo - example** (anonymous class based on interface):

```java
    public class Main {
    
        public static void main(String[] args) throws InterruptedException {
        
            // observed element
            Observable observableElement = new Observable() {
        
                // here we have method to notify observers
                // this method should be called everytime when we 
                // want to notify observers about any state change
                
                @Override
                public void notifyObservers(Object o) {
                    super.setChanged();
                    super.notifyObservers(o);
                }
            };
    
            // add observer
            
            observableElement.addObserver(new Observer() {
            
                // update method is called everytime when observer 
                // is notified about any change
                @Override
                public void update(Observable observable, Object o) {
                    System.out.println(o.toString());
                }
                
            });
    
            // add another observer as lambda expression
            observableElement.addObserver((observable, o) -> {/*...*/});
        
            // nottify observers about change in observed object
            observableElement.notifyObservers("test"); 
        
        }
    }

```
 

* **Proper example** (class extends Observable + observers registered as a lambda expression)
 
```java
    
    import java.math.BigDecimal;
    import java.util.Observable;
    
    // observed element 
    // in case of any notification from observed object, we check if we really have 
    // any value change and if so, then we notify all observers about that change 
    
    public class ObservableClientBalance extends Observable {
    
        private BigDecimal oldBalance = BigDecimal.valueOf(0);
    
        public void setNewBalance(BigDecimal newBalance) {
            if (newBalance != oldBalance) {
                super.setChanged();
                super.notifyObservers(newBalance);
                oldBalance = newBalance;
            }
        }
    };
    
    
    public class Main {
    
        public static void main(String[] args) throws InterruptedException {
        
            // observed object
            ObservableClientBalance observableValue = new ObservableClientBalance();
        
            // here we register some observers and their action 
            // observer is functional interface with method Objserver.update 
            // which is called if observer is notified about any change
            
            observableValue.addObserver((observable, o) -> { /* log new and old balance to database */ System.out.println(o.toString()); });
            
            // another observer
            observableValue.addObserver((observable, o) -> { /* send email to the client */ System.out.println(o.toString()); });
        
            // test - in the loop we are changing balance few times (we should see some logs about balance's change)
            for(int i = 0; i < 10; i++){
                Thread.sleep(500);
                observableValue.setNewBalance(BigDecimal.valueOf(122.33));
            }
            
        }
    }

```
     
* * *
*Other observer examples in Java API:* UI events handling in `JavaFX` and `Swing`.

---


#### Strategy

Strategy pattern can be used if we want to ensure many different kind of logic for the same kind of operation but depending on element's type. 
For example if we have `Vehicle` object which may have more subobects for different kind of vehicles, e.g. `Truck extends Vehicle`, `Bus extends Vehicle` and `Car extends Vehicle` and we want to 
create method for replacing tires, then - depending on vehicle type, we would need to use different kind of tires, so we would have one method `Vehicle.replaceTires` 
but this method would behave differently depending on vehicle type. 

Another example would be if we have a few kind of Vat taxes and we want to 
calculate Vat but the tax rate depends on product/service type, then, instead of using e.g. switch-case block, we can use Strategy Design Pattern to 
calculate tax value depending on product or service type.

We use Strategy Pattern if we want to split responsibilities between classes. Strategy is a good way to ensure **Single Responsibility Principle** in the code.

*** 

* Example of **badly designed** class which can be replaced to Stategy Pattern 
design.

    
```java
    
    /**
     * BadlyDesignedVehicle
     *
     * this is an examle of BAD class design
     *
     * here we have a switch-case in all methods, everytime when we want to
     * do something with our vehicle, but all operations we need strogly depend
     * on vehicle's type
     */
    
    class BadlyDesignedVehicle {
    
    
        final public static String VEHICLE_TRUCK = "truck";
        final public static String VEHICLE_CAR = "car";
        final public static String VEHICLE_BUS = "bus";
    
        private String name;
        private String type;
    
        public BadlyDesignedVehicle(String type, String name){
            this.type = type;
            this.name = name;
        }
    
        public void fixIssues() {
            switch(this.type) {
                case VEHICLE_TRUCK: /*do some work */; break;
                case VEHICLE_CAR: /*do some work */; break;
                case VEHICLE_BUS: /*do some work */; break;
            }
        }
    
        public void transportAcrossTheSea() {
            switch(this.type) {
                case VEHICLE_TRUCK: /*do some work */; break;
                case VEHICLE_CAR: /*do some work */; break;
                case VEHICLE_BUS: /*do some work */; break;
            }
        }
    
        public void replaceTires() {
            switch(this.type) {
                case VEHICLE_TRUCK: /*do some work */; break;
                case VEHICLE_CAR: /*do some work */; break;
                case VEHICLE_BUS: /*do some work */; break;
            }
        }
    }

```

* **Good strategy** example

    We have an `Vehicle` object, which can be bus, truck or just car.
    
```java

    package org.bitbucket.slawekhaa.strategy;
    
    
    /**
     * class Vehicle
     *
     * we may have many different kind of Vehicles, so all operations on tires, transport on the
     * ferry and mechanic work are different, depending on Vehicle's type
     *
     * Vehicle class implements interfaces TiresStrategy, FerryTransportStrategy and MechanicStrategy so
     * we force every vehicle to implement methods needed for handling operations like transport across the sea,
     * replace tires and do mechanic work/fix vehicle's issues
     */
    public class Vehicle implements TiresStrategy, FerryTransportStrategy, MechanicStrategy {
    
        /**
         * TiresStrategy object
         * here we have Tires service typical for our object/vehicle type
         * e.g. if our vehicle type is a truck, then our Tires service is
         * just Truck tires service. Vehicle object is not responsible for
         * tires service but only needs to have specific tires service here
         */
        public TiresStrategy tiresStrategy;
    
        /**
         * FerryTransportStrategy object
         *
         * FerryTransportStrategy, like TiresStrategy and MechanicStrategy are interfaces.
         * Here we have concrete FerryTransportStrategy object, suitable to our Vehicle type,
         * e.g. BusFerryTransportStrategy or TruckFerryTransportStrategy etc. so all responsibility
         * for vehicle's ferry transportation service is in this property/object
         */
        public FerryTransportStrategy ferryTransportStrategy;
    
        /**
         * MechanicStrategy object
         *
         * MechanicStrategy is an interface, here, in this property we have concrete
         * mechanic strategy, matching to our Vehicle type, e.g. TruckMechanicStrategy
         * or CarMechanicStrategy or BusMechanicStrategy
         */
        public MechanicStrategy mechanicStrategy;
    
        final public static String VEHICLE_TRUCK = "truck";
        final public static String VEHICLE_CAR = "car";
        final public static String VEHICLE_BUS = "bus";
    
        private String name;
        private String type;
    
        public Vehicle(String type, String name){
            this.type = type;
            this.name = name;
        }
    
        @Override
        public void fixIssues() {
    
            // here we can use method fixIssues() which has different logic
            // depending on object's type (fix truck/car/bus issues needs different kind of tools etc.)
            mechanicStrategy.fixIssues();
    
            // in case if we want to pass our object to the MechanicStrategy object as a parameter,
            // then we can declare default method in the MechanicStrategy interface, so we dont have to overrride
            // this method but we can if we want. Then we can use it and pass current objecet - Vehicle as a parameter
            mechanicStrategy.fixIssues(this);
    
        }
    
        @Override
        public void transportAcrossTheSea() {
    
            // transport accross the sea (current object - Vehicle doesnt need to know what logic is
            // behind this method. Transportation across the sea is different for different kind of vehicles
            // e.g. Tucks need special section and more space on the Ferry etc.). This responsibility has been
            // moved from current object to the TransportStrategy object
            ferryTransportStrategy.transportAcrossTheSea();

        }
    
        @Override
        public void replaceTires() {
    
            // replace tires depending on object type.
            // Responsibility has been moved to TiresStrategy object.
            tiresStrategy.replaceTires();
        }
    
        @Override
        public String toString() {
            return "Vehicle{" +
                    "name='" + name + '\'' +
                    ", type='" + type + '\'' +
                    '}';
        }
    }

```
    
First we need to declare some Strategy interfaces.
    
```java

    package org.bitbucket.slawekhaa.strategy;

    /**
     * TiresStrategy
     */
    public interface TiresStrategy {
        void replaceTires();
    }
    
    /**
     * TiresStrategy
     */
    public interface TiresStrategy {
        void replaceTires();
    }

    /**
     * MechanicStrategy
     */
    public interface MechanicStrategy {
    
        void fixIssues();
    
        // we can also declare default method here with our object as a parameter
        // so we can override this method in specific MechanicStrategy object then
        // to handle operations on our obect.
        // e.g. in TruckMechanicStrategy or BusMechanicStrategy
        // we dont have to implement this method in specific mechanic strategy because it is
        // default interface method, but we can do that if we want to enclose the whole logic
        // responsible for mechanic work in our mechanicStrategy object and use Vehicle object as param
        default void fixIssues(Vehicle vehicle){}
    
    }
```

Now we need to create concrete service for each concrete kind of vehicle, so 
`BusFerryTransportStrategy`, `CarFerryTransportStrategy`, `TruckFerryTransportStrategy` 
for ferry transportation services, `BusMechanicStrategy`, `CarMechanicStrategy`, `TruckMechanicStrategy` 
for mechanic services and `BusTiresStrategy`, `CarTiresStrategy`, `TruckTiresStrategy` for tires services.
    
        
```java

    package org.bitbucket.slawekhaa.strategy;
    
    /*
        -- Ferry Transportation Strategies --
    */
    
    public class BusFerryTransportStrategy implements FerryTransportStrategy {
        @Override
        public void transportAcrossTheSea() {
            System.out.println("Transport bus on ferry");
        }
    }
    
    public class CarFerryTransportStrategy implements FerryTransportStrategy {
        @Override
        public void transportAcrossTheSea() {
            System.out.println("Transport car accross the sea");
        }
    }
    
    public class TruckFerryTransportStrategy implements FerryTransportStrategy {
        @Override
        public void transportAcrossTheSea() {
            System.out.println("Transport truck on ferry");
        }
    }
    
    
    /*
        -- Tires Service Strategies --
    */
    
    public class BusTiresStrategy implements TiresStrategy {
        @Override
        public void replaceTires() {
            System.out.println("Replace tires in the bus");
        }
    }

    public class CarTiresStrategy implements TiresStrategy {
        @Override
        public void replaceTires() {
            System.out.println("Replace tires in the car");
        }
    }

    public class TruckTiresStrategy implements TiresStrategy {
        @Override
        public void replaceTires() {
            System.out.println("Replace tires in the truck");
        }
    }
    
    
    /*
        -- Mechanic Service Strategies --
    */

    public class BusMechanicStrategy implements MechanicStrategy {
    
        @Override
        public void fixIssues() {
            System.out.println("Fix bus issues. Call to buses mechanic.");
        }
    
        // we can also declare method here with our object as a parameter
        // so we can use this method to handle operations on our obect and we don't
        // have to override this method in our Vehicle then
        public void fixIssues(Vehicle vehicle){
            // some logic
            System.out.println("[Bus] Specific fix issues method with Vehicle obj as a param -> " + vehicle.toString());
        }
    }
    
    public class CarMechanicStrategy implements MechanicStrategy {
    
        @Override
        public void fixIssues() {
            System.out.println("Fix car issues. Call to cars mechanic.");
        }
    
        // we can also declare method here with our object as a parameter
        // so we can use this method to handle operations on our obect and we don't
        // have to override this method in our Vehicle then
        public void fixIssues(Vehicle vehicle){
            // some logic
            System.out.println("[car] Specific fix issues method with Vehicle obj as a param -> " + vehicle.toString());
        }
    }
    
    public class TruckMechanicStrategy implements MechanicStrategy {
    
        @Override
        public void fixIssues() {
            System.out.println("Fix truck issues. Call to trucks mechanic.");
        }
    
        // we can also declare method here with our object as a parameter
        // so we can use this method to handle operations on our obect and we don't
        // have to override this method in our Vehicle then
        public void fixIssues(Vehicle vehicle){
            // some logic
            System.out.println("[truck] Specific fix issues method with Vehicle obj as a param -> " + vehicle.toString());
        }
    }
    
```
    
Now we can create any `Vehicle` object and run any kind of service (tires service, mechanical service or order transport our vehicle accross the sea) on 
that object and we don't have to put any logic responsible for those operation to `Vehicle` class.

```java
    
    public class Main {
    
        public static void main(String[] args) {
    
            // we have Vehicle object (truck)
            Vehicle myVehicle = new Vehicle(Vehicle.VEHICLE_TRUCK, "My super truck");
    
            /*
                here we are setting up concrete Tires Service for our Vehicle
                (our vehicle is truck, so we need TruckTiresStrategy which is just
                tires service for trucks)
             */
            myVehicle.tiresStrategy = new TruckTiresStrategy();
    
            /*
                here we are declaring ferry transportation service for our vehicle
                our vehicle is Truck so we use TruckFerryTransportStrategy which is
                concrete kind of transportation service - just for trucks
             */
            myVehicle.ferryTransportStrategy = new TruckFerryTransportStrategy();
    
            /*
                here we want to set up mechanic service for our truck
             */
            myVehicle.mechanicStrategy = new TruckMechanicStrategy();
    
            System.out.println("------ Truck -------");
    
            /*
                now we want to do some work, such us replace tires, transport our vehicle
                accross the sea and fix issues/do some mechanic work
    
                our Vehicle object is not repososible for any of these jobs and dont have to
                know how the tires replacing for trucks look and how the truck can be moved
                accross the sea by ferry. All this logic is encapsulated in the specific
                strategical services which are able handle all these operations depending on
                vehicle's type
             */
            myVehicle.replaceTires();
            myVehicle.transportAcrossTheSea();
            myVehicle.fixIssues();
    
    
            // example with Bus as a Vehicle
            Vehicle myBus = new Vehicle(Vehicle.VEHICLE_BUS, "My bus");
    
            myBus.tiresStrategy = new BusTiresStrategy();
            myBus.ferryTransportStrategy = new BusFerryTransportStrategy();
            myBus.mechanicStrategy = new BusMechanicStrategy();
    
            System.out.println("------ Bus -------");
    
            myBus.replaceTires();
            myBus.transportAcrossTheSea();
            myBus.fixIssues();
            
            return;
            
        }
    }
``` 

* * *
*Other Strategy Pattern examples in Java API:* `javax.swing.text.TextLayoutStrategy` and more.

---

## Structural design patterns


#### Adapter

We use adapter if we want to adjust one object or logic to another. 

Particular case is when we want to map some methods or methods set 
to another method and compose any bigger/more complex service or if we want to map some method's logic to another or extend some logic by additional elements. 

Also we can use Adapter Pattern if we want to extend functionality of any object without doing any modiffication on base object, so for example 
we can use Adapter pattern as a kind of wrapper foe any object or data structure.

**Example** (object as a parameter of another object's constructor with extended functionality)

We have an object *Vehicle* with some functionality inside.

```java

package org.bitbucket.slawekhaa.adapter;

public class Vehicle implements TiresStrategy, FerryTransportStrategy, MechanicStrategy {

    public TiresStrategy tiresStrategy;
    public FerryTransportStrategy ferryTransportStrategy;
    public MechanicStrategy mechanicStrategy;

    final public static String VEHICLE_TRUCK = "truck";
    final public static String VEHICLE_CAR = "car";
    final public static String VEHICLE_BUS = "bus";

    private String name;
    private String type;

    public Vehicle(String type, String name){
        this.type = type;
        this.name = name;
    }

    public void prepareVehicleToUse() {}

    @Override
    public void fixIssues() {
        mechanicStrategy.fixIssues();
    }

    @Override
    public void transportAcrossTheSea() {
        ferryTransportStrategy.transportAcrossTheSea();
    }

    @Override
    public void replaceTires() {
        tiresStrategy.replaceTires();
    }

}

```

If we want to have specific kind of new object, e.g. `CarForHire` which is also kind of `Vehicle` but with some, 
unique functionality, then we can use Adapter Design as in below example.


```java 

/**
 * class CarForHire
 *
 * this is special kind of Vehicle which should have
 * all methods and attributes typical for every vehicle but
 * also some more, unique logic car for hire should have
 */
public class CarForHire {

    /**
     * Vehicle vehicle
     * <p>
     * here we have Vehicle object we want to extend
     * by some new, additional functionality without
     * modifying base object's logic
     */
    private Vehicle vehicle;

    /**
     * object we want to use as a base is injected as a parameter
     *
     * @param vehicle
     */
    public CarForHire(Vehicle vehicle) {
        this.vehicle = vehicle;
    }

    /**
     * hireToClient
     */
    public void hireToClient() {
        vehicle.prepareVehicleToUse();
        // (...) do some other logic unique for "car for hire"

    }

    /**
     * getHireStatistics
     *
     * @return
     */
    public Object getHireStatistics() {
        // (...) some logic
        return null;
    }
}

```

This approach is very convinient because we dont have to worry about the `Vehicle` object structure and logic. 
We just use `Vehicle` object's methods and add some, uniqe and additional logic typical for "car for hire".

***

#### Facade

Facade Design Pattern encapsulates a complex subsystem behind a simple interface. It hides much of the complexity and makes the subsystem easy to use.
For example if we have banking system, then we can publish some simple methods with advanced logic behind, for example method 
`doTransfer(User user, Account toAccount, BigDecimal amount)` could be very simple but logic behind could be advanced and complex. We can publish 
`doTransfer` method, so client or API user could use it simply and wont be obligated to know the whole, advanced logic behind.

By using Facade Pattern we can simplify our API and make it easily usable by a client. Facede should ensure one entry point to extended functionality or module.
We can also split application for separate layers such us e.g. external, client's layer + backend layer or business layer + process layer.

**Example**

Lets consider previous `Vehicle` class.

```java

public class Vehicle implements TiresStrategy, FerryTransportStrategy, MechanicStrategy {

    public TiresStrategy tiresStrategy;
    public FerryTransportStrategy ferryTransportStrategy;
    public MechanicStrategy mechanicStrategy;

    final public static String VEHICLE_TRUCK = "truck";
    final public static String VEHICLE_CAR = "car";
    final public static String VEHICLE_BUS = "bus";

    private String name;
    private String type;
    private Color color;
    private Trait trait = null;

    public Vehicle(String type, String name){
        this.type = type;
        this.name = name;
    }

    @Override
    public void fixIssues() {
        mechanicStrategy.fixIssues();
    }

    @Override
    public void transportAcrossTheSea() {
        ferryTransportStrategy.transportAcrossTheSea();
    }

    @Override
    public void replaceTires() {
        tiresStrategy.replaceTires();
    }

    public void setColor(Color color) {
        this.color = color;
    }

    public void setTrait(Trait trait) {
        this.trait = trait;
    }
}
```

Some additional classes as below.

```java

class Color {
    private String color;
    Color(String color){
        this.color = color;
    }
}

class Trait {
    final public static String BIG_TRAIT = "big";
    final public static String SMALL_TRAIT = "small";
    private String type;
    Trait(String type){
        this.type = type;
    }
};

/**
 * VehiclesDatabase
 *
 * this is just a mock of normal DB service
 * this mock allows us to simulate normal behaviour
 *
 */
class VehiclesDatabase{

    private ArrayList<Vehicle> vehicles = new ArrayList<Vehicle>(){{
        add(new Vehicle(Vehicle.VEHICLE_TRUCK, "Truck #serial no. 1"));
        add(new Vehicle(Vehicle.VEHICLE_BUS, "Bus #serial no. 1"));
    }};

    public void addVehicle(Vehicle vehicle){
        this.vehicles.add(vehicle);
    }

}

/**
 * VehiclesCreator class
 * 
 * class is responsible for handling new Vehicles
 */
class VehiclesCreator{
    
    public Vehicle create(String type) throws Exception {

        String name;

        switch(type) {
            case Vehicle.VEHICLE_BUS: name = "Bus"; break;
            case Vehicle.VEHICLE_CAR: name = "Car"; break;
            case Vehicle.VEHICLE_TRUCK: name = "Truck"; break;
            default: throw new Exception("Unknown vehicle type");
        }

        Vehicle vehicle = new Vehicle(type, name);

        vehicle.setColor(new Color("black"));

        vehicle.setTrait(new Trait(Trait.BIG_TRAIT));

        return vehicle;

    }
}

```

Now we can create `API Facade` if we want to give our client or developers posibility to create new object but also 
hide all advanced logic behind this operation like in below example.

```java

public class ApiFacade {

    /**
     * VehiclesDatabase mock
     */
    VehiclesDatabase vehiclesDb = new VehiclesDatabase();

    /**
     * VehiclesCreator
     */
    VehiclesCreator vehiclesCreator = new VehiclesCreator();

    /**
     * createVehicles
     *
     * This is Facade method - all advanced and complex logic
     * for creating trucks is behind this method and client does not know
     * this logic. Client can just use this method to create 100 or 200 new Trucks
     *
     * @param numberOfVehicles
     * @return ArrayList<Vehicle>
     */
    public ArrayList<Vehicle> createTrucks(int numberOfVehicles) throws Exception {

        ArrayList<Vehicle> vehicles = new ArrayList<>();

        for(int i = 1; i<= numberOfVehicles; i++){
            /*
                some advanced logic in vehiclesCreator
             */
            Vehicle newVehicle = vehiclesCreator.create(Vehicle.VEHICLE_TRUCK);

            /*
                second part of logic, fix issues in vehicle, replace tires,
                prepare vehicle to be provided
             */
            newVehicle.fixIssues();
            newVehicle.replaceTires();

            /*
                add new vehicle to vehicles register/DB
             */
            vehicles.add(newVehicle);

            vehiclesDb.addVehicle(newVehicle);
        }

        /*
            return/provide all vehicles
         */
        return vehicles;
    }
   
}
```

Now client or developer can easily create e.g. 10 new `Trucks` and don't have to worry about the logic behind this.

For example:

```java

public class Main {

    public static void main(String[] args) throws InterruptedException {
        
        /*
            here we can easily create e.g. 100 new Trucks without
            taking into consideration what kind of logic is behind this
         */
        ApiFacade facade = new ApiFacade();
        ArrayList<Vehicle> listOfTucks = facade.createTrucks(100);
        
    }
}
```

***

#### Decorator

Decorator pattern allows a user to add new functionality to an existing object without altering its structure. 
This pattern acts as a wrapper to existing class. For example if we have `Coffee` and `Milk` and `Sugar` and `Carmel`, and we would 
like to have a `Coffee` with `Sugar`, `Milk` and `Carmel`, then, we would need to **decorate**  `Milk` by `Sugar` and then decorate 
`Milk` with `Sugar` by `Carmel`. In this particular example the order of decoration doesn't matter because finally wa want to have 
`Milk` with `Sugar` and `Carmel`;

Decorator can be used to dynamically extend objecte's functionality by wrapping one object by another.

**Example of decorator**

We have general object interface which should be implemented by our main object and also all decorator objects.

```java
/**
 * Drink interface
 *
 * this is common interface which should be implemented
 * by each from our abstract decorator class (to ensure that every decoration
 * has getDrink() method and also by our main object). This gives us sure that
 * we can get a drink from every decoration object and also from our main object
 */
interface Drink {
    String getDrink();
}
```

Also we have 2 different basic objects implement this interface.

```java
/**
 * CoffeeImpl class
 *
 * this is our drink - coffee
 */
class CoffeeImpl implements Drink {

    final private String DRINK = "Coffee";

    @Override
    public String getDrink() {
        return DRINK;
    }
}

/**
 * TeaImpl class
 *
 * another drink - tea
 */
class TeaImpl implements Drink {

    final private String DRINK = "Tea";

    @Override
    public String getDrink() {
        return DRINK;
    }
}
```

Now we need to create **abstract decoration** class which gives us posibility to 
chain decorations based on our main object.

```java

/**
 * Abstract decorator class
 *
 * this class will be used/extended to ensure Decoration posibility
 * this have to implement interface Drink to ensure that we can build any Drink
 * based on another one and finally we can get our final drink by calling getDrink method
 * on any decoration or any decorations chain
 *
 * This abstract class gives us posibility to create kind of decorations chain
 *
 */
abstract class DrinkDecorator implements Drink {

    private Drink drink;

    DrinkDecorator(Drink drink) {
        this.drink = drink;
    }

    /**
     * getDrink
     *
     * this methods sum/join all ingridients from decoration
     * object with currect/existing ones
     *
     * @return String
     */
    @Override
    public String getDrink() {
        StringBuilder sb = new StringBuilder();
        sb.append(drink.getDrink());
        sb.append(" with ");

        /*
            here we sum ingridients by using abstract method getIngredients
            this method doesn't exist yet but it have to be implemented in
            every decoration class e.g. SugarDrink or MilkDrink
         */
        sb.append(getIngredients());
        return sb.toString();
    }

    /**
     * getIngredients
     *
     * Important abstract method
     * We can use this metho in getDrink() method even if it doesnt exist yet
     * but also we are sure that thic method is implemented in every decoration class
     *
     * Method is abstract and protected so we can use this method in decoration's object
     * context (e.g. SugarDrink or MilkDrink)
     *
     * @return String
     */
    abstract protected String getIngredients();

}

```

Now we can create some decoration classes. Each one from them should extend our *abstract decoration class* as below.

```java

/**
 * SugarDrink class
 *
 * this is our decorator class - we can use this class
 * to add some sugar to our dring
 *
 */
class SugarDrink extends DrinkDecorator {

    private static final String INGREDIENT_SUGAR = "sugar";

    public SugarDrink(Drink drink) {
        /*
            we should call partent constructor to make sure
            that we have current Dring's reference here
         */
        super(drink);
    }

    /**
     * getIngredients
     *
     * this is an implementation of getIngredients required in abstract
     * superclass. Here we have concrete implementation unique for this
     * decoration. In this example we just return "sugar" because this
     * decorator adds sugar to our drink
     *
     * @return String
     */
    @Override
    protected String getIngredients() {
        return INGREDIENT_SUGAR;
    }
}

/**
 * class CarmelDrink
 *
 * Our second decorator class - we use it to decorate
 * our drink by Carmel (to add Carmel to our Drink)
 */
class CarmelDrink extends DrinkDecorator {

    private static final String INGREDIENT_CARMEL = "carmel";

    public CarmelDrink(Drink drink) {
        /*
            we should call partent constructor to make sure
            that we have current Dring's reference here
         */
        super(drink);
    }

    /**
     * getIngredients
     *
     * this is an implementation of getIngredients required in abstract
     * superclass. Here we have concrete implementation unique for this
     * decoration. In this example we just return "carmel" because this
     * decorator adds carmel to our drink
     *
     * @return String
     */
    @Override
    protected String getIngredients() {
        return INGREDIENT_CARMEL;
    }
}

/**
 * class MilkDrink
 *
 * Milk decorator class
 */
class MilkDrink extends DrinkDecorator {

    private static final String INGREDIENT_MILK = "milk";

    public MilkDrink(Drink drink) {
        super(drink);
    }

    @Override
    protected String getIngredients() {
        return INGREDIENT_MILK;
    }
}


```

Now, if we have any `Drink`, e.g. object `TeaImpl` or `CoffeeImpl`, then we 
can decorate this drink multiple times by any decoration, e.g. `MilkDrink`, `CarmelDrink`, `SugarDrink` or any other, for example:

```java 

public class Main {

    public static void main(String[] args) throws InterruptedException {


        CoffeeImpl coffee = new CoffeeImpl();
        TeaImpl tea = new TeaImpl();

        // chain of decorations
        // we have coffee but we want to add/decorate this coffee by sugar and carmel
        // result: "My dring is: Coffee with carmel with milk with sugar"
        System.out.println("My dring is: " +
                new SugarDrink(new MilkDrink(new CarmelDrink(coffee))).getDrink()
        );

        // chain of decorations
        // result: "My second dring is: Tea with carmel with sugar"
        System.out.println("My second dring is: " +
                new SugarDrink(new CarmelDrink(tea)).getDrink()
        );
        
    }
}

```



This is how can we use Decorator Design Pattern to dynamically wrap any object/logic by another with new functiolalities, properties or states.

* * *
*Other Decorator Pattern examples in Java API:* `java.io.InputStream`, `java.io.FileInputStream`, other input streams.

## Authors

* **Slawomir Hadas** - *author* - [Github](https://github.com/hadasbro)
