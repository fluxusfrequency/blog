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

At first, there was some definite confusion, caused by significant whitespace,
capitalized booleans, remembering to use return statements, pass self into my method
definitions, complete them with colons, and leave off the `end` keyword:

```
def my_method(self, argument):
  # do things
  return True
```

But these are all superficial differences. As I solved more and more
Python problems on [exercism.io](http://exercism.io/), everything started to feel natural.
I felt like everything I wrote had an analog in Ruby. Except list comprehensions.

## You Cannot Grasp The True Form Of Arrays

In Python, arrays are called lists. There is a really cool thing you can
do with them called a "list comprehension".  They're also found in
Coffeescript. Basically, it's the Python way of doing a Ruby Enumerable
(like `each`). It turns out to be really expressive, fast, and powerful.

Instead of doing this:

```
[1, 2, 3, 4, 5, 6].each { |i| puts i }
```

You do this:

```
[print(x) for x in [1, 2, 3, 4, 5, 6]]
```
