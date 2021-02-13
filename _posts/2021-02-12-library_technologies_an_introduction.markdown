---
layout: post
title:      "Library technologies, an introduction"
date:       2021-02-13 01:18:35 +0000
permalink:  library_technologies_an_introduction
---


I have been looking for jobs at the intersection of technology and academia, and this has taken me to web development positions for academic libraries. Libraries use a number of technologies behind the hood of their websites, especially to store information on their collection. Some of these are unique to libraries or similar cultural institutions, others are more widespread but have particular

**Metadata and cataloging**
In the old days, if you wanted to look up a book in the library, you looked through the card catalog: a series of drawers in a cabinet, containing cards arranged by subject, with the locations of materials and bibliographical information. This was one of the first library services to be computerized I am old enough to remember that my elementary school had one, but the local public library and the university library where my mother worked had digitized card catalogs as long as I can remember, and my school soon followed. In order to store catalog information, libraries need formats storing metadata about their collection. There are many such formats but I will focus on MARC and METS as two representative formats.

*MARC*
MARC stands for MAchine-Readable Cataloging. It is one of the oldest formats, dating back to the 1970s. The idea behind MARC was to include all the information that could fit on a card catalog, in a standard format that a computer could read. It was originally designed for books, but has gradually been extended to cover all sorts of other media. The standard is maintained by the Library of Congress and there has been an increasing trend toward internationalization.  Each field and subfield have a key of a numeric code. The possible fields include Library of Congress or Dewey Decimal call numbers, source of acquisition, holding library, author, title, edition, place and date of publication, physical dimensions. Here is an excerpt of the MARC encoding for the children's book *Stinky Cheese Man* 
```
- 100 1- Scieszka, Jon.
- 245 14 The Stinky Cheese Man and other fairly stupid tales / $c by Jon Scieszka & Lane Smith.
- 260 -- New York, N.Y., U.S.A. : $b Viking, $c 1992.
- 300 -- 1 v. (unpaged) : $b col. ill. ; $c 28 cm.
- 500 -- Cover title.
- 505 0- Chicken licken -- Princess and the bowling ball -- Really ugly duckling -- Other frog prince -- Little Red Running Shorts -- Jack's bean problem -- Cinderumpelstiltskin -- Tortoise and the hair -- Stinky cheese man -- Boy who cried "Cow patty."
- 520 -- Madcap revisions of familiar fairy tales.
- 586 -- Caldecott Honor Book, 1993.
- 650 -0 Fairy tales $z United States.
- 650 -0 Children's stories, American.
- 650 -1 Fairy tales.
```

*METS*
As an older format, MARC has attempted to keep up with the times but it has proven inadequate for all uses in the digital age. Thus more detailed and digitally-focused metadata standards have been created, such as Metadata Encoding & Transmission Standard, which can encode a wide range of data for digital library applications. It uses the XML schema language to express shared vocabularies and allow machines to carry out rules made by people. XML schema is flexible so that the format can be adapted to many different types of applications and formats: geographical data, bibliographic data, PDFs, pictorial materials, images and videos, musical scores, museum objects a variety of internationalized formats. METS profiles are used in projects like digital libraries of ebooks (like the California Digital Library), museum databases, bibliographical databases, scholarly papers like the Mark Twain papers, and digital humanities archives containing materials ranging from ethnomusicology videos to musical theater and dance materials.

**Data formats and frameworks**
Today's libraries go far beyond being repositories for books, or even other printed material. They carry digital information in  a wide variety of formats, I will cover a two formats that have found special applications

*XML and XML Schema* XML is a broadly applicable format, in libraries it is most widely used for encoding metadata in a flexible format (see above about METS), and also storing detailed information about texts for which plain text or HTML would be inadequate. For instance, some Buddhist translation projects use XML to store the texts of the translations so that they can be annotated with glossaries, variant readings, and so forth. XML, Extensible Markup Language, is similar to HTML but as the name suggests can be extended, creating elements that serve any manner of purposes. An XML Schema defines the legal building blocks of a particular type of XML document. The schema is itself written in XML. It defines the elements and attributes that can appear, complex types with a particular number and order of child elements, data types for elements and attriburtes. The ability to create custom data types is especially powerful, as you can define the allowable content validate the correctness of documents, and convert between different types of documents. It is suitable for defining the schema of a database, and there are databases that use XML instead of SQL

*IIIF*
With digital technologies, libraries can give users access to information in the form of images. Institutions like museums, image repositories, and many companies also have a need for  technologies that deal with images. An early problem is that there were too many incompatible image formats and tools for storing, editing and viewing them. This is where IIIF ("Triple I F") comes int. The International Image Interoperability Framework is intended to "enable richer access to the world's images". It is designed to encourage compatibility and interoperability. IIF includes a number of common APIs and shared technologies for viewing and maniuplating images. The IIIF has the core APIs of image and presentation, the additional APIs for authentication and content search. These image API allows for interoprable delivery, including definitions of parameterized URIs for zooming in on images and creating thumbnails, and encoding information and metadata. IIIF extends into the realm of software, as the basis for image display tools running on a general web server, and image servers which are designed for deeper compliance with IIF standards.

**Search**
Libraries not only need to store data, they need to offer search engines to retrieve it, and what is known as a discovery layer, or an interface that allows users to interact with the search engine and browse information

*Solr*
Solr is described by its provider, the open-source Apache Software Foundation, as the "popular blazing-fast open source enterprise search platform". It is designed to be reliable, scalable, and fault toleratant. It provides features like distributed indexing, replication, load-balanced querying, automated failover and recovery, and many search and navigation features. In short, it is defined for searching applications that are high volume or involve full-text search of large amounts of text. Solr has a REST-like API in which the documents to be indexed can be stored. These documents It is built on top of Apache Lucene, a library that provides the building blocks of search and indexing. Solr can also take in additional plugins to improve its indexing capabilities.

*Elasticsearch*
Elasticsearch is another search engine built on top of the Lucene technology. It is also supposed to be scalable with large numbers of documents or data requests, and fault tolerant (with automatic failover). It can be downloaded and you can try it out on your own laptop. It comes along with a tool called Kibana which has developer tools and can visualize the data in your search index. Elasticsearch stores documents using JSON to create its index; for instance, in a collection of restaurant reviews, each review would be an object with keys including location, inspection date, and inspection scores. With ElasticSearch''s REST endpoints, you can POST or PUT this information into your index. Searches are also in JSON, specifying how you want to sort and filter your data

*Blacklight*
It is not enough to have a fast, powerful, and extensible search engine, you must also provide an interface for users to use it on the website. This is what is known in library parlance as a "discovery platform". One of the most common discovery platforms is Blacklight, which is built on top of the Solr index, and provides search and browsing functionality. In addition to output, it also has advanced search controls to transform user input into search queries. Blacklight includes plugins for library catalogs, website presentations, and geospatial data among other applications. It is used by academic customers like Harvard Digital Collections, Penn State ScholarSphere and library catalog, and nonacademic customers like the Rock and Roll Hall of Fame

**Publishing platforms**

Libraries often host content like exhibits or other scholarly projects, and seek to make web publishing accessible for faculty and students. This is one major goal of digital humanities programs hosted in libraries.

*Omeka*

Omeka is one such tool that allows users to publish web sites  with customized themes and links to all sorts of media. It comes in several versions. Omeka S is an institutional version, that allows multiple sites to share a single resource pool. It has connections to the Semantic Web and Linked Open Data. Omeka classic is more for individual scholars, or institutions putting a collection online, and can be published without knowledge of coding. There are plugins for maps, user input, tagging, blogging, and so forth. Omeka.net is a hosted service option for digital collections and exhibits, content can be hosted on Omeka's servers rather than on one's own site. You can upload items to it with structured metadata, and create interactive digital exhibits with media as well as text. Omeka Everywhere connects Omeka sites to phones and tablets. The development team was originally hosted by the Roy Rosenzweig Center of George Mason University, but now is self supporting and hosted  at the Corporation for Digital Scholarship.

**References**
[Introduction to MARC, Metadata, and RDF](http://www.librarian.net/talks/marcmetadata/)
[Metadata Encoding & Transmission Standard](https://www.loc.gov/standards/mets/)
[XML Schema](https://www.w3schools.com/xml/schema_intro.asp)
[XML Schema](https://www.tutorialspoint.com/xml/xml_schemas.htm)
[International Image Interoperability Framework](https://iiif.io/)
[Apache Solr](https://lucene.apache.org/solr/)
[Elasticsearch](https://www.elastic.co/elastic-stack?elektra=home&storm=stack)
[Blacklight](http://projectblacklight.org/)
[Omeka](https://omeka.org/)

