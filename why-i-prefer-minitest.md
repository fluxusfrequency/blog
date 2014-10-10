- Using it with Jeff Casimir and Katrina Owen in gSchool[1]
- Rspec became standard in what Klabnik calls the "prime stack"
- Story of Mike saying "people still use minitest?"
- But have you actually tried it? Really?

People ask, why should I switch to minitest?
Better question:
"What is in RSpec that you need that MiniTest::Spec does not offer?"
(https://github.com/metaskills/holy_grail_harness/issues/3)

DHH's gist
https://gist.github.com/dhh/893027

Bow Before Minitest (Adam Hawkins)
https://speakerdeck.com/ahawkins/bow-before-minitest

- Examples aren't nested in describe block, making it easier to skip them all and go into TDD mode.
- It's flat, so its 1) easier to read 2) easier to remember which scope
  you're working in
- Rspec doesn't _really_ read like english. look at all those [] () and
  {}. WTF is 'let(:post) do FactoryGirl.create(:post) end' ?
- declaring variables is more intuitive than using "let" blocks in rspec
- don't have to remember or look up matchers, just write rubby - less mental
  overhead, less magic
- It's easy to include modules for shared examples
- The DSL is small and easy to remember
- It just uses plain Ruby, no example objects and matchers that have to get garbage
  collected
- Concurrency (http://chriskottom.com/blog/2014/10/exploring-minitest-concurrency/) NOPE
- Tests are randomized by default
- Simplicity of assert and refute
- Ships with rubby
- Being explicit about what you're testing instead of describe
  "ClassName"
- Custom assertions? Just write a method
- It's a little faster but ey

- rspec syntax: nope


- consensus is: it's a matter of taste
```
minitest/benchmark is an awesome way to assert the performance of your
algorithms in a repeatable manner. Now you can assert that your newb
co-worker doesn't replace your linear algorithm with an exponential
one!
```

Used by Katrina Owen, DHH, Sandi Metz, Aaron Patterson (http://rubyrogues.com/001-rr-testing-practices-and-tools/)


