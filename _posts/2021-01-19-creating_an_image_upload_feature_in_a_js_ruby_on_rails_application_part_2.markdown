---
layout: post
title:      "Creating an Image Upload Feature in a JS Ruby on Rails Application Part 2"
date:       2021-01-20 01:31:21 +0000
permalink:  creating_an_image_upload_feature_in_a_js_ruby_on_rails_application_part_2
---


In my [previous post](https://github.com/wkdewey/javascript-portfolio-project-monastery-gazetteer-frontend/commits/master) I described how to create a file upload feature for images, using HTML and JavaScript in the frontend, posting a FormData object to the Ruby on Rails backend and using ActiveStorage to associate it with records in the SQL database

By default, this setup will cause images to be stored in local storage. Local storage takes up space and cannot offer the same performance as cloud-based services. I chose to use Cloudinary, a hosting system that is easy to integrate with Rails. First of all, include the cloudinary gem in your Gemfile `gem 'cloudinary'`
and run `bundle install`. Make sure to create a free account at Cloudinary, it will tell you how to set it up. Then configure your app to use cloudinary as follows:

Add these lines to storage.yml:
```
cloudinary:
  service: Cloudinary
```
to development.rb and production.rb in your config/environments file, add this line:
```
config.active_storage.service = :cloudinary
```
Make sure that the base url is also properly configured in development.rb and production.rb (I used localhost but of course this will change if you actually deploy your site)
```
Rails.application.routes.default_url_options = { host: "http://localhost:3000" }
```

Also, download the cloudinary.yml file from your Cloudinary dashboard and put it in your config file. It contains API keys and secrets so it must be included in .gitignore, do not upload it to GitHub or you will compromise security

In my Rails front-end, I had been storing data related to the database records as JavaScript objects. Storing the image data in JavaScript is straightforwardly simple given that I created an image_url field on the Rails side. From the fetched data, I can extract the image_url:
```
new Monastery(
        data[key]["id"],
        data[key]["attributes"]["name"],
        data[key]["attributes"]["location"],
        data[key]["attributes"]["religious_tradition"],
        data[key]["attributes"]["figures"],
        data[key]["attributes"]["image_url"]
      );
```

My monastery objects look like this:
```
constructor(id, name, religious_tradition, image_url) {
    this.id = id;
    this.name = name;
    this.religious_tradition = religious_tradition;
    this.image_url = image_url;
  }
```
Then I added an image to the cards where I was showing my monasteries and figures:
```
    if (this.image_url) {
      let image = document.createElement("img");
      image.src = this.image_url;
      image.alt = `image of ${this.name}`;
      image.classList.add("card-img-bottom", "img-fluid");
      cardDiv.appendChild(image);
    }
```
The classList attributes are for Bootstrap, to make sure the images are sized and properly positioned within the card. Note that you should include

Although image_url will appear to be a "blob" file at your base URL, it will automatically download the image from Cloudinary and it is not actually local. (Please note that so far, I have only tested this on my locally hosted application. After I deploy the application on the Web, I may have to change the setup , which I will detail in a future post.)

I had both a "create" and "edit" form for Monasteries and Figures, corresponding to the RESTful actions on the Rails side. One thing to consider with HTML is that the file input does not automatically note whether a file has already been uploaded, if it already exists in the database. It will still say "No file chosen" How might you change that? Directly putting the file URL in the value attribute does not work, this is blocked for security reasons. Instead, it's better to use JavaScript to check if a file is uploaded (i.e., my Monastery or Figure objects have the proper attribute) and add an appropriate message. I decided to display the image:
```
    if (image_url) {
      h3.textContent = "Upload new image";
      const img = document.createElement("img");
      img.src = image_url;
      img.alt = `existing image at ${image_url}`;
      img.width = "150";
      form.appendChild(img);
      form.appendChild(h3);
    } else {
      h3.textContent = "Upload image";
      form.appendChild(h3);
    }
```

**References**
[Angus Morrison on Medium: How to Upload Images to a Rails API](https://medium.com/better-programming/how-to-upload-images-to-a-rails-api-and-get-them-back-again-b7b3e1106a13)
[Cloudinary](https://cloudinary.com/)


