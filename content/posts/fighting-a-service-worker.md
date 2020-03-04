---
title: "Fighting a Service Worker"
date: 2018-12-08T08:16:01+05:30
draft: false
featuredImg: "/static/ravitejagv.me-issues-serviceworker.png"
tags: 
  - service-worker
  - Gatsby
---
[Gatsby](https://www.gatsbyjs.org/), right out of the box it enables the user to take advantage of [Service Workers](https://developers.google.com/web/fundamentals/primers/service-workers/) and some other new things to make the user experience snappier. While this has certain advantages, it is working against me right now. 

I just switched from **Gatsby** to **Hugo** , for various reasons. But since my earlier version of page has a service worker, it seems to block the loading of my latest site and is causing issues. While ideally, it should look like this 

![New shiny Hugo page](/~old/new-shiny-hugo-page.png)

**It looks like this**

![Old website with bunch of JS errors in console](/~old/old-hugo-website-with-errors.png)

### So, what's happening here ?

**Service worker**

> A service worker is a script that your browser runs in the background, separate from a web page. 

It could be used for various things that can be done without the need of a user interaction.
 
 - Push notifications
 - Background sync
 - Geo fencing. 
 - Proxy with a cache

It could be instructed to listen for fetch requests, Using [Cache](https://developer.mozilla.org/en-US/docs/Web/API/Cache) API - it can act as a proxy with cache.

This is the exact feature that's screwing me, when I am trying to do a refresh - the underlying service worker tries to fetch latest content from the website but since the endpoints don't work any more, they return a 404. I am not sure if de-registering the service worker is the ideal behaviour in that scenario, but its not happening now. 

### The solution

If we look at the service requests, we also see a **request to update the current service worker.**

![Update for service worker](/~old/service-worker-update-network-tab.png)

So, I mounted a static file at the root of the website which has code to degister the service workers for the current domain.

```javascript
navigator.serviceWorker.getRegistrations().then(function(registrations) {
    for(let registration of registrations) {
     registration.unregister();
}})
```
it works, once i reload the page with this new **sw.js** in place, I saw that the service worker was removed. And I can see the new shiny Hugo blog 🍰 

**References**

- https://developers.google.com/web/fundamentals/primers/service-workers/
- https://www.kurzor.co.uk/blog/37-offline-in-browser-part-2
- https://developer.mozilla.org/en-US/docs/Web/API/Cache
- https://github.com/w3c/ServiceWorker/issues/204


