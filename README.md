# service worker

## Offline strategy

<img src="http://www.zhimengzhe.com/d/file/bc/2jbzfswwv2f.gif" width="500px" height="350px">

- Appcache: using manifest
   - single page limit
   - refresh
- localstorage
	- size limit
- indexDB
- service worker


## what is the service worker

<img src="http://www.zhimengzhe.com/d/file/bc/nti1mbd1vij.gif" width="500px" height="300ox">

### characteristic

- proxy
- seperate from page and dom
- efficient background event handler in the browser
- cache API:https://developer.mozilla.org/en-US/docs/Web/API/CacheStorage
- promise
- make network requests using the Fetch API
- has an intentionally short lifetime


## life cycle

<img src="https://alp.alicdn.com/1477853360759.png" width="400px" height="500px">


## how to use


### register

- is browser support serviceWorker
- the cache only avaliable in the path of sw.js


```javascript
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js').then(function(registration) {
    // Registration was successful
    console.log('ServiceWorker registration successful with scope: ',    registration.scope);
  }).catch(function(err) {
    // registration failed :(
    console.log('ServiceWorker registration failed: ', err);
  });
}

```

### install

- open a cache
- add resources to cache
- forces the waiting service worker to become the active service worker


```javascript

var CACHE_NAME = 'my-site-cache-v1';
var urlsToCache = [
  '/',
  '/styles/main.css',
  '/script/main.js'
];

self.addEventListener('install', function(event) {
  // Perform install steps
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(function(cache) {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      }).then(self.skipWaiting())
  );
});

```

#### cache API

- The Cache interface provides a storage mechanism for Request / Response object pairs that are cached
- <a href="https://developer.mozilla.org/en-US/docs/Web/API/Cache" target="_blank">cache API</a>: match addAll map ...
- cache polyfill
- 



### fetch

```javascript
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response => {
      return response || fetch(event.request);
    })
  );
});

```

### active

```javascript
self.addEventListener('activate', event => {
  const currentCaches = [PRECACHE, RUNTIME];
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return cacheNames.filter(cacheName => !currentCaches.includes(cacheName));
    }).then(cachesToDelete => {
      return Promise.all(cachesToDelete.map(cacheToDelete => {
        return caches.delete(cacheToDelete);
      }));
    }).then(() => self.clients.claim())
  );
});
```

## examples


<a href="http://localhost/pwa/service-worker/service-worker-caching-example/" target="_blank">例子</a>

##   indepth： push API


<a href="http://localhost/pwa/service-worker/push-messaging-and-notifications/" target="_blank">例子</a>

<img src="https://gw.alicdn.com/tps/TB1NrMyNVXXXXc.XXXXXXXXXXXX-813-706.gif" width="600px" height="550px">
 
<img src="http://alp.alicdn.com/1477853281024.jpg">


- Check if browser supports service worker & push messaging
- Notification.requestPermission()
- register push & click event handle function
- when ready: navigator.serviceWorker.ready
- Get Server API key and Sender ID from GCM
- generate subscripionId: serviceWorkerRegistration.pushManager.subscribe
- wait to get push message

```javascript

self.addEventListener('push', function(event) {
  //do push thing
});

self.addEventListener('notificationclick', function(event) {
   // handle notification popup  
});


```

## indepth: background-sync


<a href="https://github.com/jakearchibald/emojoy/blob/master/js/sw/index.js" target="_blank">https://jakearchibald-gcm.appspot.com/</a>

<a href="https://github.com/jakearchibald/emojoy/blob/master/js/sw/index.js" target="_blank">代码</a>

```javascript
self.addEventListener('sync', event => {
  if (event.tag == 'postOutbox') {
    event.waitUntil(postOutbox());
  }
});

```

## more

- only used in https protoca
- support: chrome 49+ android 5.x.x Opera Firefox IE Edge






