---
layout: post
title:      "WordPress as a Content Management System and the web developer"
date:       2021-04-05 20:34:51 +0000
permalink:  wordpress_as_a_content_management_system_and_the_web_developer
---


Web developers at many organizations use Content Management Systems (CMS), and expertise in CMS is a common job requirement for web developer position. For that reason, it is important to understand the role of a CMS in web development. A very commonly used CMS is WordPress, best known as a blogging platform, but with many more features to manage websites. In this post, I will discuss what CMS's are in general, and in particular how WordPress includes features that would be relevant to a web developer who is already skilled at web programming langauges.

**What is a CMS?**
A content management system provides tools for authoring, collaboration and administration. It has tools for creating websites, typically designed so that no coding knowledge is necessary (WYSIWYG tools, "what you see is what you get", much like an application like Word). It provides ways for multple users to collaborate on documents and track the changes over time, with a kind of version control system. It also provides top-level tools to manage the website and its files, see analytics, determine who can modify or visit the website, and other administrative tasks.

Publishing the pages is often done via templates which provide a common theme to all content (this is a basic feature of WordPress). Then you can easily modify the content without worrying about presentational details. You can also management your documents over a lifecycle of creation, revision, publication, and deletion. Plugins extend the features of a CMS in a number of ways, including integration with other web development tools. This is one of the most powerful features of WordPress.

**Wordpress basic features**

WordPress's basic editor allows you to add pages, posts, and media.  Pages are used for more or less static content, whereas posts are used for content that you want to update frequently (like a blog). The default editor is the Block editor, which uses a WYSIWYG format, but you can also use Markdown syntax if you are more comfortable with that. Pages can be created with a template or you can just start from a blank page. WordPress provides page settings to control the visibility of pages, publish them, add categories and tags and so forth. The Block editor, as the name suggests allows you to edit content in blocks. Pressing the plus + button, at the top or whever you want a block, creates a new block. Blocks come in the formats of paragraph, columns, heading, image, and cover (for text within an image). Blocks have their own settings, generally for formatting and color. When you are ready, you can make the page live by hitting the publish option.Posts are not much different, but will be published at a URL, and will appear in order on your blog page

WordPress also contains a Customizer to change the theme, menus, and widgets. The customizer allows you to first of all set a home page (which can either consist of your posts, or a static page). It also allows you to create a menu in the order you want, including options to create submenus. When creating the menu, there is the option to create Category pages to see the blog posts that are in a given category. From the Customizer you can also customize the visual theme of your website. Widgets include other parts of your layout, including the sidebar and footer. There is also an option to include social media links from the Social Links menu.

**What is the purpose of a CMS for a skilled web developer**
The most obvious thing you can do on WordPress is to author web pages using their applications, which do not require any knowledge of coding. Right now I am using WordPress as a simple blogging tool, taking advantage of their default themes, authoring tools, and storage capabilities. But if you are a skilled web developer, you already know how to use HTML, CSS, and JavaScript, so why would you need a CMS like WordPress for a sophisticated website? Or rather, what does it offer when combined with the technical skills you already know? (This question comes to mind whenever I see WordPress, Drupal or a similar system listed as a requirement on job postings, alongside "hard" language requirements).

One useful feature of a CMS is that is provides a central point to manage a website, including tasks that might be difficult to do by hand on your own server. WordPress provides options to host your website on their server and use their online administration tools, or to download WordPress's own software and use it to manage content hosted on your own server. If you are managing multiple sites, you can set a common theme and policies from one point of administration. WordPress can be used for free but the free version is only good for a simple blog. There are a number of tiers of paid services offered by WordPress, ranging from a personal site to a large enterprise site. The higher tiers allow for more customization via plugins, and also allow you to integrate code with your site. By default, scripting languages like JavaScript are not allowed, but with plugins you can incorporate code in various languages into your site, integrate WordPress with other developer tools and extend the functionality of WordPress in other ways.

**WordPress REST API**

WordPress provides a REST API allowing WordPress sites to be manipulated with developer-friendly methods. As with any RESTful API, you can create, view, edit, and delete content via the API. In order to modify anything on a site, you need an authentication token, for which you need a WordPress account and a [client application](https://developer.wordpress.com/apps/). If all you want to do is retrieve content, you can just send a request to to an endpoint, for instance https://public-api.wordpress.com/rest/v1.1/sites/en.blog.wordpress.com/posts/?number=2&pretty=true returns a JSON representation of the blog post and associated metadata. But for anything that uses authentication, you need to use the OAuth2 authentication protocol, having the client application request a token on your behalf. The endpoints can do things like get a list of posts on a site (GET /sites/$site/posts), or a list of posts with a tag (GET /sites/$site/posts). They can also view top posts, get recommendations, and create a new post. More complex applications can be built on top of the REST API. WordPress gives the example of [Grasshoper](https://automattic.github.io/grasshopper/), a JavaScript application that visualizes statistics for WordPress sites, as well as the [Sulfur](http://https://developer.wordpress.com/2014/05/20/meet-sulfur/) app that allows you to upload, view, and delete images.

**References**
[Wikipedia: Web content management system](https://en.wikipedia.org/wiki/Web_content_management_system)
[Wikipedia: WordPress](https://en.wikipedia.org/wiki/WordPress)
[WordPress documentation](https://wordpress.com/support/)
[Introduction to the WordPress API](https://developer.wordpress.com/docs/api/getting-started/)


