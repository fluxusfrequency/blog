# Building A Ruby List Comprehension

If you don't already know me, I came from a bootcamp. gSchool to be exact. I learned
Ruby there. I also spent a lot of time on the side teaching myself
JavaScript. I fledged, left the nest, and flew out into the big bad
world. Now I'm a developer. I try to be a good one. To me, that means
always learning and expanding.

## Snake Handling

<img src=http://cdn.ilovefreesoftware.com/wp-content/uploads/2014/06/Snake-Game.gif />

My latest escapade has a been an attempt to learn Python. I enjoy the
natural way that Ruby flows off the fingers, and had heard that Python
is much the same way. It sounded easy enough, especially since this
article promised I could [Learn Python in Ten Minutes](http://www.stavros.io/tutorials/python/).

It took a little while to get used to some of the differences in Python:
- significant whitespace & leaving off the `end` keyword
- capitalized booleans
- required return statements
- having to pass self into my method definitions
- completing method definitions and control flow statements with colons
- arrays and hashes are called `list`s and `dict`s

But these are all superficial differences. As I solved more and more problems on
[exercism.io](http://exercism.io/), Python syntax began to feel natural.  I felt
like everything I wrote had an analog in Ruby. Except list comprehensions.

## You Cannot Grasp The True Form Of Lists

In Python, Coffeescript, and many functional languages, there is a really cool
thing you can do called a "list comprehension". It's a way of doing
something like a `map`, but it's written in mathematical
[set builder notation](http://en.wikipedia.org/wiki/Set-builder_notation). Here's
what it looks like:

Instead of doing this:

```
(1..100).map { |i| 2 * i if i.even? }
```

You do this:

```
[2 * x for x in range(1, 100)]
```

At first I just thought, "hm, that's an interesting way to do it," but
before too long I was wishing I could write my Ruby that way. In part,
it's because you can nest comprehensions in Python:

```
return [[self.PLANTS[plant] for plant in row] for row in self.rows]
```

I was yearning to be able to write comprehensions in Ruby, so I set out
to implement a comprehension of my own.

## Make It So

My first thought in writing a list comprehension in Ruby was to make it
look like Python. I wanted to be able to write square brackets with an
expression inside of them. To make that happen, I would need to override
the `main` `[]()` method. It turns out that that method is an alias for
the `Array#[]` singleton [method](http://www.ruby-doc.org/core-2.1.3/Array.html#method-c-5B-5D).
It's not so easy to monkey patch, because you can't really call `super`
on it.

I decided to abandon this approach and create a new method called
`c()`, that I would put in a module and include in files where I wanted
to use it. Ideally, this method would take a single argument that would
be something like: `x for x in my_array if x.odd?`. Since you can't
really pass an expression like this into a method and expect Ruby to
parse it properly, I opted to pass it in as a string and parse it. Not
ideal, but it could work fairly easily.

## Caught In A Loop

<img src="http://rs1img.memecdn.com/perfectly-looped-gifs---hatter-picard_webm_3954069.webm">

My first goal was to get the basic `x for x in my_array` part working.
I wrote a test:

```
class ComprehensionTest < Minitest::Test
  def setup
    extend ListComprehension
  end

  def test_simple_array
    result = c('n for n in [1, 2, 3, 4, 5]')
    assert_equal [1, 2, 3, 4, 5], result
  end
end

```

This was fairly straightforward to get passing.


```
module ListComprehension
  def c(comprehension)
  end
end
```















