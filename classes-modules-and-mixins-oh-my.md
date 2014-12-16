# Instances, Classes, and Modules, Oh My!

What kind of method should it be?
Exercism example

## Instances

### What Is An Instance?

A factory mold was filled and out popped an instance.

### Visibility: Public, Private, Protected



## Classes

### As A Factory

Made to create objects
Inheritance
So you should probably be creating objects with it!

### The Singleton Object

`Object#singleton_class` (works on an instance or an object)
Doesn't need to keep track of state
One-off features
Singleton module
Class inherits from Module

### class << self

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
