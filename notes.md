# Class, Module, Instance


## Instances

### What Is An Instance?

A factory mold was filled and out popped an instance.

### Visibility: Public, Protected, Private

From ruby-docs.org:
Protected visibility is most frequently used to define == and other comparison methods where the author does not wish to expose an object’s state to any caller and would like to restrict it only to inherited classes.

A private method may not be called with a receiver, not even self.

## Classes

Every class is a module, but can't be mixed in. However they can
inherit.

### As A Factory

Made to create objects
Inheritance
So you should probably be creating objects with it!
Cannot be mixed in

### The Singleton Object

AKA eigenclass or metaclass

`Object#singleton_class` (works on an instance or an object)
Doesn't need to keep track of state
One-off features
Singleton module
Class inherits from Module

### class << self or class << Foo

### Private Class Methods

Doesn't actually work by default if you use `private`.
Use `private_class_method` instead (actually comes from module)



## Modules

### What Is A Module?

A collection of methods and constants
Mix functionality into multiple classes
Container for methods shared across objects
Namespacing
One-off features
Inherits from Object

No inheritance

### Include Vs. Extend

Include mixes methods into instances of an object
Extend mixes them into the object itself (whether instance, singleton,
or module).

### extend self

Can use with private methods.

### Module Functions

Allows you to be selective with what can be called on the module itself,
while still defining methods that can be mixed in / extended to other objects.

Actually copies the method and attaches it to the module itself.

trinkets => module_eval, module_exec

### Privacy

`private_class_method`, or `private` section.

## Conclusion



=================================================

Notes

Procs
Lambdas (Kernel method) -- same as Proc but ensure arity
Structs -- are classes
OpenStructs -- basically a hash with method_missing to define setters
and getters

Class < Module < Object < Kernel < BasicObject

module_eval and instance_eval, module_exec and instance_exec
module family is in Module
instance family is in BasicObejct

define_singleton_method
define_method

owner

using method, methods, singleton_method, and singleton_methods

Most of the methods in a new class or module come from Object

Module can call 'new', but not a module instance

Inheritance:
                         +---------+             +-...
                         |         |             |
         BasicObject-----|-->(BasicObject)-------|-...
             ^           |         ^             |
             |           |         |             |
          Object---------|----->(Object)---------|-...
             ^           |         ^             |
             |           |         |             |
             +-------+   |         +--------+    |
             |       |   |         |        |    |
             |    Module-|---------|--->(Module)-|-...
             |       ^   |         |        ^    |
             |       |   |         |        |    |
             |     Class-|---------|---->(Class)-|-...
             |       ^   |         |        ^    |
             |       +---+         |        +----+
             |                     |
obj--->OtherClass---------->(OtherClass)-----------...

Vertical arrows = inheritance
Paren = metaclass

