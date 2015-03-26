# Exotic Ruby Datastores

## The Ones We Already Know

String, Array, Hash, CSV, JSON, File
All kinds of numbers



## YAML

### What is it

YAML Ain't Markup Language

Cross-language data serialization in a human-readable format.
Libyaml is a C yaml parser, psych is a Ruby wrapper for it maintained by Aaron Patterson.

In the stdlib

### When would you use it

Store Ruby objects on disk
Rails i18n

### How it works

Indentation
Access like a hash

### Example

IdeaBox



## Marshal

### What is it

A way to serialize Ruby objects into a binary format.
http://www.skorks.com/2010/04/serializing-and-deserializing-objects-with-ruby/

### When would you use it

To save an object to a file

### Example



## Struct

### What is it

Bundled attributes with accessor methods, plus the ability to create computed methods. `new` creates a class that inherits from Struct.

### When would you use it

http://blog.rubybestpractices.com/posts/rklemme/017-Struct.html
http://stephaniehoh.github.io/blog/2013/12/28/the-ruby-struct-class/

### Example



## OpenStruct

In stdlib

### What is it

Similar to a hash. Allows definition of arbitrary attributes with
associated values. A class that gets metaprogrammed to have accessors
for the attributes

### When would you use it

Not as good performance as a hash due to using `method_missing` and
`define_method`.

### Example

As a test stub.



## Set

In stdlib

### What is it

Like an unordered array that can only contain unique members.
Use array methods but with faster lookup (like hash lookup). Uses hash
under the hood

### When would you use it

Compare equality without considering order
Receiving events that should only be recorded once per identifier (e.g.
whether a user has "checked in" to an event)

### Example




## Matrix

### What is it

### When would you use it

Encryption, graphing, statistics, electronic circuits and more

### Example



## Queue

### What is it

A place to hold values to be shared between threads.

SizedQueue - queue with a max capacity

### When would you use it

### Example



## IO + The Filesystem

### What is it

A read/write stream. Can be duplex / bidirectional. Plays back like
a tape. File is its only standard subclass of IO.

Tempfile < File < IO

### When would you use it

To save data to / read data from the filesystem

### Example




## ObjectSpace

### What is it

A way to interact with all of the living objects in the current Ruby
environment, and the garbage collector.

### When would you use it

Get a reference to an object from its id.
Count objects for performance reasons
Iterate through all the objects of a certain type with `each_object`
Manually start garbage collection

### Example

finalizers


## Conclusion
