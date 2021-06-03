---
layout: post
title:      "Testing your code: Examples from my command line project"
date:       2021-06-03 18:21:04 +0000
permalink:  testing_your_code_examples_from_my_command_line_project
---


In my last blog post, I explained how to test your Ruby code with RSpec. In this post, I will provide an example of testing in action, based on my Ruby command line project Music Explorer (which interfaces with the Spotify API to browse music artists and associated information). This was the first project for Flatiron School and I chose it to demonstrate testing because it is a relatively simple application, using a single language Ruby, and not incorporating a complex frameworks like Rails.

After you have installed Rspec (see my last post), it is best to write a separate .spec file for each class. This is not a requirement but helps to organize your test code and the output better. My project had the bulk of its logic in the CLI, Artist and API classes. The CLI provides the text-based command line interface, the Artist class contains data about artists (retrived from the API), and the API class sends data back and forth from the external API.

**Testing a class**

I will focus on my Artist class because it is the simplest class to test, with the fewest methods.

```
class MusicExplorer::Artist

  attr_accessor :name, :top_tracks, :albums, :genres, :related_artists

  @@all = []
  
  def self.create_artist(artist_query)
    artist_data = lookup_artist(artist_query)
    self.new(artist_data)
  end

  def self.lookup_artist(artist_query)
    #Calls the API with the user's query, in order to get a hash of artist data
    
    api = MusicExplorer::API.new(artist_query)
    api.retrieve_artist_data
  end

  def self.all
    @@all
  end

  def initialize(artist_data = nil)
    #creates Artist object from the hash returned from the API
    if artist_data && artist_data.length > 0
      @name = artist_data[:name]
      @genres = artist_data[:genres]
      @top_tracks = artist_data[:top_tracks]
      @albums = artist_data[:albums]
      @related_artists = artist_data[:related_artists]
      @@all << self
    end
  end

end
```
The core functionality is to take in the name of an artist, and return an artist object with data (after calling the API). New objects will be initialized with a hash of data returned from the API.

How might we test this code? First we need to require all the files and provide a container for the test, in the `artist_spec.rb` file:
```
require "rspec"
require "spec_helper"
require "./lib/music_explorer"

RSpec.describe "MusicExplorer::Artist" do
end
```


**Testing the self.create_artist function**

Let's first test the` create_artist` class method, which is intended to return an artist object from a user query. Within the context of creating an artist, we want to make sure an artist is actually returned. So first we need to call the method with a sample artist query, "Bob Dylan." We are just setting up the data at this point, before writing tests:
```
context "when creating an artist" do
    artist_query = "Bob Dylan"
    MusicExplorer::Artist.create_artist(artist_query)
	end
```
Then we run the actual test, making sure that a new artist has been created (by searching the Artist.all array for an object with a name attribute equal to Bob Dylan):

```
it "creates a new artist" do
      expect(MusicExplorer::Artist.all.find { |artist| artist.name == "Bob Dylan" }).not_to be nil
    end
```
You can do any complicated query you like within the expect function, but then I just made sure it wasn't returning nil, which is what would happen if `find` failed. As it happens, all the tests pass.

**Testing self.lookup_artist**

By testing `self.create_artist`, we are implicitly testing `self.lookup_artist` and `self.all`. If those methods fail, the test will also fail. But we can and should test `self.lookup_artist` separately, as that will help isolate problems to that class method if it was the source of them:

```
  context "when looking up an artist" do
    artist_query = "Bob Dylan"
    artist_data = MusicExplorer::Artist.lookup_artist(artist_query)
    it "returns artist data in a hash" do
      expect(artist_data).not_to be nil
      expect(artist_data).to be_a(Hash)
    end
    it "returns the correct artist" do
      expect(artist_data[:name]).to eq("Bob Dylan")
    end
  end
```

Again, we set up the data and call the desired method, this time assigning the return value to a variable `artist_data`. Then we make sure that `artist_data` first of all exists, is not nil, and then, using the `be_a` method, has the correct data type of Hash. Finally, we use the method `eq`, testing for equality, to make sure that "Bob Dylan" is stored under the `:name` key of hash.

**Testing initialize**

The last method to test is `initialize`, the method that automatically gets called with `Artist.new`. This is a case where it is helpful to  create fake data in order to test the method in isolation:
```
artist_data = {name: "Beyonce", genres: [], top_tracks: [], albums: [], related_artists: []}
  artist = MusicExplorer::Artist.new(artist_data)
  context "when initialized with artist data" do

  end
```
In reality, the artist data hash would be more complicated, but this is enough to test whether an Artist instance gets initialized with all the correct values. (I created more specs in the API class, beyond the scope of this post). Then we test that all the attributes of the artist, and the class variable `Artist.all`, were initialized properly:
```
it "is an artist instance" do
      expect(artist).to be_an_instance_of(MusicExplorer::Artist)
    end
		
it "has a name" do
      expect(artist.name).to eq "Beyonce"
    end

    it "has genres" do
      expect(artist.genres).not_to be nil
      expect(artist.genres).to be_a(Array)
    end
#repeat for the other attributes that should be arrays

    it "is included in all artists" do
      expect(MusicExplorer::Artist.all).to include artist
			
    end
```
Most of this has been seen before, but two new methods are `to be an instance of`, helpful to make sure instances are being initialized correctly and `include` which tests if an element is present in a collection.

This is just the beginning of testing. There are many more methods and scenarios to consider when testing your code. For these reasons, it takes a while to become a proficient tester, and I am very much in the learning process.


**References**
[Music Explorer project](https://github.com/wkdewey/Music_Explorer/)




