---
layout: post
title:      "Using Python to parse and manipulate XML"
date:       2021-03-16 15:14:46 +0000
permalink:  using_python_to_parse_and_manipulate_xml
---


XML (extensible markup language) is an extension of HTML, with the ability to create custom tags. It is often used to store data. XML is not something that automatically renders in a browser. In order to read and do things with the data, change the data, and otherwise manipulate it, you need to be able to process it using scripts. Specialized languages exist for doing this with XML, but it also can be done with more general-purpose languages such as Python or Ruby.

I am using the following movies.xml file for the tutorial (taken from Toward Data Science), it was deliberately created with errors:

```
<?xml version="1.0"?>
<collection>
  <genre category="Action">
    <decade years="1980s">
      <movie favorite="True" title="Indiana Jones: The raiders of the lost Ark">
        <format multiple="No">DVD</format>
        <year>1981</year>
        <rating>PG</rating>
        <description>
        'Archaeologist and adventurer Indiana Jones
        is hired by the U.S. government to find the Ark of the Covenant before the Nazis.'
        </description>
      </movie>
      <movie favorite="True" title="THE KARATE KID">
        <format multiple="Yes">DVD,Online</format>
        <year>1984</year>
        <rating>PG</rating>
        <description>None provided.</description>
      </movie>
      <movie favorite="False" title="Back 2 the Future">
        <format multiple="False">Blu-ray</format>
        <year>1985</year>
        <rating>PG</rating>
        <description>Marty McFly</description>
      </movie>
    </decade>
    <decade years="1990s">
      <movie favorite="False" title="X-Men">
        <format multiple="Yes">dvd, digital</format>
        <year>2000</year>
        <rating>PG-13</rating>
        <description>Two mutants come to a private academy for their kind whose resident superhero team must oppose a terrorist organization with similar powers.</description>
      </movie>
      <movie favorite="True" title="Batman Returns">
        <format multiple="No">VHS</format>
        <year>1992</year>
        <rating>PG13</rating>
        <description>NA.</description>
      </movie>
      <movie favorite="False" title="Reservoir Dogs">
        <format multiple="No">Online</format>
        <year>1992</year>
        <rating>R</rating>
        <description>WhAtEvER I Want!!!?!</description>
      </movie>
    </decade>
  </genre>
      <genre category="Thriller">
        <decade years="1970s">
            <movie favorite="False" title="ALIEN">
                <format multiple="Yes">DVD</format>
                <year>1979</year>
                <rating>R</rating>
                <description>"""""""""</description>
            </movie>
        </decade>
        <decade years="1980s">
            <movie favorite="True" title="Ferris Bueller's Day Off">
                <format multiple="No">DVD</format>
                <year>1986</year>
                <rating>PG13</rating>
                <description>Funny movie on funny guy </description>
            </movie>
            <movie favorite="FALSE" title="American Psycho">
                <format multiple="No">blue-ray</format>
                <year>2000</year>
                <rating>Unrated</rating>
                <description>psychopathic Bateman</description>
            </movie>
        </decade>
    </genre>
</collection>
```

# Parsing XML with ElementTree

Python can read and manipulate XML files, taking advantage of the format's simple tree with the module ElementTree. It can be imported with this statment:

```
import xml.etree.ElementTree as ET
```

First, read the file with ElementTree's parse() command and get the root with getroot():

```
tree = ET.parse('movies.xml')
root = tree.getroot()
```

Then you can iterate through the children with a simple for loop, printing the tag name and attribute keys and values of each item.

```
for child in root:
    print(child.tag, child.attrib)
genre {'category': 'Action'}
genre {'category': 'Thriller'}
```

This shows that the children of collection are <genre> tags, with the categories designated as Action and Thriller.

If you want to know all the elements in the tree, you can use root.iter() as follows:

```
elements = [elem.tag for elem in root.iter()]
print(elements)
```

It prints out

```
['collection', 'genre', 'decade', 'movie', 'format', 'year', 'rating', 'description', 'movie', 'format', 'year', 'rating', 'description', 'movie', 'format', 'year', 'rating', 'description', 'decade', 'movie', 'format', 'year', 'rating', 'description', 'movie', 'format', 'year', 'rating', 'description', 'movie', 'format', 'year', 'rating', 'description', 'genre', 'decade', 'movie', 'format', 'year', 'rating', 'description', 'decade', 'movie', 'format', 'year', 'rating', 'description', 'movie', 'format', 'year', 'rating', 'description']
```

You can use root.iter() with an argument of an element to find subelements.

```
for movie in root.iter('movie'):
    print(movie.attrib)

{'favorite': 'True', 'title': 'Indiana Jones: The raiders of the lost Ark'}
{'favorite': 'True', 'title': 'THE KARATE KID'}
{'favorite': 'False', 'title': 'Back 2 the Future'}
{'favorite': 'False', 'title': 'X-Men'}
{'favorite': 'True', 'title': 'Batman Returns'}
{'favorite': 'False', 'title': 'Reservoir Dogs'}
{'favorite': 'False', 'title': 'ALIEN'}
{'favorite': 'True', 'title': "Ferris Bueller's Day Off"}
{'favorite': 'FALSE', 'title': 'American Psycho'}
```

You can also print out the text content of XML elements, rather than their attributes with the .text method:

```
for description in root.iter('description'):
print(description.text)
                'Archaeologist and adventurer Indiana Jones
                is hired by the U.S. government to find the Ark of the Covenant before the Nazis.'

None provided.
Marty McFly
Two mutants come to a private academy for their kind whose resident superhero team must oppose a terrorist organization with similar powers.
NA.
WhAtEvER I Want!!!?!
"""""""""
Funny movie on funny guy
psychopathic Bateman
```

Using XPath queries, you can search the xml in a more detailed way. One way to do this is with the .findall() function. For instance, you can find all movies that can out in 1985, looking under the "year" element.

```
for movie in root.findall("./genre/decade/movie/[year='1985']"):
    print(movie.attrib)

    {'favorite': 'False', 'title': 'Back 2 the Future'}

```

# Modifying XML

What if we wanted to change an XML element? For instance, we may want "Back 2 the Future" to be instead "Back to the Future". First you have to find the element with a .find query; this will return an element itself. then you would modify the title attribute

```
b2tf = root.find("./genre/decade/movie[@title='Back 2 the Future']")
b2tf.attrib["title"] = "Back to the Future"
print(b2tf.attrib)

{'favorite': 'False', 'title': 'Back to the Future'}
```

This can also be combined with regex queries

Also, you may find that elements are in the wrong place. Printing out all movies by decade reveals that some are out of place, specifically those that came out in 2000:

```
for decade in root.findall("./genre/decade"):
    print(decade.attrib)
    for year in decade.findall("./movie/year"):
        print(year.text)

{'years': '1980s'}
1981
1984
1985
{'years': '1990s'}
2000
1992
1992
{'years': '1970s'}
1979
{'years': '1980s'}
1986
2000

{'favorite': 'False', 'title': 'X-Men'}
{'favorite': 'FALSE', 'title': 'American Psycho'}
```

To move X-Men to the correct place, first you find the 2000 movies:

```
for movie in root.findall("./genre/decade/movie/[year='2000']"):
    print(movie.attrib)
```

Then add a 2000 decade tag, which currently doesn't exist in the DOM tree. The .subElement tag can add a new tag under a current element, then fill in its attributes with .attrib.

```
action = root.find("./genre[@category='Action']")
new_dec = ET.SubElement(action, 'decade')
new_dec.attrib["years"] = '2000s'
``
```

After creating the proper decade tag, you can move the movie into the correct place with .append(), and .remove() it from the wrong place

```
xmen = root.find("./genre/decade/movie[@title='X-Men']")
dec2000s = root.find("./genre[@category='Action']/decade[@years='2000s']")
dec2000s.append(xmen)
dec1990s = root.find("./genre[@category='Action']/decade[@years='1990s']")
dec1990s.remove(xmen)
```

Finally, save your changes back to the XML file

```
tree.write("movies.xml")
```

Congrats, you have now successfully parsed and changed an XML file with Python!

# Handling namespaces

A complicating factor that did not come up in this tutorial is namespaces. These are generally defined in the root tag using xmlns attributes. For example, a document I am parsing had the default namespace xmlns="http://read.84000.co/ns/1.0" for the rdf prefix xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#" and for the owl prefix "http://www.w3.org/2002/07/owl#". With namespaces, the parser does not just see the tag, but expands it to "{uri}tag". I was looking for the element <bibl></bibl> and initially couldn't find it because it was actually at {http://read.84000.co/ns/1.0}bibl.

```
#this didn't work
for bibl in root.iter("bibl"):
    toh_num = bibl.attrib["key"]
#this works
for bibl in root.iter("{http://read.84000.co/ns/1.0}bibl"):
    toh_num = bibl.attrib["key"]
```

Here is another example from the Python docs:

```
<?xml version="1.0"?>
<actors xmlns:fictional="http://characters.example.com"
        xmlns="http://people.example.com">
    <actor>
        <name>John Cleese</name>
        <fictional:character>Lancelot</fictional:character>
        <fictional:character>Archie Leach</fictional:character>
    </actor>
    <actor>
        <name>Eric Idle</name>
        <fictional:character>Sir Robin</fictional:character>
        <fictional:character>Gunther</fictional:character>
        <fictional:character>Commander Clement</fictional:character>
    </actor>
</actors>
```

It is possible to make find() or findall() queries by manually adding the URI:

```
for actor in root.findall('{http://people.example.com}actor'):
    name = actor.find('{http://people.example.com}name')
    print(name.text)
    for char in actor.findall('{http://characters.example.com}character'):
        print(' |-->', char.text)

John Cleese
 |--> Lancelot
 |--> Archie Leach
Eric Idle
 |--> Sir Robin
 |--> Gunther
 |--> Commander Clement
```

But this is too much of a brute force solution. You can also create a dictionary of your oen with simpler prefixes and do queries with the dictionary:

```
ns = {'real_person': 'http://people.example.com',
      'role': 'http://characters.example.com'}

for actor in root.findall('real_person:actor', ns):
    name = actor.find('real_person:name', ns)
    print(name.text)
    for char in actor.findall('role:character', ns):
        print(' |-->', char.text)
```

# references

[Processing XML in Python](https://towardsdatascience.com/processing-xml-in-python-elementtree-c8992941efd2)
[Parsing XML with Namespaces](https://docs.python.org/3/library/xml.etree.elementtree.html#parsing-xml-with-namespaces)

