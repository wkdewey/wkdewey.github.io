---
layout: post
title:      "Making Web Apps with Sinatra"
date:       2020-07-12 20:18:22 +0000
permalink:  making_web_apps_with_sinatra
---

Sinatra is a simple framework that allows you to make web apps with Ruby, a good way to learn the basics of HTTP, SQL databases, and RESTful routes in preparation for Ruby on Rails. I chose to create a travel log in which users could create records of places they have visited, and see the travels of other users. It was loosely inspired by the website  [Most Traveled People ](http://mtp.travel/), which allows users to record their progress in a competition of who has travelled to the most locations in the world. In addition to Sinatra, the app uses Active Record to provide easy access to an SQL database, and the gem bcrypt to handle password security.

The most important step in project planning is to determine which models you will use and what attributes they will have. I began with Users and Places joined in a one-to-many relationship User has_many Places and Places belongs_to Users. A user has the attributes of name, password, and hometown, and a place has the attributes of country and city. The corneal gem available at https://github.com/thebrianemory/corneal can be used to set up the structure of your app, in a manner that emulates features of Rails, with the commands `corneal new APP-NAME` which sets up required gems in your environment and `corneal scaffold NAME` which creates the model-view-controller structure for the models you specify, complete with. Much of the challenge of an MVC app consists of setting up the initial migrations and associations to work properly. Although corneal can create migration files, I still had to create several new migrations to get my database to work properly, for instance to use password_digest instead of password in the users table.

Creating the basic structure of the app was straightforward, having gotten a lot of practice creating other apps with RESTful routes. I used validations, from ActiveRecord, to make sure that users input valid data with all the necessary fields when they created. I created routes and views to create, update, delete, and read (CRUD) new places, and also to edit user accounts within the app. The routes have logic to process data from the user and pass data to the views, and the views contain logic to display the data as well as html for. ERB files handle this combination of HTML and Ruby, with Tuby code in <% brackets %>. I created helper methods to determine the current user and their logged-in status, so that users must be logged in to see data and are only able to edit their own places and account. These in turn rely on sessions (enable :sessions) as a means of keeping track of logged in users in a way that is globally available throughout the app.
```
helpers do
    def logged_in?
      !!session[:user_id]
    end

    def current_user
      User.find_by(:id => session[:user_id])
    end
end
```

The big challenge came when I decided to make the relationships more complex. Users and Places really have a many-to many relationship, as a place could be visited by many people, necessitating the creation of a join table and class named UserPlaces. I also wanted to implement Country and City classes so that I could create views for cities and countries. Implementing this was more complex and it was especially important to get the associations in the database right.
```
  class User < ActiveRecord::Base
	has_many :user_places
  has_many :places, through: :user_places
  has_many :cities, through: :places
  has_many :countries, through: :cities 
	end
```
A number of bugs needed to be fixed once I created the join table; it broke a surprising amount of my code, which was written on the assumption that a place could only have one user. With the new Country and City model it became more complicated to create a new instance of a Place than simply passing strings provided by the users into the .new method. New instances of countries and cities needed to be created as well.
```
country = Country.find_or_create_by(name: params[:country])
    city = City.find_or_create_by(name: params[:city], country_id: country.id)
    place = Place.new(name: params[:name], city_id: city.id)
    if !place.users.include?(current_user)
      place.users << current_user
    end
```
In addition to creating new instances of Countries and Cities from the parameters, it is also necessary to avoid duplicating data by checking for existing instances of the country, city, and creating user. These complex associations allow users to manage much more information in the app, for instances they can choose places they have visited from a checklist. This ERB file takes advantage of ActiveRecord's ability to automatically handle arrays instead of the programmer having to track each element manually (note that the @place variable contains all the places in the database). 
```
<form>
<h3>Select a place</h3>
<% @places.each do |place| %>
  <input type="checkbox" name="**user[place_ids][]**" id="<%= place.id %>" value="<%= place.id %>" <%='checked' if @user.places.include?(place) %>><%= place.name %></input><br>
<% end %>
<input type="submit" name="submit" id="submit">
</form>
```
 Then `user.place_ids = params[:user][:place_ids]` (where user is an instance of User) can be used to associate all selected places with the user.
 With the added models, users can also browse places by city and country, and see all users who have visited them (in this case, the .distinct method was necessary to avoid the duplicate records that result from all the complex joins).
