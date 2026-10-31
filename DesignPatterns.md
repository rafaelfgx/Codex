# Design Patterns

The 23 Gang of Four design patterns, defined in the book "Design Patterns: Elements of Reusable Object-Oriented Software".

## Behavioral

**1. Chain of Responsibility**

Avoid coupling the sender of a request to its receiver by giving more than one object a chance to handle the request. Chain the receiving objects and pass the request along the chain until an object handles it.

```java
record Request() { }

abstract class Handler {
    private Handler successor;

    void setSuccessor(final Handler successor) {
        this.successor = successor;
    }

    abstract void handle(final Request request);
}

final class ConcreteHandlerA extends Handler {
    void handle(final Request request) { }
}

final class ConcreteHandlerB extends Handler {
    void handle(final Request request) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var handler = new ConcreteHandlerA();
        final var successor = new ConcreteHandlerB();
        handler.setSuccessor(successor);
        handler.handle(new Request());
    }
}
```

```java
record ExpenseRequest(int amount) { }

abstract class ExpenseApprover {
    private ExpenseApprover successor;

    void setSuccessor(final ExpenseApprover successor) {
        this.successor = successor;
    }

    void handle(final ExpenseRequest request) {
        if (canApprove(request)) {
            approve(request);
            return;
        }

        if (successor != null) {
            successor.handle(request);
        }
    }

    abstract boolean canApprove(final ExpenseRequest request);

    abstract void approve(final ExpenseRequest request);
}

final class TeamLeadApprover extends ExpenseApprover {
    boolean canApprove(final ExpenseRequest request) {
        return request.amount() <= 500;
    }

    void approve(final ExpenseRequest request) { }
}

final class ManagerApprover extends ExpenseApprover {
    boolean canApprove(final ExpenseRequest request) {
        return request.amount() <= 2000;
    }

    void approve(final ExpenseRequest request) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var teamLead = new TeamLeadApprover();
        final var manager = new ManagerApprover();
        teamLead.setSuccessor(manager);
        teamLead.handle(new ExpenseRequest(1200));
    }
}
```

**2. Command**

Encapsulate a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations.

```java
interface Command {
    void execute();
}

record Receiver() {
    void action() { }
}

record ConcreteCommand(Receiver receiver) implements Command {
    public void execute() { }
}

record Invoker(Command command) {
    void invoke() {
        command.execute();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var command = new ConcreteCommand(new Receiver());
        final var invoker = new Invoker(command);
        invoker.invoke();
    }
}
```

```java
interface DeviceCommand {
    void execute();
}

final class SmartLight {
    void turnOn() { }

    void turnOff() { }
}

record TurnOnLightCommand(SmartLight light) implements DeviceCommand {
    public void execute() {
        light.turnOn();
    }
}

record RemoteControl(DeviceCommand command) {
    void pressButton() {
        command.execute();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var light = new SmartLight();
        final var command = new TurnOnLightCommand(light);
        final var remote = new RemoteControl(command);
        remote.pressButton();
    }
}
```

**3. Interpreter**

Given a language, define a representation for its grammar along with an interpreter that uses the representation to interpret sentences in the language.

```java
record Context() { }

interface Expression {
    void interpret(final Context context);
}

final class TerminalExpression implements Expression {
    public void interpret(final Context context) { }
}

record NonterminalExpression(Expression expression) implements Expression {
    public void interpret(final Context context) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var context = new Context();
        final var expression = new NonterminalExpression(new TerminalExpression());
        expression.interpret(context);
    }
}
```

```java
record OrderContext(boolean paid, boolean inStock) { }

interface OrderRule {
    boolean interpret(final OrderContext context);
}

final class PaidRule implements OrderRule {
    public boolean interpret(final OrderContext context) {
        return context.paid();
    }
}

final class InStockRule implements OrderRule {
    public boolean interpret(final OrderContext context) {
        return context.inStock();
    }
}

record AndRule(OrderRule left, OrderRule right) implements OrderRule {
    public boolean interpret(final OrderContext context) {
        return left.interpret(context) && right.interpret(context);
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var rule = new AndRule(new PaidRule(), new InStockRule());
        rule.interpret(new OrderContext(true, true));
    }
}
```

**4. Iterator**

Provide a way to access the elements of an aggregate object sequentially without exposing its underlying representation.

```java
interface Iterator {
    boolean hasNext();

    Object next();
}

interface Aggregate {
    Iterator iterator();
}

final class ConcreteAggregate implements Aggregate {
    public Iterator iterator() {
        throw new UnsupportedOperationException();
    }
}

record ConcreteIterator(Aggregate aggregate) implements Iterator {
    public boolean hasNext() {
        throw new UnsupportedOperationException();
    }

    public Object next() {
        throw new UnsupportedOperationException();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var aggregate = new ConcreteAggregate();
        final var iterator = new ConcreteIterator(aggregate);
        iterator.hasNext();
    }
}
```

```java
record Playlist(String[] songs) { }

final class PlaylistIterator implements Iterator {
    private final Playlist playlist;
    private int index;

    PlaylistIterator(final Playlist playlist) {
        this.playlist = playlist;
    }

    public boolean hasNext() {
        return index < playlist.songs().length;
    }

    public Object next() {
        return playlist.songs()[index++];
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var playlist = new Playlist(new String[] {"Intro", "Build", "Finale"});
        final Iterator iterator = new PlaylistIterator(playlist);
        while (iterator.hasNext()) {
            iterator.next();
        }
    }
}
```

**5. Mediator**

Define an object that encapsulates how a set of objects interact. Mediator promotes loose coupling by keeping objects from referring to each other explicitly, and lets you vary their interaction independently.

```java
interface Mediator {
    void notify(final Colleague colleague);
}

abstract class Colleague {
    private final Mediator mediator;

    Colleague(final Mediator mediator) {
        this.mediator = mediator;
    }
}

final class ConcreteColleagueA extends Colleague {
    ConcreteColleagueA(final Mediator mediator) {
        super(mediator);
    }
}

final class ConcreteColleagueB extends Colleague {
    ConcreteColleagueB(final Mediator mediator) {
        super(mediator);
    }
}

final class ConcreteMediator implements Mediator {
    public void notify(final Colleague colleague) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var mediator = new ConcreteMediator();
        final var colleague = new ConcreteColleagueA(mediator);
        mediator.notify(colleague);
    }
}
```

```java
interface ChatMediator {
    void send(final String message, final ChatUser user);
}

record ChatUser(String name, ChatMediator mediator) {
    void send(final String message) {
        mediator.send(message, this);
    }
}

final class ChatRoom implements ChatMediator {
    public void send(final String message, final ChatUser user) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var room = new ChatRoom();
        final var alice = new ChatUser("Alice", room);
        alice.send("Order #42 is ready.");
    }
}
```

**6. Memento**

Without violating encapsulation, capture and externalize an object's internal state so that the object can be restored to this state later.

```java
record Memento() { }

final class Originator {
    Memento save() {
        throw new UnsupportedOperationException();
    }

    void restore(final Memento memento) { }
}

record Caretaker(Originator originator) { }

public final class Demo {
    public static void main(final String[] args) {
        final var originator = new Originator();
        final var caretaker = new Caretaker(originator);
        final var memento = originator.save();
        originator.restore(memento);
    }
}
```

```java
record EditorSnapshot(String text) { }

final class TextEditor {
    private String text = "";

    EditorSnapshot save() {
        return new EditorSnapshot(text);
    }

    void restore(final EditorSnapshot snapshot) {
        text = snapshot.text();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var editor = new TextEditor();
        final var snapshot = editor.save();
        editor.restore(snapshot);
    }
}
```

**7. Observer**

Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

```java
interface Observer {
    void update();
}

interface Subject {
    void attach(final Observer observer);

    void detach(final Observer observer);

    void notifyObservers();
}

final class ConcreteSubject implements Subject {
    public void attach(final Observer observer) { }

    public void detach(final Observer observer) { }

    public void notifyObservers() { }
}

record ConcreteObserver() implements Observer {
    public void update() { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var subject = new ConcreteSubject();
        final var observer = new ConcreteObserver();
        subject.attach(observer);
        subject.notifyObservers();
    }
}
```

```java
interface WeatherObserver {
    void update(final int temperature);
}

final class WeatherStation {
    void attach(final WeatherObserver observer) { }

    void notifyObservers(final int temperature) { }
}

record DisplayPanel() implements WeatherObserver {
    public void update(final int temperature) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var station = new WeatherStation();
        station.attach(new DisplayPanel());
        station.notifyObservers(28);
    }
}
```

**8. State**

Allow an object to alter its behavior when its internal state changes. The object will appear to change its class.

```java
interface State {
    void handle(final Context context);
}

final class Context {
    private State state;

    Context(final State state) {
        this.state = state;
    }

    void request() {
        state.handle(this);
    }
}

final class ConcreteStateA implements State {
    public void handle(final Context context) { }
}

final class ConcreteStateB implements State {
    public void handle(final Context context) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var context = new Context(new ConcreteStateA());
        context.request();
    }
}
```

```java
interface OrderState {
    void handle(final OrderProcess context);
}

final class OrderProcess {
    private OrderState state;

    OrderProcess(final OrderState state) {
        this.state = state;
    }

    void next(final OrderState nextState) {
        this.state = nextState;
    }

    void request() {
        state.handle(this);
    }
}

final class PendingPayment implements OrderState {
    public void handle(final OrderProcess context) { }
}

final class ReadyToShip implements OrderState {
    public void handle(final OrderProcess context) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var order = new OrderProcess(new PendingPayment());
        order.request();
    }
}
```

**9. Strategy**

Defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients who use it.

```java
interface Strategy {
    void execute();
}

record Context(Strategy strategy) {
    void execute() { }
}

final class ConcreteStrategyA implements Strategy {
    public void execute() { }
}

final class ConcreteStrategyB implements Strategy {
    public void execute() { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var context = new Context(new ConcreteStrategyA());
        context.execute();
    }
}
```

```java
interface PaymentStrategy {
    void pay(final int amount);
}

final class CreditCardPayment implements PaymentStrategy {
    public void pay(final int amount) { }
}

final class PixPayment implements PaymentStrategy {
    public void pay(final int amount) { }
}

record Checkout(PaymentStrategy strategy) {
    void pay(final int amount) {
        strategy.pay(amount);
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var checkout = new Checkout(new PixPayment());
        checkout.pay(250);
    }
}
```

**10. Template Method**

Define a skeleton of an algorithm in an operation, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithms structure.

```java
abstract class AbstractClass {
    final void templateMethod() { }

    abstract void primitiveOperationA();

    abstract void primitiveOperationB();
}

final class ConcreteClass extends AbstractClass {
    void primitiveOperationA() { }

    void primitiveOperationB() { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var instance = new ConcreteClass();
        instance.templateMethod();
    }
}
```

```java
abstract class ReportGenerator {
    final void generate() {
        collectData();
        formatReport();
        sendReport();
    }

    abstract void collectData();

    abstract void formatReport();

    abstract void sendReport();
}

final class SalesReportGenerator extends ReportGenerator {
    void collectData() { }

    void formatReport() { }

    void sendReport() { }
}

public final class Demo {
    public static void main(final String[] args) {
        new SalesReportGenerator().generate();
    }
}
```

**11. Visitor**

Represent an operation to be performed on the elements of an object structure. Visitor lets you define a new operation without changing the classes of the elements on which it operates.

```java
interface Visitor {
    void visitConcreteElementA(final ConcreteElementA element);

    void visitConcreteElementB(final ConcreteElementB element);
}

interface Element {
    void accept(final Visitor visitor);
}

final class ConcreteElementA implements Element {
    public void accept(final Visitor visitor) { }
}

final class ConcreteElementB implements Element {
    public void accept(final Visitor visitor) { }
}

final class ConcreteVisitor implements Visitor {
    public void visitConcreteElementA(final ConcreteElementA element) { }

    public void visitConcreteElementB(final ConcreteElementB element) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var visitor = new ConcreteVisitor();
        final var element = new ConcreteElementA();
        element.accept(visitor);
    }
}
```

```java
interface InvoiceItem {
    void accept(final InvoiceVisitor visitor);
}

interface InvoiceVisitor {
    void visit(final ProductLine productLine);

    void visit(final DiscountLine discountLine);
}

record ProductLine(int value) implements InvoiceItem {
    public void accept(final InvoiceVisitor visitor) {
        visitor.visit(this);
    }
}

record DiscountLine(int value) implements InvoiceItem {
    public void accept(final InvoiceVisitor visitor) {
        visitor.visit(this);
    }
}

final class TotalVisitor implements InvoiceVisitor {
    public void visit(final ProductLine productLine) { }

    public void visit(final DiscountLine discountLine) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var visitor = new TotalVisitor();
        new ProductLine(100).accept(visitor);
    }
}
```

## Creational

**12. Abstract Factory**

Provide an interface for creating families of related or dependent objects without specifying their concrete classes.

```java
interface ProductA { }

interface ProductB { }

interface AbstractFactory {
    ProductA createProductA();

    ProductB createProductB();
}

record ConcreteProductA() implements ProductA { }

record ConcreteProductB() implements ProductB { }

final class ConcreteFactory implements AbstractFactory {
    public ProductA createProductA() {
        throw new UnsupportedOperationException();
    }

    public ProductB createProductB() {
        throw new UnsupportedOperationException();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var factory = new ConcreteFactory();
        factory.createProductA();
        factory.createProductB();
    }
}
```

```java
interface Button { }

interface Checkbox { }

interface GuiFactory {
    Button createButton();

    Checkbox createCheckbox();
}

record WindowsButton() implements Button { }

record WindowsCheckbox() implements Checkbox { }

final class WindowsFactory implements GuiFactory {
    public Button createButton() {
        return new WindowsButton();
    }

    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var factory = new WindowsFactory();
        factory.createButton();
        factory.createCheckbox();
    }
}
```

**13. Builder**

Separate the construction of a complex object from its representation so that the same construction processes can create different representations.

```java
record Product() { }

interface Builder {
    void buildPartA();

    void buildPartB();

    Product build();
}

final class ConcreteBuilder implements Builder {
    public void buildPartA() { }

    public void buildPartB() { }

    public Product build() {
        throw new UnsupportedOperationException();
    }
}

record Director(Builder builder) {
    void construct() { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var builder = new ConcreteBuilder();
        final var director = new Director(builder);
        director.construct();
        final var product = builder.build();
    }
}
```

```java
record Computer(String processor, String memory, String storage) { }

interface ComputerBuilder {
    void setProcessor(final String processor);

    void setMemory(final String memory);

    void setStorage(final String storage);

    Computer build();
}

final class GamingComputerBuilder implements ComputerBuilder {
    private String processor;
    private String memory;
    private String storage;

    public void setProcessor(final String processor) { this.processor = processor; }

    public void setMemory(final String memory) { this.memory = memory; }

    public void setStorage(final String storage) { this.storage = storage; }

    public Computer build() {
        return new Computer(processor, memory, storage);
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var builder = new GamingComputerBuilder();
        builder.setProcessor("Ryzen 7");
        builder.setMemory("32 GB");
        builder.setStorage("1 TB SSD");
        builder.build();
    }
}
```

**14. Factory Method**

Define an interface for creating an object, but let the subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.

```java
interface Product { }

abstract class Creator {
    abstract Product factoryMethod();

    void operation() { }
}

record ConcreteProduct() implements Product { }

final class ConcreteCreator extends Creator {
    Product factoryMethod() {
        throw new UnsupportedOperationException();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var creator = new ConcreteCreator();
        final var product = creator.factoryMethod();
    }
}
```

```java
interface Notification {
    void send();
}

abstract class NotificationCreator {
    abstract Notification createNotification();

    void sendNotification() {
        createNotification().send();
    }
}

record EmailNotification() implements Notification {
    public void send() { }
}

final class EmailNotificationCreator extends NotificationCreator {
    Notification createNotification() {
        return new EmailNotification();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        new EmailNotificationCreator().sendNotification();
    }
}
```

**15. Prototype**

Specify the kinds of objects to create using a prototypical instance, and create new objects by copying this prototype.

```java
interface Prototype {
    Prototype clone();
}

final class ConcretePrototype implements Prototype {
    public Prototype clone() {
        throw new UnsupportedOperationException();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var prototype = new ConcretePrototype();
        final var copy = prototype.clone();
    }
}
```

```java
record DocumentTemplate(String title, String body) implements Prototype {
    public Prototype clone() {
        return new DocumentTemplate(title, body);
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var template = new DocumentTemplate("Invoice", "Thank you for your purchase");
        final var copy = template.clone();
    }
}
```

**16. Singleton**

Ensure a class only has one instance, and provide a global point of access to it.

```java
final class Singleton {
    private static final Singleton INSTANCE = new Singleton();

    private Singleton() { }

    static Singleton getInstance() {
        return INSTANCE;
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var instance = Singleton.getInstance();
    }
}
```

```java
final class AppConfig {
    private static final AppConfig INSTANCE = new AppConfig();

    private AppConfig() { }

    static AppConfig getInstance() {
        return INSTANCE;
    }

    String getEnvironment() {
        return "production";
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var config = AppConfig.getInstance();
        config.getEnvironment();
    }
}
```

## Structural

**17. Adapter**

Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces.

```java
interface Target {
    void request();
}

final class Adaptee {
    void specificRequest() { }
}

record Adapter(Adaptee adaptee) implements Target {
    public void request() {
        adaptee.specificRequest();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var adaptee = new Adaptee();
        final var target = new Adapter(adaptee);
        target.request();
    }
}
```

```java
interface PaymentProcessor {
    void process(final int amount);
}

final class LegacyGateway {
    void makePayment(final int cents) { }
}

record GatewayAdapter(LegacyGateway gateway) implements PaymentProcessor {
    public void process(final int amount) {
        gateway.makePayment(amount * 100);
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var processor = new GatewayAdapter(new LegacyGateway());
        processor.process(25);
    }
}
```

**18. Bridge**

Decouple an abstraction from its implementation so that the two can vary independently.

```java
interface Implementor {
    void operationImpl();
}

abstract class Abstraction {
    private final Implementor implementor;

    Abstraction(final Implementor implementor) {
        this.implementor = implementor;
    }

    abstract void operation();
}

final class RefinedAbstraction extends Abstraction {
    RefinedAbstraction(final Implementor implementor) {
        super(implementor);
    }

    void operation() { }
}

final class ConcreteImplementor implements Implementor {
    public void operationImpl() { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var implementor = new ConcreteImplementor();
        final var abstraction = new RefinedAbstraction(implementor);
        abstraction.operation();
    }
}
```

```java
interface MessageSender {
    void send(final String text);
}

final class EmailSender implements MessageSender {
    public void send(final String text) { }
}

abstract class Message {
    private final MessageSender sender;

    Message(final MessageSender sender) {
        this.sender = sender;
    }

    abstract void deliver(final String text);
}

final class AlertMessage extends Message {
    AlertMessage(final MessageSender sender) {
        super(sender);
    }

    void deliver(final String text) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var message = new AlertMessage(new EmailSender());
        message.deliver("Server is down");
    }
}
```

**19. Composite**

Compose objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uniformly.

```java
interface Component {
    void operation();
}

final class Leaf implements Component {
    public void operation() { }
}

final class Composite implements Component {
    public void operation() { }

    void add(final Component component) { }

    void remove(final Component component) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var composite = new Composite();
        final var leaf = new Leaf();
        composite.add(leaf);
        composite.operation();
    }
}
```

```java
interface MenuComponent {
    void render();
}

final class MenuItem implements MenuComponent {
    public void render() { }
}

final class Menu implements MenuComponent {
    public void render() { }

    void add(final MenuComponent component) { }

    void remove(final MenuComponent component) { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var menu = new Menu();
        menu.add(new MenuItem());
        menu.render();
    }
}
```

**20. Decorator**

Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

```java
interface Component {
    void operation();
}

final class ConcreteComponent implements Component {
    public void operation() { }
}

abstract class Decorator implements Component {
    private final Component component;

    Decorator(final Component component) {
        this.component = component;
    }

    public void operation() {
        component.operation();
    }
}

final class ConcreteDecorator extends Decorator {
    ConcreteDecorator(final Component component) {
        super(component);
    }

    public void operation() {
        super.operation();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var component = new ConcreteComponent();
        final var decorator = new ConcreteDecorator(component);
        decorator.operation();
    }
}
```

```java
interface Beverage {
    int cost();
}

final class Espresso implements Beverage {
    public int cost() {
        return 10;
    }
}

abstract class BeverageDecorator implements Beverage {
    private final Beverage beverage;

    BeverageDecorator(final Beverage beverage) {
        this.beverage = beverage;
    }

    public int cost() {
        return beverage.cost();
    }
}

final class MilkDecorator extends BeverageDecorator {
    MilkDecorator(final Beverage beverage) {
        super(beverage);
    }

    public int cost() {
        return super.cost() + 2;
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var coffee = new MilkDecorator(new Espresso());
        coffee.cost();
    }
}
```

**21. Facade**

Provide a unified interface to a set of interfaces in a system. Facade defines a higher-level interface that makes the subsystem easier to use.

```java
final class SubsystemA {
    void operationA() { }
}

final class SubsystemB {
    void operationB() { }
}

record Facade(SubsystemA subsystemA, SubsystemB subsystemB) {
    void operation() { }
}

public final class Demo {
    public static void main(final String[] args) {
        final var facade = new Facade(new SubsystemA(), new SubsystemB());
        facade.operation();
    }
}
```

```java
final class FlightService {
    void bookFlight() { }
}

final class HotelService {
    void bookHotel() { }
}

record TravelFacade(FlightService flightService, HotelService hotelService) {
    void bookTrip() {
        flightService.bookFlight();
        hotelService.bookHotel();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var facade = new TravelFacade(new FlightService(), new HotelService());
        facade.bookTrip();
    }
}
```

**22. Flyweight**

Use sharing to support large numbers of fine-grained objects efficiently. A flyweight is a shared object that can be used in multiple contexts simultaneously. The flyweight acts as an independent object in each context. It is indistinguishable from an instance of the object that is not shared.

```java
interface Flyweight {
    void operation(final Object extrinsicState);
}

record ConcreteFlyweight(Object intrinsicState) implements Flyweight {
    public void operation(final Object extrinsicState) { }
}

final class FlyweightFactory {
    Flyweight getFlyweight(final Object key) {
        throw new UnsupportedOperationException();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var factory = new FlyweightFactory();
        final var flyweight = factory.getFlyweight(new Object());
        flyweight.operation(new Object());
    }
}
```

```java
record CharacterStyle(String font, int size) { }

final class CharacterStyleFactory {
    CharacterStyle getStyle(final String font, final int size) {
        return new CharacterStyle(font, size);
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var factory = new CharacterStyleFactory();
        final var heading = factory.getStyle("Arial", 18);
        final var body = factory.getStyle("Arial", 12);
    }
}
```

**23. Proxy**

Provide a surrogate or placeholder for another object to control access to it.

```java
interface Subject {
    void request();
}

record RealSubject() implements Subject {
    public void request() { }
}

record Proxy(Subject subject) implements Subject {
    public void request() {
        subject.request();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final var proxy = new Proxy(new RealSubject());
        proxy.request();
    }
}
```

```java
interface Image {
    void display();
}

record HighResolutionImage(String path) implements Image {
    public void display() { }
}

final class ImageProxy implements Image {
    private final String path;
    private HighResolutionImage image;

    ImageProxy(final String path) {
        this.path = path;
    }

    public void display() {
        if (image == null) {
            image = new HighResolutionImage(path);
        }

        image.display();
    }
}

public final class Demo {
    public static void main(final String[] args) {
        final Image image = new ImageProxy("invoice.png");
        image.display();
    }
}
```
