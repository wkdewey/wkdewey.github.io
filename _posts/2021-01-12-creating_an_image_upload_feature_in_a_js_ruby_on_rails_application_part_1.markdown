---
layout: post
title:      "Creating an image upload feature in a JS/Ruby on Rails application: Part 1"
date:       2021-01-12 19:17:51 +0000
permalink:  creating_an_image_upload_feature_in_a_js_ruby_on_rails_application_part_1
---

In my [Monastery Gazetteer application](https://wkdewey.github.io/the_power_and_pitfalls_of_javascript_as_a_frontend_to_rails) ([Github frontend](https://github.com/wkdewey/javascript-portfolio-project-monastery-gazetteer-frontend), [Github backend](https://github.com/wkdewey/javascript-portfolio-project-monastery-gazetteer-back), I already provide users the opportunity to create and update records on monasteries and figures via forms. These send the information in JSON hashes, which can then be parsed by Rails (operating in API mode). What I wanted to do was additionally give users the opportunity to upload an image of the monastery or figure, and 

If you have ever applied to a job online and uploaded your resume or cover letter, you are familiar with the file upload capabilities provided by HTML. The HTML is simple enough, as you can see from the example from the W3schools website:
```
<form action="/action_page.php">
  <input type="file" id="myFile" name="filename">
  <input type="submit">
</form>
```
All you need is an input element with the type attribute set to "file" and the proper id and name attributes. Creating the file input also be simply done dynamically via JavaScript manipulating the DOM, an excerpt from my Monastery Gazeteer JavaScript code:
```
  static createImageUpload(form) {
    const h3 = document.createElement("h3");
    h3.textContent = "Upload image";
    form.appendChild(h3);
    debugger;
    const upload = createInputElement(
      "upload-image",
      "file",
      "filename",
      this.image_url
    );
    upload.accept = "image/png, image/jpeg, image/gif";
    form.appendChild(upload);
    const br = document.createElement("br");
    form.appendChild(br);
  }
```
On it's own, creating a file input doesn't create any useful functionality. Any backend processing is ordinarily handled by the file specified in the action attribute (in the above example, written in PHP). Alternatively, a JavaScript event handler can be attached to the submit button, which sends a fetch request back to the backend to modify the database. This is what I did in my JS/Rails application. I will not go into the details of how this works, this post focuses on the upload specifically.

Associating the image file with the proper database record, so that later it can be retrieved and displayed, is a problem for the backend, and that is more complicated to deal with. It is still more complicated by the fact that my image upload feature is intended to complement the rest of the form, which consists of simple text inputs and checkboxes. How to send an image along with text (which can be straightforwardly converted to a format that Rails can parse)? There are two options, either converting the image to a JSON string or sending the necessary information via a JavaScript FormData object.

Of these two options, FormData (which consists of text in key-value pairs) is by far less complicated and requires less modification to your JavaScript and Rails code.
From the JavaScript end, it is easy to create a FormData object and pass it the value corresponding to the image.

First you grab the image:

```
 const imageInput = document.querySelector("#upload-image").files[0];
```
The input element in the DOM has the attribute "files" which is an array of the files that have been uploaded. In this case, there is only one file which is what we want, the first element of the array.

Then you send it to the server
```
static uploadImage(imageInput, id) {
    const formData = new FormData();
    formData.append("image", imageInput);
    fetch(`${MONASTERIES_URL}/${id}`, {
      method: "PATCH",
      body: formData,
    });
  }
```
Note that there is no need to send a header with the fetch request (as there would be with JSON data), the data will automatically be in the "multipart/form-data" format. The code to set up the fetch request looks like this: I create the object first with the JSON data, then append the image to it with a separate method:
```
  static postMonasteries(
    nameInput,
    locationInput,
    religiousTraditionInput,
    imageInput,
    figureIds
  ) {
    const bodyData = {
      name: nameInput,
      location: locationInput,
      religious_tradition: religiousTraditionInput,
      figure_ids: figureIds,
    };
    fetch(MONASTERIES_URL, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(bodyData),
    })
      .then((response) => response.json())
      .then((monastery) => {
        if (imageInput) {
          Monastery.uploadImage(imageInput, monastery.data.id);
        }
```

What is actually sent is a hash that looks like this
```
"file"=>#<ActionDispatch::Http::UploadedFile:0x00007fc53f1be638 @tempfile=#<Tempfile:/var/folders/sl/nh9hb7j102q9xcwsy3jrtskw0000gn/T/RackMultipart20200206-63800-1mm9z70.json>, @original_filename="test_data.json", @content_type="application/json", @headers="Content-Disposition: form-data; name=\"file\"; filename=\"test_data.json\"\r\nContent-Type: application/json\r\n">, "controller"=>"upload_files", "action"=>"create"
```

The trouble with FormData is that it is not compatible with JSON. More particularly, the array of IDs that associate my two models (the associated figures of a monastery, and the associated monasteries of a figure) cannot easily be converted into a format that can be parsed by Rails, because it is turned into a string format, which prevents Rails from permitting these parameters and associating the records in the database. There are [ways  around this](https://stackoverflow.com/questions/54739544/using-javascript-to-submit-an-array-and-a-file-object-to-a-rails-backend) like Direct Upload, but the simplest way is to send the image information seperately as FormData, and keeping the rest of the form data as JSON.



How to associate the data from the frontend? You need to install ActiveStorage in Rails. This can be done by running `rails active_storage:install` in the terminal in your project directory. A migration is created, with the tables active_storage_blobs and active_storage_attachements, which will hold the data you want. Next run `rails db:migrate`

There is no need to modify to your model migrations to include your image. Rather, each model that has the image needs the line `has_one_attached :image`. A potential pitfall (especially while testing) is that now all records will have an "image" attribute regardless if the image is actually attached. If you are playing around with the console, or some code needs to check this, you need to use the "attached?", like `record.image.attached?`

Fortunately, there is not much else you need to do in Rails with your controller actions to get the image to upload.  It is possible that the params won't be compatible with the JSON params, as Rails does not automatically wrap params in form-data format. I solved this problem by permitting the image params separately
```
  def figure_params
    params.require(:figure).permit(:name, :lifespan, :religious_tradition, :image, monastery_ids: [])
  end

  def image_params
    params.permit(:image, :id)
  end
```
Aside from checking whether an image is being received and permitting the params accordingly, there is nothing special you need to do to update the record with your image:
```
  def update
    figure = Figure.find_by(id: params[:id])
    if params[:image]
      figure.update(image_params)
    end
    figure.update(figure_params)
    if figure.save
      render json: FigureSerializer.new(figure), status: :accepted
    else
      figure = Figure.find_by(id: params[:id])
      render json: { errors: figure.errors.full_messages }, status: :unprocessible_entity
    end
  end
```

Finally, I wanted to have an image_url attribute in my serialized API. I did this by defining a get_image_url method in my models:
```
  def get_image_url
    if self.image.attached?
      url_for(self.image)
    end
  end
```
And in the serializer file creating a custom :image_url attribute (I had been using fastjson_api for the rest of the attributes):

```
attribute :image_url do |monastery|
    monastery.get_image_url()
  end
```


Stay tuned for future posts on how to store images using the Cloudinary service, to avoid having to use local storage, and how to retrieve the images from the database and display them in your website.


References:
https://www.w3schools.com/howto/howto_html_file_upload_button.asp
https://medium.com/@aresnik11/how-to-upload-a-file-on-the-frontend-and-send-it-using-js-to-a-rails-backend-29755afaad06
https://medium.com/better-programming/how-to-upload-images-to-a-rails-api-and-get-them-back-again-b7b3e1106a13
https://api.rubyonrails.org/classes/ActiveStorage/Attached/One.html
https://stackoverflow.com/questions/54739544/using-javascript-to-submit-an-array-and-a-file-object-to-a-rails-backend
