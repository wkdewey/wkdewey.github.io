---
layout: post
title:      "Linked Open Data and RDF"
date:       2021-02-23 20:34:04 -0500
permalink:  linked_open_data_and_rdf
---


Linked Open Data and RDF are the basis of many digital publication projects. Libraries, archives, and other public knowledge institutions seek to publish their data in a way that is easily accessible, machine readable, and relatable to other data sets.

**Linked Open Data**
Linked Open Data has been promoted by Tim Berners-Lee, the inventor of the World Wide Web, as part of his initiative to create a Semantic Web. The Semantic Web attempts to store information in a semantically meaningful way, one that is also machine readable. This is intended to overcome some of the limitations of the original World Wide Web just consisted of HTML pages and hyperlinks between them, without any semantic information, or information about the meaning of the data. (Relational databases similarly don't include semantic links between data; humans can understand what the data means but computers cannot. The question of what machines can "understand" is philosophically complex, but links between data points is thought to be an essential part of understanding, as a table of data is meaningless without additional contextual information). 

Linked Open Data is, as the name suggests, a combination of linked data and open data. Open data is publicly available data, generally free of charge, that can be reused and reproduced freely. This means that it should be in the public domain, or at least available under a non-restrictive license like some forms of Creative Commons. Linked data, as I described above, is machine readable, semantically informative, and linked to other data for context. It is often stored using HTML microdata, or RDF (which I will describe below)  Tim Berners-Lee has described a "five star data" model that defines Linked Open Data in its fullest form, each step gets you closer to the LOD model.
1. It is available on the Web under an open license and is freely accessible and reusable
2. It is structured (plain text or jpg files would not qualify, but something like JSON would)
3. It is in a non-proprietary format (like CSV, rather than excel)
4. It uses URI's, uniform resource identifiers, to point to the data
5. It links to other data to provide semantic context
The first three of these (especially 1 and 3) define open data, while 4 and 5 define linked data. Linking is done through URIs and data triples that consist of the original data, a relationship, and the data that is being pointed to. It is stored in a serialized format like RDF.

**Resource Description Format**
The World Wide Web Consortium describes the Resource Description Format as "a language for representing infromation about resources on the World Wide Web. It is used in such projects as the Dublin Core Metadata Initiative, and a common usage of RDF is metadata about web pages: their title, author, date, copyright, licensing. But it also can represent anything that exists, even if it is not a retrievable resource on the web, for instance a person, a piece of merchandise, or a literary work. These things can be identified by Uniform Resource Identifiers (URI). URIs can be read by applications and can be exchanged with other applications. (They should not be confused with URL's despite a similar format, and putting them in a web browser will not necessarily lead to anything). For instance, the individual Eric Miller could be identified with the URI http://www.w3.org/People/EM/contact#me. This identifier could in turn be connected to a kind of Person (http://www.w3.org/2000/10/swap/pim/contact#Person), The individual could also have properties such as mailbox (http://www.w3.org/2000/10/swap/pim/contact#mailbox) which would have the value of mailto:em@w3.org or full name(http://www.w3.org/2000/10/swap/pim/contact#fullName). These URIs together take the form of a graph. It could be represented in XML as 
```
<?xml version="1.0"?>
<rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
             xmlns:contact="http://www.w3.org/2000/10/swap/pim/contact#">

  <contact:Person rdf:about="http://www.w3.org/People/EM/contact#me">
    <contact:fullName>Eric Miller</contact:fullName>
    <contact:mailbox rdf:resource="mailto:em@w3.org"/>
    <contact:personalTitle>Dr.</contact:personalTitle> 
  </contact:Person>

</rdf:RDF>
```
RDF statements talk about things, their properties and values. The formal names of this "triple" are subject, predicate and object. My birthplace is Bloomington, Indiana; the subject is me, the predicate (property or characteristic) is birthplace, the object (value of property) is object. This language is designed to be machine-processable and to allow machines to communicate, and URIs are intended to be unique identifiers that won't be confused with something else.

**References**
[Difference between linked data and open data](https://opendata.stackexchange.com/questions/522/what-is-the-major-difference-between-open-data-and-linked-data)
[Five Star Data](https://5stardata.info/en/)
[Linked Data vs. Open Data vs. RDF Data](http://blog.soton.ac.uk/webteam/2011/07/17/linked-data-vs-open-data-vs-rdf-data/)
[Semantic data model](http://en.wikipedia.org/wiki/Semantic_data_model)
[RDF Primer](https://www.w3.org/TR/rdf-primer/)
