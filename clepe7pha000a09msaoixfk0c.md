---
title: "Resolve HTTP 206 issues while serving static content with an Apache server"
datePublished: Wed Mar 01 2023 08:05:27 GMT+0000 (Coordinated Universal Time)
cuid: clepe7pha000a09msaoixfk0c
slug: resolve-http-206-issues-while-serving-static-content-with-an-apache-server
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1677192567078/1b57e76e-47c9-4346-a14b-af1d03faaab5.jpeg
tags: http, apache, range-requests, http-206

---

HTTP 206 is a response code obtained when a range request is issued to a server. According to [**developer.mozilla.org**](http://developer.mozilla.org)

> The HTTP `206 Partial Content` success status response code indicates that the request has succeeded and the body contains the requested ranges of data, as described in the [`Range`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Range) header of the request.

Range requests are great for loading small chunks of data. This is why they are perfect for video and audio assets: as you don't load all of their content at once, they can be played sooner.

Let's illustrate with a file called **lorem\_ipsum.txt** with the following content :

> Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

The file is hosted on a public server. We'll retrieve the first 13 bytes through a range request made with cURL :

```bash
curl --header "Range: bytes=0-12" http://example.com/lorem_ipsum.txt
Lorem ipsum d
```

The bytes index starts at 0. The `Range` headers help specify the desired bytes.

# HTTP 206 and the browser

Range requests can be managed on the client side by your Javascript. However, the browser [may add them on its own the **Range** header](https://philna.sh/blog/2018/10/23/service-workers-beware-safaris-range-request/).

Considering the browser's cache, it'll be used as a performant way to avoid excessive downloads of the website's static assets. Caching happens when the server returns a successful response, i.e when the server answers a **2XX**. This is why an HTTP 206 triggers file caching.

This behavior is problematic for assets that can't be used partially. Imagine a VueJS webapp divided into chunks through a webpack compilation. Those chunks are typically Javascript files loaded during the webapp's start. A partial Javascript file cut in the middle has few chances to work. Thus, it's likely to result in an app crash. It would be materialized by a blank page with an error in the browser's console :

> Uncaught syntaxError: Unexpected end of input (at chunk.4fkjds4677.js:34:657)

The browser cache creates a deadlock. Indeed, as long as the chunk's name remains the same, the browser won't try to download it again but will instead load it from its cache.

This is important here: range requests **must** be managed on the client side. There is no automatic download of the following bytes.

# Disable Range Responses for the Javascript files in an Apache Server

If you serve your content through Apache and don't think range requests are relevant for the Javascript content, a bit of configuration helps deactivate them. We consider here that you've defined a [VirtualHost](https://httpd.apache.org/docs/2.4/vhosts/examples.html).

In your vhost.conf, add the following :

```apache
<Files "*.js">
    MaxRanges none
</Files>
```

The [MaxRanges](https://httpd.apache.org/docs/2.4/mod/core.html#maxranges) directive is the key here :

> The MaxRanges directive limits the number of HTTP ranges the server is willing to return to the client. If more ranges than permitted are requested, the complete resource is returned instead

By setting the directive to none, the range headers are simply ignored. Thus the server will always return the full resource.

You need to restart your server for the modifications to be applied. You can use [apachectl](https://httpd.apache.org/docs/2.4/programs/apachectl.html) :

```bash
apachectl restart
```

Finally, to verify that everything's working, execute a cURL request that's supposed to get a byte range of a javascript file :

```bash
curl --header "Range: bytes=0-12" http://example.com/index.js
```

The server will answer an **HTTP 200** instead of an **HTTP 206**, with the full javascript content.

# Conclusion

I faced range requests issues with Chrome. Some headers were added when big chunks of data had to be downloaded with a degraded network. Overall, range requests are great for managing data efficiently. However, they tend to produce weird behaviors when they're not controlled.