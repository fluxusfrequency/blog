# How To Write Code With Style

As a consultant, I spend a lot of time reading through other peoples' code. Every few months I find myself in a new context, and unless I building a greenfield project, I have to ramp up on a codebase with months to years of history.

It's always surprising to me the extent to which teams rely on undocumented knowledge. When I'm trying to get a project up and running, I always have to reach out to somebody else at the company to get past a certain snag in the set up process. The inevitable answer comes: "oh yeah, you just need to _foobar_ the _bazqux_, then it will work." While this process does get me where I need to go, a lot of time and context-switching could have been saved if the necessary hack had been included in the README or the code had been written in a less cryptic way. In that case, I probably could have figured it out on my own.

Code has to work
Code is communication

[The Poetics of Coding](http://www.smashingmagazine.com/2010/05/the-poetics-of-coding/)

[How To Write With Style](http://kmh-lanl.hansonhub.com/pc-24-66-vonnegut.pdf) by Kurt Vonnegut.

## 1. Business Value Comes First

There are many motivations that can drive a programmer when she sits down to write code. Maybe she wants to be a [Rock Star](https://news.ycombinator.com/item?id=1757059). She's going to show everyone how smart she is by using the most arcane APIs available in her language. Maybe she's interested in functional programming, and wants to see if she can find a legitimate use for [Proc#curry](http://ruby-doc.org/core-2.2.3/Proc.html#method-i-curry) in Ruby. Or maybe she's ready for the weekend, and all that's on her mind is "fuck it, ship it".

Whatever might be simmering below the surface, when we sit down to write code, we should take a step back and look at _why_ we're coding in the first place. Nine times out of ten, it's so that we can help make money for the band of misfits we run with. We should set aside our plans for world domination, and adopt a view that asks "how can I help us succeed as a business?" In most cases, this is why we're writing code. So we should strive to write the most solid instructions to the computer that we can.

## 2. Do What You Came To Do

If you've taken the time to open your editor and start writing code, there is a good reason that you're doing it. Whatever the specific problem you're trying to solve is, stay focused on it. If your codebase is like most of the ones I've seen, there's probably a lot of touching going on between modules, classes, services, etc.

Sometimes when you're writing a feature, you can't resist going down a rabbit hole. You might think: "if only we used Active Model Serializers, I would be able to change this API response with a single line of code. I'll just start putting it in." STOP. Maybe you're right. But do the thing you came to do. Make a note of your brilliant idea, and write a story for it when you're done with your feature so that you can give it your complete focus later.

Don't leave TODOs littered throughout the code with things you'd like to see done later (for example, `# Need to pull this call out into a background job`). Write stories instead.

The need to be present with what you're doing goes beyond getting sidetracked by your own code. Your attention can also be split by distractions like social media sites, reading the news, Slack or HipChat, and growl notifications. If you've never tried timeboxing (for example, with Pomodoros), I highly recommend it. Combine them with an app like Freedom to keep yourself from getting pulled away, and pretty soon you'll find yourself able to avoid context switching for good blocks of time. I like to do 25 minute blocks.

## 3. Keep It Simple, Stupid

You've heard the gospel before: premature optimization is evil. Don't write an abstract class and start subclassing it for one use case (or even two)! If I'm reading your code, I would much rather see magic strings and duplication between files than five levels of indirection between service objects or higher-order functions that will take me an hour to decipher.

When you're getting ready to introduce a new piece of functionality, try to solve it the easiest way you can think of. It's classic TDD: red, green, refactor. That means when you're starting to solve a problem and you think "this is a _horrible_ way to do it", stop yourself from trying to solve it the "right way" at first. Write it horribly! Then you'll be able to step back and see exactly _why_ it's horrible, and with the help of the tests you wrote along the way, you can rewrite it in a cleaner way.

You also don't always have to worry about performance right out of the gate. Solve the problem first. If performance is _truly_ a concern, run some benchmarks, choose a reasonable solution, and leave comments so that the next person will understand what you're doing.

## 4. Backspace Is Your Friend

The number one thing that clients pay me for is understanding their codebase. I spend countless hours trying to understand custom DSLs, indirection between service objects, and other complexities of code. The mental overhead is high for getting things done. To some degree, this is unavoidable. But there is an inexpensive way to make code easier to understand: delete unused code.

Don't be afraid to remove things that aren't getting used. If you're deprecating a feature, don't comment it out with a note like `TODO: put this back in after FooBar integration is complete`. If you're writing a bit of code that duplicates the functionality of something that's already there, pull out the old version. Just delete it. You can get it back, I promise. That's what Git is for.

Why should you delete it? When there's old, dusty code hanging around that never gets called, it scares developers. We don't know whether we are supposed to be using it, and we are afraid to delete it because we don't want to break anything. If you have a decent test suite, you should be confident about pulling things out as they become unnecessary. If not, take the time to write a few integration cases. Just cover the workflows that are vital to your business. Doing so will pay huge dividends in the future, because you'll be able to remove dead code without fear, and keeping the project slim will make development faster in the future.

## 5. Be Understandable

Consider the following code:

```
(function(w, d, s, l, i) {
   w[l] = w[l] || [];
   w[l].push({
     'service.start': new Date().getTime(),
     event: 'service'
   });
   var f = d.getElementsByTagName(s)[0],
   j = d.createElement(s),
   dl = l != 'dataMonitor' ? '&l=' + l : '';
   j.async = true;
   j.src = '//cdn.foobar.com/script.js?id=' + i + dl;
   f.parentNode.insertBefore(j, f);
 })(window, document, 'script', dataMonitor, id);
```

Wat?

Just because you can write things succinctly doesn't mean you should. Code golf is fun as an exercise, but it's not fun to try to understand your minimal code when I'm trying to build a business.

Writing code that's easy to understand is both courteous and economical. I can't count the number of hours I've spent trying to decipher terse and cryptic blocks of code. Adding those hours up across a team of devs, it's easy to see how clear naming and formatting has the potential to save a company enormous amounts of money.


## 6. Follow The Style Guide

Strunk and White
Ruby Style Guide
JS - AirBnB, NPM, Crockford
Python Pep8

## 7. Write With Compassion

It's hard to understand code. The developer that comes after you will have to dig through a complicated folder structure to find your files, and when they get there they'll have to decipher your variable names, dig through your dependencies to find magic values, _and_ try to grok how what you've written is a metaphor for a real business rule.

As writers of modern, flexible languages, the possible ways to solve a problem seem innumerable and glamorous. But we should realize that if we want to do our best to make our thoughts clear to whoever comes after us, our best bet is to write code as straightforwardly as we can.

We should strive to be humble. Leave behind the desire to write the most clever or efficient algorithm if it means sacrificing clarity. Blazing fast code has a time and place. But generally speaking, the money your company will save when the next programmer understands your code immediately will more than make up for the hundred milliseconds you would have saved by using bitmasking instead of a dictionary.

## Conclusion
