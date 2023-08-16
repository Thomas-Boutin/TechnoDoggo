---
title: "Apache mod_rewrite and a VueJS PWA"
datePublished: Wed Aug 16 2023 08:46:33 GMT+0000 (Coordinated Universal Time)
cuid: clldhno3m000309lcbf6y34fu
slug: apache-modrewrite-and-a-vuejs-pwa
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/DX5r6BNoWVE/upload/a770362fc141382cc09526f7fccf8d27.jpeg
tags: vuejs, apache, pwa, vue-router

---

Recently we came into a weird issue: random blank screens appeared to the users of our [PWA](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps). It happened after a webapp update.

We started to investigate with the developer tools. It showed an issue during the startup: some successful HTTP calls (HTTP 200) contained HTML while the webapp tried loading javascript resources.

We had configured the used Apache [vhost](https://httpd.apache.org/docs/2.2/en/vhosts/examples.html) with a [FallbackResource](https://httpd.apache.org/docs/trunk/en/mod/mod_dir.html#fallbackresource). This looked like so :

```apache
<VirtualHost *:8080>
    ...
    <Directory "/usr/local/apache2/htdocs">
        ...

        FallbackResource /index.html
    </Directory>

    DirectoryIndex index.html
    ...
</VirtualHost>
```

This configuration states that instead of returning an HTTP 404 when the resource is not found, it should instead return the ***index.html*** file content\*.\*

With this additional information, we could assess that the webapp tried to load non-existing content, and the Apache server returned the **index.html** content. This triggers another question: why is it trying to load missing resources?

The answer resides in the webapp type. We built a PWA with a service worker. Static files are cached, but [we used a network-first strategy](https://developer.chrome.com/docs/workbox/modules/workbox-strategies/#network-first-network-falling-back-to-cache). This means the service worker will first try to fetch the resource and fallback to the cache.

This can be tricky during an update: the content on the Apache server changes, but the service worker might try to fetch the old assets. But because of the **FallbackResource** configuration, Apache answers with the **index.html** content. The service worker tries to load the file as if it were a javascript resource. This fails and provokes blank pages.

## Resolution

### Option 1: Remove the FallbackResource directive

We thought first about removing the **FallbackResource** directive. However, it comes at a high cost. We use that redirection because we implement a VueJS PWA with [Vue Router](https://router.vuejs.org/). It's needed to handle 404 scenarios when a user refreshes a route that Vue Router manages. Take a look a the schema below :

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692185588604/98b95a62-e01a-43a5-ac7e-1278dbc52274.png align="center")

This sums up why we should configure a **FallbackResource** directive. By the way, it's specified [in the Vue Router documentation](https://router.vuejs.org/guide/essentials/history-mode.html#Apache).

The only available alternative is to use a different history mode: [Hash Mode](https://router.vuejs.org/guide/essentials/history-mode.html#Hash-Mode). This prefixes every route with a hashtag. Upon reloading, this prevents the browser from asking the Apache server for the given route. However, it makes the route less accessible and degrades the SEO.

### Option 2: Disable redirection for static assets

We chose this option. We decided to turn off the fallback to **index.html** for the static assets. The challenge was to keep the previous behavior for every other asset or route.

This is where [mod\_rewrite](https://httpd.apache.org/docs/2.4/fr/mod/mod_rewrite.html) comes in. This is an Apache module that helps rewrite incoming URLs using regex. Our resulting configuration looked like this :

```apache
<VirtualHost *:8080>
    ...

    <Directory "/usr/local/apache2/htdocs">
        ..
        # Enables the rewrite engine
        RewriteEngine On

        # Redirect requests for static assets to their respective locations
        RewriteRule \.(js|png|jpg|jpeg|svg|gif|css)$ - [L]

        # Redirect all other requests to index.html
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule ^ index.html [L]
    </Directory>

    DirectoryIndex index.html

    ...
</VirtualHost>
```

Here :

* The rewrite engine has to be enabled before we can write any directive.
    
* A **RewriteCond** directive defines a condition that has to be satisfied to trigger the **RewriteRule** that follow
    
* A **RewriteRule** rewrites the URL matched with a regex
    

If we take a look, for instance, at the second rule block :

```apache
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.html [L]
```

* `RewriteCond %{REQUEST_FILENAME} !-f` satisfies if the URL doesn't target a file that exists
    
* `RewriteCond %{REQUEST_FILENAME} !-d` satisfies if the URL doesn't target a directory that exists
    
* `RewriteRule ^ index.html [L]` rewrites the URL to **index.html**
    
* `[L]` is a flag that breaks the sequential evaluation of the rules
    

# Conclusion

Apache and PWA configurations are not easy to tackle. The **mod\_rewrite** module either because it uses many flags and different configurations.

To simplify and accelerate the test of the configuration, we used the [htaccess tester](https://htaccess.madewithlove.com/) website. This saved tens of minutes as we could test the configuration online instead of waiting for the Apache to deploy.

I'd recommend being very cautious about how you decide to configure and implement your service worker. This is a powerful tool, but you can experience harmful side effects fast.