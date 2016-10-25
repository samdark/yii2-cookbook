Dependencies
============

Class A depends on class B when B is used within A i.e. when B is required for A to function.

## Bad and good dependencies

There are two metrics of dependencies:

- Cohesion.
- Coupling.

Cohesion means dependency on a class with related functionality.

Coupling means dependency on a class with not really related functionality.

It's preferrable to have high cohesion and low coupling. That means you should get related functionality together into
a group of classes usually called a module (which is *not* Yii module and not an actual class, just a logical boundary).
Within that module it's a good idea not to over-abstract things and use interconnected classes directly. As for classes
which aren't part of the module's purpose but are used by the module, such as general purpose utilities, these should not
be used directly but through interface. Via this interface module states what is needed for it to function and from this point
it doesn't care how these dependencies are satisfied.

## Achieving low coupling

You can't eliminate dependencies altogether but you can make them more flexible.

## Inversion of control


## Dependency injection

## Dependency container

