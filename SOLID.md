# SOLID

SOLID is an acronym introduced by Michael Feathers to represent five object-oriented design principles promoted by Robert C. Martin (Uncle Bob).

These principles aim to make code more readable, maintainable, extensible, and reusable while reducing code duplication.

## Single Responsibility Principle

A class should have only one reason to change.

### Bad

```java
public final class UserService {
    public void activate() { /* Domain */ }
    
    public void insertDatabase() { /* Database */ }
    
    public void sendEmail() { /* Email */ }
}
```

**The code is incorrect because the class has three reasons to change.**

### Good

```java
public final class UserService {
    public void activate() { }
}

public final class UserRepository {
    public void insert() { }
}

public final class EmailService {
    public void send() { }
}
```

**The code is correct because each class has a single responsibility and only one reason to change.**

## Open/Closed Principle

Code should be open for extension but closed for modification.

### Bad

```java
public enum PaymentMethod {
    CASH, CREDIT_CARD, DEBIT_CARD
}

public record Payment(PaymentMethod paymentMethod) { }

public final class PaymentService {
    public void pay(final Payment payment) {
        switch (payment.paymentMethod()) {
            case CASH -> { }
            case CREDIT_CARD -> { }
            case DEBIT_CARD -> { }
        }
    }
}
```

**The code is incorrect because PaymentService must be modified whenever a new payment method is added.**

### Good

```java
public interface PaymentMethod {
    void pay();
}

public record Cash() implements PaymentMethod {
    public void pay() { }
}

public record CreditCard() implements PaymentMethod {
    public void pay() { }
}

public record DebitCard() implements PaymentMethod {
    public void pay() { }
}

public record Payment(PaymentMethod paymentMethod) { }

public final class PaymentService {
    public void pay(final Payment payment) {
        payment.paymentMethod().pay();
    }
}
```

**The code is correct because new payment methods can be added without modifying PaymentService.**

## Liskov Substitution Principle

Subtypes must be substitutable for their base types without altering the correctness of the program.

### Bad

```java
public abstract class Bird {
    public void fly() {
        System.out.println("Flying");
    }
}

public final class Penguin extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Penguins can't fly");
    }
}
```

**The code is incorrect because Penguin does not fulfill the contract established by Bird. Replacing a Bird with a Penguin changes the expected behavior.**

### Good

```java
public abstract class Bird { }

public abstract class FlyingBird extends Bird {
    public void fly() {
        System.out.println("Flying");
    }
}

public final class Penguin extends Bird { }

public final class Eagle extends FlyingBird { }
```

**The code is correct because only compatible types inherit the flying behavior, preserving the expected behavior when substituting subtypes.**

## Interface Segregation Principle

Many specific interfaces are better than a single interface.

### Bad

```java
public interface Editable {
    void changeId(final int id);

    void changeAddress(final String address);

    void changePrice(final double price);
}

public final class Customer implements Editable {
    @Override
    public void changeId(final int id) { }

    @Override
    public void changeAddress(final String address) { }

    @Override
    public void changePrice(final double price) {
        throw new UnsupportedOperationException();
    }
}

public final class Product implements Editable {
    @Override
    public void changeId(final int id) { }

    @Override
    public void changeAddress(final String address) {
        throw new UnsupportedOperationException();
    }

    @Override
    public void changePrice(final double price) { }
}
```

**The code is incorrect because the interface forces classes to implement methods they do not need.**

### Good

```java
public interface Identifiable {
    void changeId(final int id);
}

public interface Addressable {
    void changeAddress(final String address);
}

public interface Priceable {
    void changePrice(final double price);
}

public final class Customer implements Identifiable, Addressable {
    @Override
    public void changeId(final int id) { }

    @Override
    public void changeAddress(final String address) { }
}

public final class Product implements Identifiable, Priceable {
    @Override
    public void changeId(final int id) { }

    @Override
    public void changePrice(final double price) { }
}
```

**The code is correct because the interface has been split into smaller interfaces, and classes implement only the methods they need.**

## Dependency Inversion Principle

Depend on abstractions rather than concrete implementations.

### Bad

```java
public record User() { }

public final class SqlUserRepository {
    public SqlUserRepository(final String connectionString) { }

    public void insert(final User user) { }
}

public final class UserService {
    private final SqlUserRepository sqlUserRepository = new SqlUserRepository("ConnectionString");

    public void insert(final User user) {
        sqlUserRepository.insert(user);
    }
}
```

**The code is incorrect because UserService depends on a concrete implementation and is responsible for creating its dependency.**

### Good

```java
public record User() { }

public interface UserRepository {
    void insert(final User user);
}

public final class SqlUserRepository implements UserRepository {
    public SqlUserRepository(final String connectionString) { }

    @Override
    public void insert(final User user) { }
}

public final class UserService {
    private final UserRepository userRepository;

    public UserService(final UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public void insert(final User user) {
        userRepository.insert(user);
    }
}
```

**The code is correct because UserService depends on an abstraction instead of a concrete implementation.**
