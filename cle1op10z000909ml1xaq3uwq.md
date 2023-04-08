---
title: "Send a multipart/related request with cURL"
datePublished: Sun Feb 12 2023 17:52:23 GMT+0000 (Coordinated Universal Time)
cuid: cle1op10z000909ml1xaq3uwq
slug: send-a-multipartrelated-request-with-curl
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/6AL5ong0mFQ/upload/9982b30dd8ed1a8074597c5c21298902.jpeg
tags: curl, http, multipart, multipartrelated

---

Sometimes you need to send a request with a body made of both JSON data and files. Here's a quick guide for sending complex body content with an HTTP client :-)

# Standard POST requests

An HTTP POST request is usually made of headers and a body. According to [developer.mozilla.org](https://developer.mozilla.org/) :

> The `Content-Type` representation header is used to indicate the original [media type](https://developer.mozilla.org/en-US/docs/Glossary/MIME_type) of the resource (prior to any content encoding applied for sending).

For instance, a typical POST request with a JSON body should have an ***application/json*** Content-Type. Let’s dive into a raw HTTP request :

```plaintext
POST /foo HTTP/1.1
Host: example.com
Content-Length: 14
Content-Type: application/json

{"foo": "bar"}
```

Another widely used Content-Type is ***multipart/form-data****.* [Here’s another sample of a raw HTTP request](https://everything.curl.dev/http/multipart) :

```plaintext
POST /submit.cgi HTTP/1.1
Host: example.com
Content-Length: 313
Content-Type: multipart/form-data; boundary=d74496d66958873e

--d74496d66958873e
Content-Disposition: form-data; name="person"

anonymous
--d74496d66958873e
Content-Disposition: form-data; name="secret"; filename="file.txt"
Content-Type: text/plain

contents of the file
--d74496d66958873e--
```

This special Content-Type indicates that several **subparts** compose the body. A **boundary** identifies each **subpart.** In the example above, the boundary is

```plaintext
d74496d66958873e   
```

The two dashes with the boundary indicate the **subpart's start**. Some headers like [Content-Disposition](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition) and Content-Type may come afterward. These headers are facultative. Keep in mind though that the contacted server often interprets them.

As you can see, each piece of content comes after its headers and a blank line. Two extra dashes follow a boundary to state the body’s end.

# Send standard POST requests with cURL

[cURL](https://curl.se/) is a widely used utility to send HTTP requests with the command line. Thus, it is convenient to use in a CI/CD pipeline, or more generally to write scripts.

As ***application/json*** and ***multipart/form-data*** requests are standards, sending them with cURL is straightforward. Let’s see how to send our previous requests using cURL :

**Request 1, application/json :**

```bash
curl -X POST http://example.com/foo \
-H "Content-Type: application/json" \
-d '{"foo": "bar"}'
```

cURL calculates by itself the request’s **Content-Size** and adds the header automatically.

**Request 2, multipart/form-data :**

```bash
curl -X POST http://example.com/submit.cgi \
-F person=anonymous \
-F secret=@file.txt
```

Here cURL calculates the **Content-Size** but also generates a random boundary, specifies the start and end of each **subpart,** and transfers the content of our file to a **subpart** (with the “@” which precedes “file.txt”).

Dead simple!

# Nonstandard POST requests

Some APIs rely on endpoints with more exotic requests content. For instance, their content can be made of a JSON metadata **subpart** and one or several files :

```plaintext
POST /submit.cgi HTTP/1.1
Host: example.com
Content-Length: X
Content-Type: multipart/related; boundary=d74496d66958873e

--d74496d66958873e
Content-Type: application/json

{"foo": "bar"}
--d74496d66958873e
Content-Disposition: attachment; name="secret"; filename="file.txt"
Content-Type: text/plain

contents of the file
--d74496d66958873e--
```

[According to its RFC](https://www.ietf.org/rfc/rfc2387.txt) :

> The Multipart/Related content-type provides a common mechanism for representing objects that are aggregates of related MIME body parts.

Here comes trouble: cURL doesn’t support easily ***multipart/related***, and other famous HTTP clients like [**HTTPie**](https://httpie.io/) or **Powershell** neither. For instance, you can’t use the “**\-F”** or “**\-d”** arguments to :

* Specify the number of blank lines between the **subparts**
    
* Specify the [Content-Transfer-Encoding](https://www.w3.org/Protocols/rfc1341/5_Content-Transfer-Encoding.html) header of a **subpart**. Useful in case of **Content-Type: application/octet-stream** subpart transferred with **base64** instead of **binary**.
    
* Overall, to finely customize your HTTP request
    

# Send raw body content with cURL

cURL can send a raw body with the **\--data-binary** option :

```bash
curl -X POST http://example.com/submit.cgi \
--data-binary "raw"
```

However, if your raw body is too long, you may run into an [argument list too long error](https://unix.stackexchange.com/questions/174350/curl-argument-list-too-long).

You may know in advance that your body will be long. For instance, a file upload will probably create a long body. Then you’ll better save the raw body first in a file, and load it in cURL with a “@” prefix :

```bash
curl -X POST http://example.com/submit.cgi \
--data-binary @my-raw-body-file
```

## Create a raw body

While creating your raw body, you should pay attention to :

* The line breaks, carriages, and blank lines
    
* The multipart boundary (if you're sending multipart content)
    
* Additional spaces
    

## Bash script sample

Here's a sample of a bash script I used to upload a file with its JSON metadata:

```bash
#!/bin/bash

fileBase64=$(base64 -i ./file.bin)
boundary="boundary"

body="--$boundary\r\n"
body="${body}Content-Type: application/json\r\n\r\n"
body="${body}{\"FileName\":\"filename\"}\r\n"
body="${body}\r\n--$boundary\r\n"
body="${body}Content-Type: application/octet-stream\r\n"
body="${body}Content-Transfer-Encoding: base64\r\n"
body="${body}Content-Disposition: attachment; filename=\"file.bin\"\r\n\r\n"
body="${body}$fileBase64"
body="${body}\r\n--$boundary--\r\n"

echo -e $body > body.http

curl -X POST "https://example.com/upload" \
-H "Content-Type: multipart/related; boundary=$boundary" \
--data-binary @body.http
```

As you can see, the script :

* first converts the file in base64
    
* then creates the body request and saves it to a file called **body.http**
    
* finally, it uploads the content with cURL
    

# Final thoughts

Most of the time, such ***metadata + files*** body will be uploaded with a **multipart/form-data** request. This is more standard and so more convenient to use. However, you may encounter exceptions, especially if you don't manage the APIs you're using.

I've used cURL and bash which are okay for me as long as the requests and bodies remain simple. However, the longer and the more complex they'll be, the harder it'll be to read, write, and maintain.