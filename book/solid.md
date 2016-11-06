SOLID
=====

SOLID is a set of principles that you should follow if you want to get pure object oriented code which is easy to test and extend.

These stand for:

- [Single responsibility](#single-responsibility)
- [Open-closed](#open-closed)
- [Liskov substitution](#liskov-substitution)
- [Interface segregation](#interface-segregation)
- [Dependency inversion](#dependency-inversion)

Let's check what these mean.

## Single responsibility

A class should be responsible for a single task.

## Open-closed

A class or module (a set of related classes) should hide its implementation details (i.e. how exactly things are done)
but have a well defined interface that both allows its usage by other classes (public methods) and extension via inheritance (
protected and public methods).

## Liskov substitution

LSP, when defined classically, is the most complicated principle in SOLID. In fact, it's not that complicated.

It is about class hierarchies and inheritance. When you implement a new class extending from base one
it should have the same interface and behave exactly the same in same situations so it's possible to
interchange these classes.

For more see [a good set of answers at StackOverflow](http://stackoverflow.com/questions/56860/what-is-the-liskov-substitution-principle).

## Interface segregation

The principle points that an interface should not define more functionality that is actually used at the same time. It is like
single responsibiltiy but for interfaces. In other words: if an inteface does too much, break it into multiple more focused interfaces.

## Dependency inversion

Dependency inversion basically states that a class should tell what it needs via interfaces but never get what it needs itself.

See [dependencies.md]() for more information.
