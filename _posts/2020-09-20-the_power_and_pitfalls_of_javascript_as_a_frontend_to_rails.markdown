---
layout: post
title:      "The Power and Pitfalls of JavaScript as a frontend to Rails"
date:       2020-09-21 00:29:38 +0000
permalink:  the_power_and_pitfalls_of_javascript_as_a_frontend_to_rails
---


For this project, I again turned to my academic research in Tibetan Buddhism for inspiration. This is an app to allows you to explore monasteries and Tibetan Buddhist figures, and the relationships between them. A monastery can have many figures, obviously, but a Buddhist figure can also have many monasteries--their home monastery, but those they inhabited briefly to obtain an education, those they founded, and others they may have been connected to. This app would allow researchers to see that famous figures were influenced by a wide range of traditions through their connections to different monasteries. The many-to many relationship between monasteries and figures can be easily modelled in Rails with an SQL database, while JavaScript provides a frontend for a simple, one page interface in which users can explore monasteries, figures, and the relationships between then, and add new monasteries and figures, and their associations, to the database.

In many ways this project was more straightforward than the previous one. I followed the vertical development model outlined here: https://github.com/learn-co-curriculum/js-spa-project-instructions/blob/master/project-planning-tips.md. In this model, rather than building all your models, then all your controllers, all your views, etc, you start with one model and build vertically (migration, model, seed data, view logic, fetching). This method definitely saved me a lot of trouble in comparison with the Rails project, where my "horizontal" development process resulted in a lot of debugging when a change broke my code. It certainly did not eliminate debugging or complexity, but it did reduce the amount of time I spent with broken code, and the scale of the problem when I did make a change that caused my app to stop working.

Creating a single model and view stack was quite simple and straightforward. I created the model and migration with Rails' generators, and then created some seed data (I wanted to use real seed data for this project, rather than a faker). Because the backend in Rails uses an API, setting up the routes and controller is a little simpler than an ordinary Rails app. The routes look like this, in order to get a proper API url like http://localhost:3000/api/v1/monasteries:
```
namespace :api do
    namespace :v1 do
      resources :monasteries, only: [:index, :show]
    end
  end
```
The views are also quite simple, thanks to the use of the [Fast Json Api](https://github.com/Netflix/fast_jsonapi/). After including the gem and setting up the serializer file:
```
class MonasterySerializer
  include FastJsonapi::ObjectSerializer
  attributes :name, :location, :religious_tradition, :figures
end
```
all you need to do in the controller is
```
def index
    figures = Figure.all
    render json: FigureSerializer.new(figures)
  end
```
In the frontend I created a bare-bones html file with a header, buttons, and a div container for content,  then in the Javascript file added event listeners, which executed a fetch request for the Monastery data which returned the data in the form of JSON, used the data to create a Monastery object, then rendered this data in the DOM. Building up the view in the DOM may have been the most challenging part, as I did not directly use HTML but DOM manipulation methods like querySelector, createElement, textContent, and appendChild

Creating the second model was more challenging, but still relatively straightforward. This was the point at which I created the many-to many associations, and this always takes a while to set up in Rails, especially since I tested it in the console and created more seed data. Creating the Figure view in JavaScript was simply a matter of duplicating all my fetch and view methods in JavaScript. The challenging part was associating figures with monasteries on the JavaScript objects, so that I could show the associated Monasteries for each Figure and vice versa. My first attempt was 
```
class Monastery {
  constructor(name, location, religious_tradition) {
  constructor(name, location, religious_tradition, figures) {
    this.name = name;
    this.location = location;
    this.religious_tradition = religious_tradition;
    this.figures = [];
    if (figures) {
      for (const figure of figures) {
        const figureObject = new Figure(
          figure["name"],
          figure["lifespan"],
          figure["religious_tradition"]
        );
        this.figures.push(figureObject);
      }
    }
  }
```
The trouble with this solution of objects belonging to objects is, how could you then click on the associated object and see* it's* associated objects? Using this initial attempt to model the association, JavaScript ends up creating duplicate objects, and if you click on one of the them they do not retain the association. Solving this problem would lead to an infinite. Eventually I decided to just save the name attribute and look up the full object as needed:
```
for (const figure of this.figures) {
      const figureObject = Figure.find(figure);
      figureObject.render();
    }
```

Creating the forms and posting to the database were more difficult still. I built up the form through DOM manipulation, which took a lot of code (eventually I managed to shorten it with a helper method to create input elements on the form). Posting to the database was straightforward, taking the inputs and posting them using fetch and a configuration object
```
static postMonasteries(
    nameInput,
    locationInput,
    religiousTraditionInput,
    figureIds
  ) {
    let bodyData = {
      name: nameInput,
      location: locationInput,
      religious_tradition: religiousTraditionInput,
      figure_ids: figureIds,
    };
    fetch(MONASTERIES_URL, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(bodyData),
    })
      .then((response) => response.json())
      .then((monastery) => {
        const monasteryObject = Monastery.createFromJson(monastery.data);
        monasteryObject.render();
      });
  }
```
Actually getting the parameters from the form was tricky in the case of the input checkboxes I created for the join model (Monasteries that belong to Figures, Figures that belong to Monasteries). I assigned each checkbox a value of figure.id, but how to get the ID's of the selected checkboxes? Via StackExchange, I used the following method:
```
const checkboxes = document.getElementsByName("figure");//every checkbox was given this name attribute in the form
const figureIds = Array.prototype.slice
      .call(checkboxes)
      .filter((ch) => ch.checked == true)
      .map((ch) => parseInt(ch.value));
```
What this does is to convert the nodeList of checkboxes into an array, filter it for those that are checked, and create another array mapped with the id values. These id values are necessary for the Rails backend to associate the objects in the database. A second problem I faced was getting Rails to recognize the params I sent in JSON--it was not wrapping the figure_id properly, because it only recognized params that were attributes of the Monastery model itself. I ended up having to use accepts_nested_attributes and wrapping the params manually with `wrap_parameters :monastery, include: [:name, :location, :religious_tradition, :figure_ids].`--a hack, but otherwise it wouldn't recognize the figures_id param

In some ways, refactoring the JavaScript code was more challenging that writing the code in the first place. But my code became cleaner and I found ways to improve the functionality of the app. I extracted most of the functionality, the views and create forms and fetching to/from the database, into class and instances methods on the Monastery and Figure models. I reduced the number of fetch methods and also created a helper method to create form elements:
```
class Helpers {
  static createInputElement(id, type, name, value, placeholder) {
    const element = document.createElement("input");
    element.id = id;
    element.type = type;
    element.name = name;
    element.value = value;
    element.placeholder = placeholder;
    return element;
  }
}
```
I also decided to pre-fetch all instances of both models, so that I was not calling the database multiple times and all methods of my app always had access to the data they needed.

Is this design a good model for your app? I ended up, esentially, duplicating Rails functionality within JavaScript, recreating a number of built-in methods the hard way. If you use ERB for views and forms, Rails (specifically ActiveRecord) makes dealing with the associations much easier, with methods like collection_check_boxes, and automatically creating params from associations without having to construct the JSON yourself and hack it to get Rails to recognize it. However, JavaScript is necessary to get the instant-response interactivity for single-page web applications; it would be possible, but quite clunky, to handle all the logic with Ruby and ERB. Perhaps in the future I will come up with a better solution for handling the JavaScript associations



