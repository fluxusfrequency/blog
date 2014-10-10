# You're Not Using Minitest Yet? 7 Reasons Minitest is the Future of Ruby Testing

The other day at our company standup, I mentioned that I was eager to
read an article on [Concurrency in Minitest](http://chriskottom.com/blog/2014/10/exploring-minitest-concurrency/)
that was featured in [Ruby Weekly](http://rubyweekly.com/issues/216). One of my
coworkers asked: "people still use Minitest?" My reply: "you mean you're not
using Minitest yet?"

I love Minitest. It's small, lightweight, and ships with Ruby.
It's used by heavyweights like [Aaron Patterson](http://rubyrogues.com/001-rr-testing-practices-and-tools/),
Sandi Metz, Katrina Owen and DHH.  Here's a look at why Minitest is gaining
popularity in the Ruby community.

## There and Back Again

I was a student in the second [gSchool](http://www.galvanize.it/school/) class. At
that time, it was being taught by [Jumpstart Lab](http://jumpstartlab.com/).
My instructors were [Jeff Casimir](https://twitter.com/j3), [Franklin Webber](https://twitter.com/franklinwebber), and [Katrina Owen](https://twitter.com/kytrinyx).

My cohort was brought up on a TDD ethic from day one. We practiced it in everything
we did. The tool we used was Minitest. When it was first introduced, I scoffed a little
because of the name.

"Why are we using a 'mini' testing framework?  I want to use what the pros use,"
I complained to Katrina.

"Despite the name, Minitest is a fully-featured testing framework, and is used
in plenty of production apps," was her reply.

I've been using it ever since. Although I've tried other testing
frameworks, and use them regularly for client work, I always prefer to
use Minitest if I can.

## Why Minitest Rocks

Here are seven reasons that Minitest rocks.

### 1. It's Plain Ruby

When you choose to use Minitest, you are choosing the use Ruby to write
your tests, rather than a DSL. Less magic comes with less mental
overhead. If you want a custom matcher, just write a method for it. Need
a [shared example](https://canaryup.com/blog/shared-examples-with-minitest)? Include a module.


### 2. It Makes Sense to Programmers

Occasionally, I hear the argument that using `expect` or `should` syntax
makes it easier for customers to understand what's being tested. When's
the last time that you sat down with your customers and wrote a bunch of
tests?

Tests are written to make design decisions and figure out why things broke. These are
two activities that are usually undertaken by developers. So why are we
writing BDD tests in psuedo-english for customers? We should be writing tests the
same way we write the rest of our applications: with code.

Declaring variables is more intuitive than using "let" blocks. Which
makes more sense if you think about it as code rather than English?

"Let post do post dot create end"
`let(:post) { Post.create }`

"post equals post dot create"
`post = Post.create`

The DSL for Minitest is small and easy to use. All you need to know is `assert` for booleans,
and `assert_equal` for everything else. If you want to negate it, use
`refute` or `refute_equal` instead. The rest is just Ruby.

### 3. It's Flat

With nested `context`, `describe`, and `it` blocks, it can be difficult
to remember what `it` refers to, or which `before` block is included in
your scope. I find myself scanning indentation in BDD tests to figure
out what scope I'm working in.

Minitest is flat. You start with a class, then you write a bunch of test
methods inside of it. It's clear that the only variables available are
those defined in the `setup` method or in the test itself.

You might object that `describe`, `expect`, and `should` are a great way
to document the desired behavior of your code. I would argue that you
can document it even more straightfowardly in Minitest, by naming the
method descriptively, and setting an output message if you're really
worried you'll forget what the test was for.

```
test 'it calculates the average speed of an unladen swallow' do
  swallow = Swallow.new(wing_span: 30, laden: false)
  expected = '11 MPS'
  actual = swallow.average_speed
  assert_equal expected, actual, 'The average speed of an unladen
    swallow was incorrect'
end
```

Minitest's flatness is also a benefit when it comes to practicing a good
Test-Driven workflow. You can `skip` all of the tests in a file easily,
without scanning through nested blocks. Then you can make them pass, one
at a time.

### 4. It Lends Itself to A Good Test-Driven Workflow

Minitest is awesome for getting into a red/green/refactor workflow.
Write a test, watch it fail, make it pass, refactor. Repeat. A Minitest
file is just a flat list of tests that are waiting for you to make them
pass.

Plus, since you're just writing Ruby, you can use a style like this to get the
next test set up with a minimum of effort:

```
test 'something' do
  expected = # some code
  actual = # some simple result
  assert_equal expected, actual
end
```

### 5. Minitest::Benchmark is Awesome

If you are dealing with large amounts of data, and performance is
a concern, [Minitest::Benchmark](https://github.com/seattlerb/minitest/blob/master/lib/minitest/benchmark.rb) is your best friend.

It lets you test your algorithms in a repeatable manner, to make sure
that their Big O classifications don't accidentally get changed. You can
collect benchmarks in 'tab-separated format, making it
easy to paste into a spreadsheet for graphing or further
analysis'.

Here are a few of the assertions in Minitest::Benchmark that might be of
interest:

- `assert_performance_constant`
- `assert_performance_exponential`
- `assert_performance_logarithmic`
- `assert_performance_linear`

### 6. It's Randomized By Default

Running the tests in a different order than they're written can catch bugs
caused by poorly written tests. Running them in a different order every
time can do the same.

### 7. It's Faster

Minitest does not create any matchers or example objects that have to be
garbage collected. Many people have run tests comparing Minitest to
other frameworks, and it usually comes out slightly ahead. Sometimes it
comes out [significantly ahead](http://blog.rawonrails.com/2012/01/very-cursory-test-of-rspec-28-speed.html).

Minitest also supports concurrent test runs. Although you would expect
this to lead to great speed gains, it does so [only in JRuby and Rubinius](http://chriskottom.com/blog/2014/10/exploring-minitest-concurrency/).
Still, it's nice to know that the option is there.

## Have You Tried It?

I've talked to a lot of people that are surprised when I say I prefer Minitest. They
often ask, "why should I switch to Minitest?" Perhaps a better question to ask is
this one, posed by [Ken Collins](https://github.com/metaskills/holy_grail_harness/issues/3):
What is in other testing frameworks that you need that Minitest does not offer?

In the end, I've found that asking questions isn't going to convince
anyone to change their toolset. Programmers are a stubborn and
opinionated breed. If you're curious about Minitest, but still feeling
pretty sure that you won't like it?

Try it. I'd suggest using [Exercism](http://exercism.io/getting-started). It's a small investment. You will learn it in a few minutes.
Pretty soon, I bet you'll be using it too.

## Other Resources

[Bow Before Minitest](https://speakerdeck.com/ahawkins/bow-before-minitest)
A slide deck examining the benefits of using Minitest.

[A Big Look At Minitest](http://www.slideshare.net/markykang/minitest-2013-0910)
Another slide deck that explores the ins and outs of Minitest.

[DHH's gist](https://gist.github.com/dhh/893027)
Pitting a Rails controller test written in RSpec against one in Minitest.

