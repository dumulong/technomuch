---
title: Design Pattern
tags:
  - Concept
categories:
  - General Coding
date: 2020-03-30 14:08:13
---


## Behavioral Patterns

Most of these design patterns are specifically concerned with communication between objects.

* __Chain of responsibility__ : Chain of responsibility delegates commands to a chain of processing objects.

* __Command__ : Command creates objects which encapsulate actions and parameters.

* __Interpreter__ : Interpreter implements a specialized language.

* __Iterator__ : Iterator accesses the elements of an object sequentially without exposing its underlying representation.

* __Mediator__ : Mediator allows loose coupling between classes by being the only class that has detailed knowledge of their methods.

* __Memento__ : Memento provides the ability to restore an object to its previous state (undo).

* __Observer__ : Observer is a publish/subscribe pattern which allows a number of observer objects to see an event.

* __State__ : State allows an object to alter its behavior when its internal state changes.

* __Strategy__ : Strategy allows one of a family of algorithms to be selected on-the-fly at runtime.

* __Template method__ : Template method defines the skeleton of an algorithm as an abstract class, allowing its subclasses to provide concrete behavior.

* __Visitor__ : Visitor separates an algorithm from an object structure by moving the hierarchy of methods into one object.

## Creational

Creational patterns are ones that create objects, rather than having to instantiate objects directly. This gives the program more flexibility in deciding which objects need to be created for a given case.

* __Abstract factory__ : Abstract factory groups object factories that have a common theme.

* __Builder__ : Builder constructs complex objects by separating construction and representation.

* __Factory method__ : Factory method creates objects without specifying the exact class to create.

* __Prototype__ : Prototype creates objects by cloning an existing object.

* __Singleton__ : Singleton restricts object creation for a class to only one instance.

## Structural

These concern class and object composition. They use inheritance to compose interfaces and define ways to compose objects to obtain new functionality.


* __Adapter__ : Adapter allows classes with incompatible interfaces to work together by wrapping its own interface around that of an already existing class.

* __Bridge__ : Bridge decouples an abstraction from its implementation so that the two can vary independently.

* __Composite__ : Composite composes zero-or-more similar objects so that they can be manipulated as one object.

* __Decorator__ : Decorator dynamically adds/overrides behaviour in an existing method of an object.

* __Facade__ : Facade provides a simplified interface to a large body of code.

* __Flyweight__ : Flyweight reduces the cost of creating and manipulating a large number of similar objects.

* __Proxy__ : Proxy provides a placeholder for another object to control access, reduce cost, and reduce complexity.


## Learn more

WIKI : [https://en.wikipedia.org/wiki/Software_design_pattern](https://en.wikipedia.org/wiki/Software_design_pattern)

Book: [Design Patterns: Elements of Reusable Object-Oriented Software](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8/ref=sr_1_2?keywords=Design+Patterns%3A+Elements+of+Reusable+Object-Oriented+Software&qid=1585596430&sr=8-2)

Book: [Head First Design Patterns: A Brain-Friendly Guide](https://www.amazon.com/Head-First-Design-Patterns-Brain-Friendly-ebook/dp/B00AA36RZY/ref=sr_1_2?keywords=headfirst+design+pattern&qid=1585596487&sr=8-2)

YouTube: [Christopher Okhravi](https://www.youtube.com/playlist?list=PLrhzvIcii6GNjpARdnO4ueTUAVR9eMBpc)