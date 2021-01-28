---
layout: post
title:      "Deploying my JavaScript Rails app"
date:       2021-01-28 01:10:57 +0000
permalink:  deploying_my_javascript_rails_app
---


**Deploying the front end**

I have finished with my changes, for now, on the Monastery Gazetter app, so now I am in the process of deploying my app. I decided to deploy the front end of the app on Github Pages. If you have already put the front end on Github, as long as you have a repository and an index.html page, you can just change the setting on your repository. In the Settings page, scroll down to GitHub Pages, and select your main (master) branch as the source. Your app will then be live at http://username.github.io/repository.
Mine is now at https://wkdewey.github.io/javascript-portfolio-project-monastery-gazetteer-frontend/

**Deploying the backend**

Deploying the Rails backend is more complicated. First of all, you need to have a Git repository and the database should be PostgreSQL--Heroku does not work with SQLite. You can set Postgres as your database when you create a new Rails app: `rails new myapp --database=postgresql`. Or else change `gem 'sqlite'` to `gem 'pg'` in your Gemfile. However, switching the database on an existing app can cause bugs. 

You also should create a free Heroku account, and install the Heroku CLI. Instructions are here: https://devcenter.heroku.com/articles/heroku-cli#download-and-install. Then you can log in with `heroku login` (As it happened, I previously installed Heroku)

Heroku gives instructions for creating a welcome index page by adding a welcome controller, since Rails 6 does not provide an index page by default, but this is unecessary if you are using Rails as an API-only backend to a JavaScript frontend. In fact, it will not work as I discovered after a couple hours of debugging.

Heroku expects you to have an up to date version of rails, so this may need to be changed in your gemfile, i.e. `ruby "2.6.6"`. You may need to run `bundle update --ruby` and change your Gemfile.lock file. See https://devcenter.heroku.com/articles/ruby-versions for more information.

Next, run the command `heroku create`. If you have done the setup correctly, it should create a Heroku server for you, like `https://young-forest-03120.herokuapp.com`. Then push your git code onto heroku, with `git push heroku main` (or master).

If this is successful, you can migrate your database with `heroku run rake db:migrate` and seed the database (if you have a seed data file) with `heroku run rake db:seed`

At this point you should have a working frontend app and backend app, but they won't yet work together. The only point of connection between the two is the AJAX request (I used fetch). This means you need to change the fetch urls. In my app, all requests were built from the variable BACKEND+URL, which originally pointed to localhost. So it now needed to be `const BACKEND_URL = "https://young-forest-03120.herokuapp.com"`

Congrats! You now have a full stack app deployed to the !nternet. Now you need to make sure that all parts of it are functioning properly, the production environment may cause tricky debugging. A future blog post will discuss the challenges of configuring Cloudinary to work in a deployed app.


**References**

[GitHub Pages](http://pages.github.com)
[Getting Started on Heroku with Rails 6.x](https://devcenter.heroku.com/articles/getting-started-with-rails6)

