---
layout: post
title:      "Creating a research app with Rails"
date:       2020-08-12 13:48:15 +0000
permalink:  creating_a_research_app_with_rails
---


This project was the most challenging that I have done so far at Flatiron. I learned a valuable lesson about the importance of planning. My initial idea was to create a project that would be useful to people in religious studies, the focus of my pre-coding career. I first planned out the models for the texts, authors, religious traditions, projects and notes that researchers can track.

![https://drive.google.com/file/d/1hXpM07ZuXvLjjzWCNt7lG7_533eMdq1R/view?usp=sharing](http://)

The models have a number of has_many, belongs_to, and has_many, through ways but the most important is the many-to-many relationship between Projects and Texts, through Notes. Each association between a researcher's project and a text is recorded through a Note, which contains the user submittable attribute :content--where the researcher can write a note on the text associated with the project. The flaw in my planning was that I assumed that the app would, as it were, write itself after I sketched out the models, and that I had no need for wireframing. Because of this I ran into some trouble as the app grew more complex.

After putting in the models into the database and writing out the associations, the next step was creating login functionality. Unlike most of the RESTful functionality of the app, these actions require custom routes to be created:
```
 get '/login', to: 'sessions#new'
  post 'login', to: 'sessions#create'
  get '/logout', to: 'sessions#destroy'
  match '/auth/:provider/callback', to: 'sessions#create_omniauth', via: [:get, :post]
```

The omniauth functionality allows user to use their github account for authentication, bypassing the app's own authentication which is based on bcrypt. It requires one to obtain a client_id and secret for the app, which should be stored in an env file that is not tracked by Git (otherwise you could expose the app to hackers). This can be handled through the gem 'dotenv-rails'. At first I simply included this gem as 'dotenv' which caused github--it took days to track down this bug. Always be careful to read the documentation for how to use gems!

Then I created CRUD (Create, Read, Update, Delete) functionality on the Project and Text models. A Project belongs_to a Researcher (my model for users), while Texts exist independently but require an association with an Author and a ReligiousTradition. Rails makes much of this work easier, automating much of the work of routing, assigning parameters in the controller, and displaying information and forms in view. Here is the form for creating a project:
```
<%= form_for project do |f| %>
  <%= f.label :title %>
  <%= f.text_field :title %>
  <%= f.label :description %>
  <%= f.text_field :description %>
  <%= f.label :texts %>
  <%= f.collection_check_boxes :text_ids, texts, :id, :title %>
  <%= f.submit %>
<% end %>
```
However, some of my design choices at the beginning made the CRUD functionality unusually complicated. In order to create a text, an Author and ReligiousTradition must also be created. I also wanted to give users the option to either choose from an existing author and religious tradition or create new ones. In order to build the form, I initialized instances of a model like this in the create action:
```
@text = Text.new(project_ids: [params[:project_id]])
      @text.build_author
      @text.author.build_religious_tradition
```
This allowed me to provide options for the author and religious_tradition like this:
```
  <%= f.fields_for :author do |author| %>
    <%= author.label :id, "Existing author" %>
    <%= author.collection_select :id, authors, :id, :name %>
    <%= author.label :name, "New author" %>
    <%= author.text_field :name %>
    
    <%= author.fields_for :religious_tradition do |r| %>
      <%= r.label :id, "Religious tradition" %>
      <%= r.collection_select :id, religious_tradition, :id, :name %>
      <%= r.label :name, "input new religious tradition" %>
      <%= r.text_field :name %>
    <% end %>
  <% end %>
```
To implement the functionality that providing a new name overrides the existing options, I defined methods in the models to assign attributes conditionally, as this one in the Author model:
```
def religious_tradition_attributes=(religious_tradition)
    if religious_tradition[:name].empty?
      self.religious_tradition = ReligiousTradition.find_by(id: religious_tradition[:id])
    else
      self.create_religious_tradition(name: religious_tradition[:name])
      
    end
  end
```
In order to make these forms and methods work, it was also necessary to use the accepts_nested_attributes_for method on Text and Author classes, for :author and :religious_tradition respectively.

One of the project requirements was to create a nested route. I nested Texts under Projects so that texts could be associated with a project (see the assignment of :project_ids above).

The biggest challenge was creating the user-submittable attribute. My attempt to do this within the Text or Projects actions did not work and led to bugs that lasted for days. I was trying to input the note content via nested attributes but this approach resulted in duplicate notes being created, breaking the code. (because Notes is the join table, Notes are automatically created every time a Text is associated with a Project). A better approach was to directly create new and create actions for  Notes, and again use nested routes, nesting Notes under Projects. Secondarily I added a link to edit the note in my project show view, where I was already displaying the notes:
```
<%= link_to "edit note", edit_project_note_path(@project, text.note(@project)) %>
```

In order to be useful to researchers, the app should be extended further in the future. My models were chosen so that I could write functionality allowing researchers to group the texts by author or religious tradition, for example.
