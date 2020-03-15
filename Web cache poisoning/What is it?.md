# Web cache poisoning

Web cache poisoning involves putting a payload into the cache of a user. For example, when a user's browser then retrieves a URL from the cache, the URL is poisoned and so will execute a payload.

In order to exploit this vulnerability, the attacker must find the name of (let’s say a js file) that the web app imports from a specific link. To find a JS file that is leaked, the attacker can send a request to a URL with a cache buster *(a cache buster is a part of the URL that is unique. For example, adding a random parameter such as cb=123.)* Upon observing the response, a JS file that is imported by the web page may be found.

Let's say, for example, that the JS file located at */js/SecurePayloads/website.js* is leaked. Now the attacker knows the path to the legitimate JS file. This file is then stored in the cache by the user’s web browser.

**So how do we then change this js file to our own?**

Well, the short answer is that we don't. (A point to note at this point is that in the source code where the js file is leaked, it must be have a hostname appended at the start. In addition to this, the host name must be one that can be modified (the following section explains how to do so)).
Instead we trick the browser into accessing a JavaScript file located at the same path, but on our server. To do this, we add in a header in the request called **X-Forwarded-Host**. This header is used to identify the original host of the request. What we will do is add this header into the request, and we will do so in the following format:

X-Forwarded-Host: Our-Own-Server/js/SecurePayloads/website.js

See what we did there? We kept the JS file that is imported the same, but we manipulated the request into importing one on our server instead of the legitimate one. Now what will happen is we will keep running this request until it is stored in the cache (Burp repeater comes in handy for doing this). How will we know when it is stored in the cache? Well the response header will include *X-Cache: hit*

Now, when the user loads the webpage it will come from their cache. Subsequently this means that the attacker’s payload will be run (so long as it remains in the cache)
