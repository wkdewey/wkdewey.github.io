---
layout: post
title:      "Python file downloads and Google spreadsheets"
date:       2021-03-28 19:22:49 +0000
permalink:  python_file_downloads_and_google_spreadsheets
---


In this post I will describe how to download files from the Web. If you have been following earlier posts, I have been parsing XML documents and modifying them for a project. In my project there is now a requirement to update a spreadsheet of external data, rather than the XML file, in order to note discrepancies. I wanted to be able to dynamically use the most up-to-date version of the file as well, because the maintainer of the spreadsheet is actively making changes to the data.

**Downloading files from the internet**
A  Python module you can use to download files over HTTP is the urllib.request module. Import it into your Python script with `import urllib.request`. One method to download files with this module is `urlretrieve` (however, this is considered a legacy method, more suitable to Python 2 and subject to possible deprecation in Python 3). `urlretrieve` takes two arguments: the url you want to retrieve, and the path where you want to save the file. For instance, for the spreadsheet of data I want to use, the url is https://docs.google.com/spreadsheets/d/1BLhfPjUdtwt6JS_yZG5NXqcHA7e9IGFHaLsBH-y3igg/, and I want to save it to the absolute path of '/users/williamdewey/Development/code/84000-data-rdf/xml-parsing/data-export/ATII - Tentative template.xlsx'.

```
import urllib.request
spreadsheet_url = "https://docs.google.com/spreadsheets/d/1BLhfPjUdtwt6JS_yZG5NXqcHA7e9IGFHaLsBH-y3igg/"
urllib.request.urlretrieve(spreadsheet_url, '/users/williamdewey/Development/code/84000-data-rdf/xml-parsing/data-export/ATII - Tentative template.xlsx')
```

After doing this, I see the file with the specified file name in the desired location on my computer. However, it is actually in html format rather than excel format, so this will need to be corrected.

Another way to retrieve files is via the`requests` module, in combination with standard python syntax for opening files

```
import requests
spreadsheet_url = "https://docs.google.com/spreadsheets/d/1BLhfPjUdtwt6JS_yZG5NXqcHA7e9IGFHaLsBH-y3igg/"
r = requests.get(spreadsheet_url)

with open('/users/williamdewey/Development/code/84000-data-rdf/xml-parsing/data-export/ATII - Tentative template.xlsx', 'wb') as f:
    f.write(r.content)

print(r.status_code)
print(r.headers['content-type'])
print(r.encoding)

```
The `.get` method makes an HTTP GET request to retrieve the given URL, creatiing an object which is saved to the variable r. This object contains methods such as `.content` which returns the content of the file, as well as others that indicate the metadata related to the request, including headers, the encoding, and the status code returned by the server.

Having retrieved the content of the GET request, you can use python's `open` method to create a file object with the desired path ('wb' indicates that it is open to be written, in binary as opposed to text format), then write the content to the file.

**Getting spreadsheets from Google Docs"**
The links I've used above are suitable for illustration but they need to be modified to actually retrieve the spreadsheet in the right format. Note that simply retrieving the URL that you use to access the Google Doc in your browser doesn't work as expected. It downloads an HTML file (which is what you see in the browser), not the spreadsheet file itself. To get the spreadsheet file, you need to modify the url to use one of their datasource protocols, as follows https://docs.google.com/spreadsheets/d/KEY/export?format=csv&gid=SHEET_ID. KEY is the key that is present in the public URL
(These instructions apply to a publicly available document), while SHEET_ID is the index of the sheet.
Or else you can publish the sheet to the web (in the Google doc, File -> Publish to the web), choose the desired format, and then there will be a public URL to download from.

**References**
[Download files with Python](https://stackabuse.com/download-files-with-python/)
[Google Sheets API documentation](https://developers.google.com/sheets/api/reference/rest/v4/spreadsheets/get)
[Download link for Google Spreadsheets](https://stackoverflow.com/questions/33713084/download-link-for-google-spreadsheets-csv-export-with-multiple-sheets/50802790#50802790)

