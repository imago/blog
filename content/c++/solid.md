---
title: "SOLID Principle"
date: 2023-05-29T14:57:34+02:00
draft: false
---

# Preamble / Aim

- Reduce technical debt
  - The cost of prioritizing fast delivery over code quality for long periods of time.
  - applying: SOLID Principles, Design Patterns, TDD, Continuous Refactoring
  - Key: Keep it under control
  - Do not let it kill your project

- Code Fragility

"Fragility is the tendency of the sofware to break in many places every time it is changed." - Robert C. Martin

- Code Rigidity

"Rigidity is the tendency for software to be difficult to change, even in simple ways. Every change causes a cascade of subsequent changes in dependent modules." - Robert C. Martin

- Coupling

The level of inter-dependency between various software components.

- API

A contract/agreement between different software components on how they should work together.

# Principles

## 1. Single Responsibility Principle

- Every function, class or module should have one and only one reason to change.
- Responsibilities:
  - Business logic
  - User Interface
  - Persistence
  - Logging
  - Orchestration
  - Users

A class should do just one thing and not more than it is responsible for. It encapsulates its responsibility, make it invisible to other parts. If it needs to do more, separate functionality into more than one class and use these methods / classes from upper e.g. main / upper class.

Assume you have a User Class. The User Class should just hold user information. If you need to print user information for a Timesheet, create a new class Timesheet and print the information of the user using a method in Timesheet Class. Do not add the user print information method to User Class because it is not the responsibility of User Class to print Timesheet user information out.

Often this is hidden within if-clauses or switch-cases. Depending on a variable you are doing this or that which could be a complete different thing / responsibility.

## 2. Open, Closed Principle

- Classes, functions, and modules should be closed for modification, but open for extension.

If a class / method meets Single Responsibility Principle we should not change code anymore of this entity (except bugs). This method should/was unit tested and was implemented because of requirements. If we change it, because we need new functionality, it could break other part.

Therefore, implement new functionality creating e.g. an interface, abstraction and implement it using new appropriate classes: Create an abstract class, interface and create a new class which implements this abstract class, interface using already implemented methods and new methods which you need for your new functionality. This new interface is used in old class which you wanted to extend.

Old code will not be changed (but could be moved to new location)(closed) but functionality is extended (open). This also helps to fulfill the Single Responsibility Principle better.



## 3. Liskov Substitution Principle

- If S is a subtype of T, then objects of type T in a program may be replaces with objects of type S without modifying the functionality of the program.

Same as
- Any object of a type ust be **substitutable** by objects of a derived typed without altering the correctness of the program.

  Example: Is the class rectangle **fully substitutable** by the class square?

Square has the restriction that width and height is same. If square would be a rectangle and rectangle is able to set width and height, rectangle cannot restrict width to height length. This means S cannot be a subset of base type T although it looks like it could in real life.

If your class inherits from base class but cannot implement one of the base class methods, thand the base class is not fully substitutable by the derived class. You will experience this if you try to implement an empty method or throw an error that you did not implement a method from base (super) class.

  Example: Often, you can use the Strategy pattern.

Using an abstract class or interface, different classes inherit from it and implement its implementation.
In Strategy pattern you have some kind of main method which uses the abstract class / interface and decides which of the implementations to choose.

** Real life categories do not always map to OOP relationships.**

## 4. Interface Segregation Principle

Clients should not be forced to depend on methods that they do not use.

- If own interfaces: break them to smaller ones (focused)
- If external interfaces: Use pattern "Adapter"

"Fat interfaces lead to inadvertent couplings between clients that ought otherwise to be isolated." - Robert C. Martin

If you break your interfaces into smaller one, it looks like you did not really change anything. But in reality you decouple parts of your components and reduce technical debt by using the smaller interfaces with less dependencies and are able to compose being more flexible.

## 5. Dependency Inversion Principle

1. **High level modules** should not depend on **low level modules**; both should depend on abstractions.

Just create interfaces / abstract classes and implement them in your class. Split the interfaces into smaller pieces depending on responsibility / logic. Implemented classes are high (e.g. business logic) or low level (io, networking, serialization,etc) modules.

2. Abstractions should not depend on details. Details should depend upon abstraction. Abstraction is something, that is not concrete.


### Dependency Injection

- A technique that allows the creation of dependent objects outside of a class and provides those objects to a class.

Sometimes it is not enough to apply the Dependency inversion principle, because it can be that you are using the interfaces directly in your code with some initializer object list. The objects for initialization need to be created. Therefore you need to know much about all dependent objects and create them. With dependency injection you add all your dependent classes into your Constructor of your class. This makes it a bit easier on usage.

### Inversion of Control (IoC)

- Inversion of Control is a design principle in which the control of object creation, configuration, and lifecycle is passed to a container or framework.

The ultimate way is to use an IoC container which knows which objects to create and initialize knowing all their dependencies. It just creates them and you are using the objects in your implementation. You do not need to know about transversal dependencies of objects which can be huge in larger projects.

Now it is easier to test your implementation on unit test level using e.g. mocks depending on if you are just using dependency injection and mocks or all IoC container implementation.
