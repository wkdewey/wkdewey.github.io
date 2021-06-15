---
layout: post
title:      "Using Mocha to test a JavaScript web app "
date:       2021-06-15 17:23:24 +0000
permalink:  using_mocha_to_test_a_javascript_web_app
---


I have been using the Mocha framework to test the frontend of a web app that I created with JavaScript, [Tibetan Monastery Gazetteer](https://github.com/wkdewey/monastery-gazetteer). Unit testing in Mocha is quite similar to RSpec testing for Ruby, as the principles of testing are the same regardless of the language. My app poses some challenges as far as testing is concerned. Although much of my code controls the appearance of a web page, Mocha is designed for a Node.js environment, so it does not has access to the DOM without addons. One approach is to set up Mocha to be run in the browser, but there are workarounds so that you can use to test the DOM functionality in the command line. A second challenge is that some of the app's functionality is asynchronous, and so is the functionality that can load an HTML file into Mocha.

**Installing Mocha and Chai**

I first navigate to the project directory in the terminal and typed `npm install --save-dev mocha`. I also want to install the [Chai library](https://www.chaijs.com/), because it provides more test functionality  than Mocha's built in functions: `npm install chai`. I also need to create a package.json file so that the tests could be run with `npm test`, with the following content:
```
{
  "scripts": {
    "test": "mocha"
  },
  "dependencies": {
    "chai": "^4.3.4"
  }
}
```

The first function I have chosen to test is the constructor of the BuddhistEntity class, which in my project is the parent class of Monastery and Figure.
```
class BuddhistEntity {
  constructor(id, name, religious_tradition, image_url) {
    this.id = id;
    this.name = name;
    this.religious_tradition = religious_tradition;
    this.image_url = image_url;
  }
	end
```

In order to write the test functions in test/test.js, it is necessary to require the assert test library and the file where the class is defined and assign them to variables.

```
var assert = require("chai").assert;
BuddhistEntity = require("../src/buddhistentity");
``` 
If you write any test code using BuddhistEntity, at this point it will throw an error that BuddhistEntity is not defined. It is necessary to add a line exporting the code at the end of buddhistentity.js:
```
module.exports = BuddhistEntity;
```

**A simple test**

As with RSpec, you organize your tests within `describe` methods. Since I am testing the BuddhistEntity class, that is the object of the describe method that wraps all the other tests:
```
describe("BuddhistEntity", function () {
}
```
First I need to create some data to test the function:
```
  const testEntity = new BuddhistEntity(
    "1",
    "Buddha",
    "Buddhism",
    "https://example.com/buddha.jpg"
  );
```
Now, to test the function, I want to make sure it returns an object of the correct type:
```
describe("#constructor()", function () {
it("returns an instance of class BuddhistEntity", function () {
      assert.typeOf(testEntity, "Object");
      assert.instanceOf(testEntity, BuddhistEntity);
    });
}
```
`assert.typeOf` tests if testEntity is of type Object, and `assert.instanceOf` tests if testEntity is an instance of BuddhistEntity.

In order to test that all the properties are initialized correctly, I can use the following code:
```
    it("has property id", function () {
      assert.property(testEntity, "id");
    });
    it("has property name", function () {
      assert.property(testEntity, "name");
    });
    it("has property religious_tradition", function () {
      assert.property(testEntity, "religious_tradition");
    });
    it("has property image_url", function () {
      assert.property(testEntity, "image_url");
    });
```
Each of these tests make sure that the saved entity has the given property name.

**Testing an asynchronous functions**

Another function in BuddhistEntity requests the data from a given URL (the API endpoint I created for the backend), and returns the data in JSON format:
```
  static fetchEntries(url) {
    return fetch(url)
      .then((response) => response.json())
      .then((json) => json["data"]);
  }
```
Just to keep it simple, I will only test if the function returns a promise.
I need a URL for it to retrieve, so I need to copy the urls I defined in index.js:
```
  const BACKEND_URL = "https://young-forest-03120.herokuapp.com";
  const MONASTERIES_URL = `${BACKEND_URL}/api/v1/monasteries`;
```
Testing the code seems simple enough:
```
it("returns a promise", function () {
      const entries = BuddhistEntity.fetchEntries(MONASTERIES_URL);

      assert.typeOf(entries, "Promise");
    });
```
Unfortunately, Mocha, based in the node.js environment, doesn't understand the `fetch` method. I need to use the package `isomorphic-fetch` so that it doesn't throw an error. I install it by typing `npm install --save isomorphic-fetch es6-promises` into terminal, then inserting the line `require("isomorphic-fetch");` into both index.js and the test file. After this `fetch` will execute correctly, and the tests will pass.

To test whether the JSON data returns correctly, I will need to use Mocha's asynchronous functionality. I will save that for the following function I'm testing.

**Testing functions that manipulate the DOM**

The next function I want to test is one that makes things happen on the web page:
```
  render(contentContainer, div, link) {
    div.classList.add("col-md-4");
    contentContainer.appendChild(div);
    ...
    }
```
I can define the parameters, which all refer to DOM elements, as they would appear in the page.
```
    const contentContainer = document.querySelector("#content-container");
    const div = document.createElement("div");
    const link = document.createElement("a");
```
But this raises a bigger question: how does the test file know what `document` is? It is in a node.js environment, so it doesn't understand anything browser related.
The solution is to use the JSDOM module, installing it with `npm install --save-dev --save-exact jsdom jsdom-global`.
```
const { JSDOM } = jsdom;
```

In order to load the HTML file into JSDOM, and define `document` based on it, I need to use an asynchronous function `fromFile()`. Asynchrony poses some challenges for getting tests to pass, but Mocha does provide suitable methods for using a function with promises. Pass it the callback `done` and then call `done` again after the asynchronous code is done.
```
  describe(".render()", function () {
    let contentContainer;
    let div;
    let link;
    beforeEach(function (done) {
      JSDOM.fromFile("./index.html")
        .then((dom) => {
          global.document = dom.window.document;
          contentContainer = document.querySelector("#content-container");
          div = document.createElement("div");
          link = document.createElement("a");
        })
        .then(done, done);
    });
```
Here I am setting up the necessary parameters for a test. `document` can only be initialized after loading the HTML file, and in fact it should be `global.document` so that my functions outside test.js can use it.  It is necessary to initialize the variables before the asynchronous function, otherwise they will not be available outside the function. But if you use the `beforeEach` hook, Mocha will wait for this function to complete before moving on with the tests. After this setup, the test can be simple and follow the usual Mocha patterns:
```
    it("displays the entity", function () {
      testEntity.render(contentContainer, div, link);
      assert.include(contentContainer, div);
    });
```

I am calling `render` as an instance method on `testEntity` (see above where I define it), using the parameters I have just defined above. Then I run my test, making sure that the new div has been added to `contentContainer`. Of course there is much more code in the function I could test, but this shows that the function is correctly manipulating the DOM.

This has been a summary of some of the important methods of testing with Mocha, as applied to one of my projects. Of course there is much more to testing than this, but hopefully it will get you started on some of the more important ways you can test your code.

**References**
[Tibetan Monastery Gazetteer project](https://github.com/wkdewey/monastery-gazetteer)
[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch)
[JSDOM](https://github.com/jsdom/jsdom)
[StackOverflow: Loading an HTML file into JSDOM](https://stackoverflow.com/questions/45945798/loading-existing-html-file-with-jsdom-for-frontend-unit-testing/45961549#45961549)
[Chai Assert API](https://www.chaijs.com/api/assert/)


