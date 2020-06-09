---
layout: post
title:      "CLI Data Gem Project"
date:       2020-06-09 16:09:31 +0000
permalink:  cli_data_gem_project
---


I found this first portfolio project to be challenging yet ultimately rewarding. I chose to get data from the Spotify API because I am a big music nerd and knew that Spotify was a rich source of data about music (for an example that goes in far greater depth than my project, see http://everynoise.com/). First I set up the environment, basic file structure, and requirements (including dotenv and httparty which are needed to get data from the API). I divided my code into three classes, CLI, Artist and API. I first wrote the code that didn't need data from the API: the CLI with its menu-driven interface, and an initial draft of the Artist class that initialized artist objects that the CLI could get data from. At first I just put placeholder data arrays in the Artist class, leaving the API for later. I created the methods to print the data in a numbered list, as follows:
```
def print_numbered_list(array)
    array.each_with_index do |item, index|
      puts "#{index + 1}. #{item}"
    end
  end
```
(this is the generic version, that I later made to make the code more DRY)

The Spotify API requires authentication using OAuth2, and this was a major challenge, since I had no prior experience doing authentication. Developers can easily obtain a client_id and client_secret for their from https://developer.spotify.com/dashboard/. But then these keys must be sent to Spotify's servers in the proper way, as detailed here https://developer.spotify.com/documentation/general/guides/authorization-guide/ (specifically. Spotify's documentation did not explain how to send this request using Ruby. Morever, HTTParty does not support OAuth authentication, so I needed to look for another gem. There is an "oauth2" gem that appeared to have the neccessary methods but after inputting the relevant data, I only received errors from the server. I tried the command line methods using curl and similarly received errors. I was at an impasse. After some googling and looking at the issue pages of various gems, I finally realized that the problem was that the data needed to be base64 encoded. I finally found a gem that properly handled the authentication, RSpotify. (It also provided an easier way to access the API, but that would somewhat defeat the purpose of this project). Their interface is simple, all that is needed to access the API is
```
RSpotify.authenticate(ENV['CLIENT_ID'], ENV['CLIENT_SECRET'])
```
The environment variables can be called like this because of the dotenv gem, having been placed in a .env file  for security reasons. Then, to get the token:
```
@token = RSpotify.client_token
```

Getting data from the API can appear intimidating but is relatively straightforward once you know what to do. To get the data in the first place, use the HTTParty gem:
```
HTTParty.get(url, 
      {headers: {"Authorization" => "Bearer #{@token}"}}
    )
```
The Pry gem then comes in handy in examining the data retrieved from Spotify's servers. There are hashes inside arrays and vice versa, but if you examine the data structure one level at a time, it is not too complicated to deal with. The .class method comes in handy. When querying the artist's top tracks, storing it in a variable, top_tracks.class verifies that the initial response received is converted into an HTTParty::Response object, a hash-like object. top_tracks.length is 1, and top_tracks.keys returns ["tracks"]. top_tracks["tracks"].class verifies that it is an array, the object we need to iterate over to get the tracks. Looking at the first elements, top_tracks["tracks"][0] is a hash, and with the .keys method you can verify that it includes a key "name". Finally, top_tracks["tracks"][0]["name"] returns the name of a track, the value we were looking for. I then wrote a method for iterating through the tracks (again, this is the DRY version that I refactored from all my other methods at the end).

```
def return_array_with_names(array)
    name_array = []
    array.collect do |item|
      name_array << item["name"]
    end
    name_array
  end
```

It is also important to verify your url to call the API, something that gave me some trouble. You must know the artist id to get the proper url, which I handled by using Spotify's search endpoint (I return the first result for whatever the user queries), then interpolating it into the url string: "#{@@base_url}artists/#{id}/top-tracks?country=US". Pry is useful here for verifying that the url is correct.
