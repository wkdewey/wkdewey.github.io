---
layout: post
title:      "Testing JavaScript code with Mocha"
date:       2021-06-11 16:02:47 +0000
permalink:  testing_javascript_code_with_mocha
---


In previous posts I talked about testing Ruby code in RSpec. What if you want to test your JavaScript code? The most common framework for JavaScript testing is Mocha. As we will see, the syntax is quite similar to RSpec and other testing frameworks. The differences primarily involve the syntactic features of JavaScript.

**Installation**

If you want to install Mocha globally, type into your terminal application:
```
npm install --global mocha
```
If you want to install Mocha as a dependency for a specific project (which you have initialized with npm):
```
npm install --save-dev mocha
```
Finally, you can just type `npm install mocha` within the directory where you will create your test folder.

**Example of a test**
Create a folder *test* and a file within it *test.js*, with the following contents:
```
var assert = require('assert');
describe('Array', function() {
  describe('#indexOf()', function() {
    it('should return -1 when the value is not present', function() {
      assert.equal([1, 2, 3].indexOf(4), -1);
    });
  });
});
```
Now you can run the test, from the directory where you installed Mocha :
```
./node_modules/mocha/bin/mocha
```
You can also put the following in package.json
```
{"scripts": {
  "test": "mocha"
}
}
```
and then you can simply type `npm test` to run your tests. The test output looks like this:
```
  Array
    #indexOf()
      ✓ should return -1 when the value is not present


  1 passing (9ms)
```
Tests will have a check mark if they pass, and an x with an error message if they fail.

**How testing works**

The above code tests a function named `indexOf()`. `describe()` and `it()` work the same as in RSpec, providing convenient labels to organize test code and output, and wrapping the actual tests that go within `it()`. These methods take the parameters of a label and an anonymous function The code uses the `assert` library to test the code, which is built into Mocha. [Assert](https://nodejs.org/api/assert.html) can test for a number of conditions. The above method `.equal()` takes in two values, the value produced by the code you are testing, and the value you want it to equal. For instance, `assert.equal(foo, 'bar');`.  Here  are some other testing methods that go with assert
```
assert.typeOf(foo, 'string');
```
typeOf() tests the type of a given value, in this case making sure the value in `foo`, "bar", is a string.
```
assert.lengthOf(foo, 3)
```
lengthOf() tests for the length of a value; in this case "bar" has a length of 3.
```
assert.property(tea, 'flavors');
assert.lengthOf(tea.flavors, 3);
```
property() tests that a given object has a given property. In this case, that whatever object stored in `tea` has the property with key "flavors". You can also test the length of that property with lengthOf(), passing in `tea.flavors` as the argument.

**Different styles of tests**
There are three styles of tests that are common. Besides the `assert` function you saw above, there is also [should](https://github.com/shouldjs/should.js) and [expect](https://github.com/Automattic/expect.js), which have their own separate frameworks that work with Mocha. The Chai framework allows you to use all three styles.

*Expect* works the same way as RSpec, it creates tests that read like English, but work much the same as the above tests with assert. Note how you can chain on another test with `.with`, after testing `.to.have.property` (with assert you needed a separate test)
```
expect(foo).to.be.a('string');
expect(foo).to.equal('bar');
expect(foo).to.have.lengthOf(3);
expect(tea).to.have.property('flavors')
  .with.lengthOf(3);
```

*Should* is similar to a deprecated testing syntax in RSpec. It also is an English-like syntax and has equivalent methods to the above
```
foo.should.be.a('string');
foo.should.equal('bar');
foo.should.have.lengthOf(3);
tea.should.have.property('flavors')
  .with.lengthOf(3);
```

**Asynchronous tests**
It is possible to do Mocha testing asynchronously. Suppose there was a User object that saved, using AJAX methods, to the database. You could test it as follows:
```
describe('User', function() {
  describe('#save()', function() {
    it('should save without error', function(done) {
      var user = new User('Luna');
      user.save(function(err) {
        if (err) done(err);
        else done();
      });
    });
  });
});
```
done() is a function passed as a callback to it(). Mocha will wait for the callback to be called; if it is called with an error instance or a falsy value, it passes. if done() is called twice by the test code, the test code will fail.

There is another syntax using `eventually` that can be used for asynchronous tests:
```
beforeEach(function() {
  return db.clear().then(function() {
    return db.save([tobi, loki, jane]);
  });
});

describe('#find()', function() {
  it('respond with matching records', function() {
    return db.find({type: 'User'}).should.eventually.have.length(3);
  });
});
```
Note that this code also illustrates the `beforeEach() ` method. beforeEach() is a way to take setup actions before writing your test code.

**References**

[Mocha](https://mochajs.org/#examples)
[Chai Assertion Library](https://www.chaijs.com/)
