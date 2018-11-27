SOLID
=====

SOLID is a set of principles that you should follow if you want to get code which is not fragile e.g. easy to change.

These stand for:

- [Single responsibility](#single-responsibility)
- [Open-closed](#open-closed)
- [Liskov substitution](#liskov-substitution)
- [Interface segregation](#interface-segregation)
- [Dependency inversion](#dependency-inversion)

Let's check what these mean.

## Single responsibility

A class should have one, and only one, reason to change.

Gather together the things that change for the same reasons. Separate those things that change for different reasons.

## Open-closed

You should be able to extend a classes behavior, without modifying it.

A class or module (a set of related classes) should hide its implementation details (i.e. how exactly things are done) but have a well defined interface that both allows its usage by other classes (public methods) and extension via inheritance (
protected and public methods).

## Liskov substitution

Derived classes must be substitutable for their base classes.

LSP, when defined classically, is the most complicated principle in SOLID. In fact, it's not that complicated.

It is about class hierarchies and inheritance. When you implement a new class extending from base one
it should have the same interface and behave exactly the same in same situations so it's possible to
interchange these classes.

For more see [a good set of answers at StackOverflow](http://stackoverflow.com/questions/56860/what-is-the-liskov-substitution-principle).

## Interface segregation

Make fine grained interfaces that are client specific.

The principle points that an interface should not define more functionality that is actually used at the same time. It is like
single responsibiltiy but for interfaces. In other words: if an interface does differnt things, break it into multiple more focused interfaces.

## Dependency inversion

Depend on abstractions, not on concretions.

Dependency inversion basically states that a class should tell what it needs via interfaces but never get what it needs itself.

See [dependencies.md](https://github.com/samdark/yii2-cookbook/blob/master/book/dependencies.md) for more information.
