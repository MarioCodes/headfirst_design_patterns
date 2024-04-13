# Software architecture patterns - implemented in csharp

*(disclaimer: all this info is for personal use and comes from my understanding from the book [Head First Design Patterns](https://www.oreilly.com/library/view/head-first-design/0596007124/). For more info or details please purchase the book to support the original authors)*

## Design principles  
* Identify the aspects of your application that vary and separate them from what stays the same
* Program to an interface, not an implementation
* Favor composition over inheritance -> has-a may be better than is-a
* Strive for loosely coupled designs between objects that interact
* Classes should be open for extension, but closed for modification

## Strategy pattern
Define una familia de algoritmos, encapsulando cada uno de ellos y los hace intercambiables en runtime (configuración), independientemente de los clientes que los utilizan.  
Útil para cambiar algoritmos en tiempo de ejecución y elegir la estrategia más adecuada según el contexto. 

![strategy pattern class diagram](_images/strategy_pattern.drawio.png)

*code example - how to use it!*
~~~ csharp
Duck duck = new MallardDuck(new Quack(), new FlyNoWay());
duck.PerformQuack(); // quacks like a duck
duck.PerformFly(); // cannot fly
duck.Display(); // looks like a MallardDuck

Duck duck2 = new MallardDuck(new MuteQuack(), new FlyWithWings());
duck2.PerformQuack(); // <<silence>>
duck2.PerformFly(); // flying with wings
duck2.Display(); // looks like a MallardDuck

Duck duck3 = new RedheadDuck(new Squak(), new FlyWithWings());
duck3.PerformQuack(); // squeaks
duck3.PerformFly(); // flying with wings
duck3.Display(); // looks like a Redhead Duck
~~~

## Observer pattern
Define una relacion, de una a muchos, entre un sujeto con estado y sus observadores de manera que, cuando el sujeto cambia de estado, todos sus observadores son notificados y actualizados automáticamente.  

Facilita la notificación de cambios en un objeto a múltiples observadores. 

![observer pattern class diagram](_images/observer_pattern.drawio.png)

*code example - how to use it!*
~~~ csharp
WeatherData weatherData = new WeatherData();
DisplayElement currentDisplay = new CurrentConditionsDisplay(weatherData);
DisplayElement forecastDisplay = new ForecastDisplay(weatherData);

// SetMeasurements() calls NotifyObservers() and this calls Update() for all observers
// on Update() it calls Display() to see the change. this prints:
weatherData.SetMeasurements(80, 65, 30.4f);
//   currentConditionsDisplay: temp 80, humidity 65%
//   forecast display based on pressure: temp 80, humidity 65%

weatherData.SetMeasurements(40, 25, 28.5f);
//   currentConditionsDisplay: temp 40, humidity 25%
//   forecast display based on pressure: temp 40, humidity 25%
~~~

## Decorator pattern
Permite añadir nuevas funcionalidades a objetos de forma dinámica sin modificar su estructura interna.  
Funciona mediante wrappers que agregan comportamientos antes o después de delegar llamadas al objeto original.  

![decorator pattern class diagram](_images/decorator_pattern.drawio.png)

*code example - how to use it!*
~~~ csharp
Beverage beberage = new Espresso();
Console.WriteLine($"{beverage.Description} {beverage.Cost()}");
// Espresso $1,99

Beverage2 = new DarkRoast();
beverage2 = new Mocha(beverage2);
Console.WriteLine($"{beverage2.Description} {beverage2.Cost()}");
// Dark roast, Mocha $1,45

Beverage3 = new HouseBlend();
beverage3 = new Soy(beverage3);
beverage3 = new Mocha(beverage3);
beverage3 = new Whip(beverage3);
Console.WriteLine($"{beverage3.Description} {beverage3.Cost()}");
// House blend coffee, Soy, Mocha, Whip $1,74
~~~

## Factory pattern
Define una interfaz para crear un objeto, pero deja a las subclases decidir que tipo instanciar. Permite a una clase diferir la instanciación a sus subclases. 
Útil para cuando se trabaja con familias de objetos relacionados y se desea que todos los objetos que tienen que cooperar entre sí, provengan de la misma familia o tipo.  
Permite utilizar diferentes implementaciones para diferentes situaciones.  

![factory pattern class diagram](_images/factory_pattern.drawio.png)

*code example - how to use it*
~~~ csharp
PizzaStore nyStore = new NYPizzaStore();
PizzaStore chicagoStore = new ChicagoPizzaStore();

Pizza pizza = nyStore.OrderPizza("clam");
Pizza pizza = chicagoStore.OrderPizza("clam");
// ny pizza: New york style clam pizza w. thin crust dough, marinara sauce, reggiano cheese, fresh clams
// chicago pizza: Chicago style clam pizza w. thick crust dough, barbaque sauce, emmental cheese, frozen clams
~~~

## Singleton pattern
Asegura que una clase tiene una única instancia y ofrece un punto de acceso global a la misma

*code example - how to use it!*
~~~ csharp
public class SingletonService
{
  // eager loading because this is thread-safe
  private static SingletonService uniqueInstance = new SingletonService();

  // private constructor so there can be no initialization outside this class
  private SingletonService() {}

  private static SingletonService GetInstance()
  {
    return uniqueInstance();
  }
}
~~~

## Command pattern
Encapsula la invocación de métodos en un objeto independiente. Esto permite que los métodos de los Productores sean independientes de los métodos de los Consumidores.  
Útil cuando los productores son diferentes entre sí. 

![command pattern class diagram](_images/command_pattern.drawio.png)

*code example - how to use it!*
~~~ csharp
// set up
RemoteControl remote = new RemoteControl();

Light livingRoomLight = new Light("living room");
Light kitchenLight = new Light("kitchen");
GarageDoor garageDoor = new GarageDoor("");

// light commands
var livingRoomLightOn = new LightOnCommand(livingRoomLight);
var livingRoomLightOff = new LightOffCommand(livingRoomLight);
var kitchenLightOn = new LightOnCommand(kitchenLight);
var kitchenLightOff = new LightOffCommand(kitchenLight);

// garage door commands
var garageDoorUp = new GarageDoorUpCommand(garageDoor);
var garageDoorDown = new GarageDoorDownCommand(garageDoor);

// load commands into programmable remote
remote.SetCommand(0, livingRoomLightOn, livingRoomLightOff);
remote.SetCommand(1, kitchenLightOn, kitchenLightOff);
remote.SetCommand(2, garageDoorUp, garageDoorDown);

// push buttons
remote.OnButtonWasPushed(0); // living room light is on
remote.OffButtonWasPushed(0); // living room light is off

remote.OnButtonWasPushed(1); // kitchen light is on
remote.OffButtonWasPushed(1); // kitchen light is off

remote.OnButtonWasPushed(2); // garage door opens
remote.OffButtonWasPushed(2); // garage door closes
~~~

## Adapter pattern
Permite adaptar un diseño que espera una interfaz, a una clase que implementa otra interfaz completamente diferente. Permite trabajar juntas a clases que de otro modo serían incompatibles.

![adapter pattern class diagram](_images/adapter_pattern.drawio.png)

*code example - how to use it!*
~~~ csharp
// we have a Duck interface which Quacks
Duck duck = new MallardDuck();
duck.Quack();

// we have a Turkey interface which Gobbles
Turkey turkey = new WildTurkey();
turkey.Gobble();

// now we have a Turkey which knows how to Quack
Duck turkeyAdapter = new TurkeyAdapter(turkey);
turkeyAdapter.Quack();
~~~

## Façade pattern
Wrapper de objetos para simplificar su interfaz. 
