---
layout: post
title:      "Testing your code"
date:       2021-05-27 15:57:05 +0000
permalink:  testing_your_code
---


Most of programming is about getting your code to work properly. New programmers often just write a bunch of code, play with while running the code, and then try to find the bugs (perhaps using some debugging tools in the process). But this is a haphazard approach, and it can miss some edge cases, or result in  complex bugs that is difficult to trace to a specific piece of code. A better approach is to write unit tests using a testing suite, testing each function and method to make sure it works as expected. (Even better is *test driven development*: write the tests first, and then write code to make them work. This post, however, is focused on how to write tests, and I will save TDD for a future post.) Testing not only makes debugging simpler and more effective, it is also a skill highly sought after by employers in the tech field, something you often need to know to get a developer job.

**Testing with RSpec**

Each popular language has testing tools designed for it, and for Ruby the most popular testing suite is RSpec. In order to install it, navigate to your project directory in terminal and type `gem install rspec`. This will create three files:
```
	create  .rspec
	create  spec
	create  spec/spec_helper.rb
```
In the case of a Rails project, you would type `gem install rails` and it would also create `spec/rails_helper.rb`. You can then write tests and they go in the spec folder. To run the tests, simply type `rspec` (or if that gives an error, `bundle exec rspec`).

At the top of the spec file, you need to require respec and spec helper, in addition to the ruby scripts you are testing (I am using the example of my [Music Explorer CLI project](https://github.com/wkdewey/Music_Explorer), and "./lib/music_explorer" is the base file that defines the CLI module and requires all the necessary files).

```
require "rspec"
require "spec_helper"
require "./lib/music_explorer"
```
**RSpec syntax**

RSpec syntax is designed to be easily readable by developers. The basic syntax of RSpec starts with the *describe* method, which identifies the class or instance it is testing, like this:
```
describe User do

end
```
or this:
```
describe 'Some string' do

end
```
Or for a specific method, you preface the string with # for a class method and . for an instance method:
```
describe Agent, '#favorite_gadget' do
  ...
end
```
```
describe Agent, '#favorite_gadget' do
  ...
end
```

For an individual situation that you're testing, you use the *it* keyword followed by a descriptive string
```
RSpec.describe Artist do
    it "has a name" do
    end
end
```
To group your tests in a more organized way, you can specify a *context:*
```
RSpec.describe "MusicExplorer::Artist" do
  context "when initialized with artist data" do
    it "has a name" do
    end

    it "has genres" do
    end
	end
end

```

All of this is merely semantic, for easy to read test code and output (all the strings will be visible in the output).

The actual test code starts with the keyword *expect*:
```
it "has genres" do
      expect(artist.genres).not_to be nil
    end
```

**Four phases of testing**

The four phases of testing your code are setup, exercise, verification, and teardown.

*Setup* means preparing the data. In order to test your code, for example, you may need to make instances of the class. Here I am creating an artist instance to test my Artist class:
```
RSpec.describe "MusicExplorer::Artist" do
  artist_data = {name: "Beyonce", genres: [], top_tracks: [], albums: [], related_artists: []}
  artist = MusicExplorer::Artist.new(artist_data)
	...
end
```

*Exercise* means running the code you want to test:
```
context "when initialized with artist data" do
    it "has a name" do
      name = artist.name
    end
```
Specifically, I am testing the name attribute, with `name = artist.name` being the exercise code.

*Verification* is what tests the resulting code against the expectation, starting with the *expect* keyword. If it passes the test will be green when you run `rspec`, otherwise the test will be red and produce an error:
```
it "has a name" do
      name = artist.name
			expect(name).to eq "Beyonce"
    end
end
```
Often, you can combine exercise and verification into one line, for simple tests:
```
it "has a name" do
      expect(artist.name).to eq "Beyonce"
end
```

*Teardown* is handled by the testing framework itself, you do not usually need to write teardown code for basic tests. RSpec automatically resets state after a testing block ends, so that the code can be tested in a different way.

**Examples of expectations**

There are a number of expectations that can be tested with the expect method, as you can see above. Expect takes in a parameter, the value you are testing. The *.to* method determines what the expectation is, the condition you want to be true for the test to pass. If you want it to be false, use *.not_to* instead. The following is just a sample of possible expectations:

`.to eq` tests for equality with a particular value, you can see this in many of the tests above.

`.to be_truthy` tests for a truthy value, either the boolean value true or another value that is not nil (anything other than nil or false is truthy in Ruby). `.to be_true` tests for boolean true. The opposite is `.to be_falsy` for falsy values or `.to be_false` for false values.

`.to be_nil` test for equality with nil.

`.to match()` matches against a regex expression, for instance:

```
expect(agent.number.to_i).to match(/\d{3}/)
```

You can use simple comparison operators like > < `expect(agent.kill_count).to be >= 25 `

`.to be_a`, `to be_an`, `.to be_an_instance_of` are used to test for classes and instances, very useful for object oriented code:
```

```

`.to raise_error` tests to see if the code raises an error, which is often the expected and desired behavior.

**References**

[Better Specs](https://www.betterspecs.org/)
[RSpec testing for beginners, part 1](https://code.tutsplus.com/articles/rspec-testing-for-beginners-part-1--cms-26716)
[RSpec testing for beginners, part 2](https://code.tutsplus.com/articles/rspec-testing-for-beginners-02--cms-26720)
