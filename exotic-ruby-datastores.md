# Nine Exotic Ruby Datastores

Admit it: you like to get fancy. We all do. People love the exotic. As programmers, we often love the arcane.

Despite constant warnings against premature optimization, an emphasis on "readable code", and the old aphorism, "keep it simple, stupid", we just can't help ourselves. We love the obscure.

In that spirit, let's go on an adventure. In this post, we'll take a look at nine weird ways to store data in the Ruby language.

## The Ones We Already Know

Before we get started, we'll set a baseline. What are the ways to store data in Ruby that we use every day? Well, these are the ones that come to mind for me:

- String
- Array
- Hash
- CSV
- JSON

And all kinds of numbers.

These are the ones we'll skip. So what are some of the other ways to store data in Ruby? Let's find out.



## Struct

### What It Is

A struct is a way of bundling together a group of variables under a single name. If you've done any [C programming](http://en.wikipedia.org/wiki/Struct_%28C_programming_language%29), you've probably come across structs before.

A struct is similar to a class. At its most basic, it's a group of bundled attributes with accessor methods. You can also define methods that instances of the stuct will respond to.

In Ruby, structs inherit from Enumerable, so they come with all kinds of great behavior, like `to_a`, `each`, `map`, and member access with `[]`.

### Example

You can define a struct object by setting a constant variable equal to `Struct.new` and passing in some default attribute names. From there, you can create any number of instances of the struct, passing in the desired attributes.

```ruby
Cat = Struct.new(:name, :breed, :hair_length) do
  def meow
    "m-e-o-w-w"
  end
end

tabby = Cat.new("Tabitha", "Russian Blue", "short")

tabby.name
=> "Tabitha"
tabby.meow
=> "m-e-o-w-w"
tabby[0]
=> "Tabitha"
tabby.each do |attribute|
  puts attribute
end
"Tabitha"
"Russian Blue"
"short"
=> #<struct Cat name="Tabitha", breed="Russian Blue", hair_length="short">
```

### When You Would Use It

If you want to quickly define a class that has easily accessible attributes and little other behavior, structs are a great choice. Since they also respond to enumerable methods, they are great for use as stubs in tests.

If you want to stub a class and send it a message in a test, but you don't want to use [a double](https://relishapp.com/rspec/rspec-mocks/v/3-2/docs/verifying-doubles/using-an-instance-double), you can fake it with a struct in a single line of code.

```ruby
fake_stripe_charge = Struct.new(:create)
```

Next we'll take a look at `Struct`'s close cousin, `OpenStruct`.



## OpenStruct

### What It Is

OpenStruct lives in the Ruby Standard Library, so to use it in your code, you'll have to `require 'ostruct'`.

Similar to a hash. Allows definition of arbitrary attributes with associated values. A class that gets metaprogrammed to have accessors for the attributes

### Example

As a test stub.

### When You Would Use It

Not as good performance as a hash due to using `method_missing` and
`define_method`.



## Marshalling

### What It Is

A way to serialize Ruby objects into a binary format.
http://www.skorks.com/2010/04/serializing-and-deserializing-objects-with-ruby/

### Example

### When You Would Use It

To save an object to a file or database



## YAML

If there's one


### What It Is

YAML Ain't Markup Language

Cross-language data serialization in a human-readable format.
Libyaml is a C yaml parser, psych is a Ruby wrapper for it maintained by Aaron Patterson.

In the stdlib

### Example

Indentation
Access like a hash

IdeaBox

### When You Would Use It

Store Ruby objects on disk
Rails i18n



## Set

In stdlib

### What It Is

Like an unordered array that can only contain unique members.
Use array methods but with faster lookup (like hash lookup). Uses hash
under the hood

### Example

### When You Would Use It

Compare equality without considering order
Receiving events that should only be recorded once per identifier (e.g.
whether a user has "checked in" to an event)





## Matrix

### What It Is

### Example

### When You Would Use It

Encryption, graphing, statistics, electronic circuits and more




## Queue

### What It Is

A place to hold values to be shared between threads.

SizedQueue - queue with a max capacity

### Example

### When You Would Use It




## IO + The Filesystem

### What It Is

A read/write stream. Can be duplex / bidirectional. Plays back like
a tape. File is its only standard subclass of IO.

Tempfile < File < IO

### Example

### When You Would Use It

To save data to / read data from the filesystem





## ObjectSpace

### What It Is

A way to interact with all of the living objects in the current Ruby
environment, and the garbage collector.

### Example

finalizers

### When You Would Use It

Get a reference to an object from its id.
Count objects for performance reasons
Iterate through all the objects of a certain type with `each_object`
Manually start garbage collection



## Conclusion


