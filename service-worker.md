# Create a simple PWA

## Progressive Web App

Functionality includes working offline, push notification, and device hardware access, enabling creating user experiences similar to native applications on desktop and mobile devices.

> **Note:** This will create the bare bones required to sample a PWA from simple files.

## Create a new Node Project

Create a new folder for your project.
```javascript
npm init -y
```
Add new files: **index.html** and **service-worker.js** (empty for now)
**index.html**
```
<!DOCTYPE  html>
<html  lang="en">
<head>
<meta  name="viewport"  content="width=device-width, minimum-scale=1.0, initial-scale=1.0, user-scalable=yes">
<meta  charset="utf-8">
<title>Simple Service Worker</title>
</head>
<body>
<header>
<h1>Service Worker</h1>
</header>

</body>
</html>
```

# Start the site

In the project directory run 
```
npx http-server -o
```
This will start a local server to test this on.
> -o Will just open a browser page automatically to view the site

## Register a service worker

Update **Index.html** to include the following script just before the end of the body
```javascript
<script>
// register the worker
if ('serviceWorker' in  navigator) {
	window.addEventListener('load', function() {
	navigator.serviceWorker
			 .register('service-worker.js')
			 .then(reg  => {
	console.log('Service worker registered!, reg);
}).catch(err  => {
	console.log('Service worker registration failed: ', err);
	});
});
}
</script>
```

If you refresh the page and now hit F12 in Chrome and go to > Application > service workers. You should see a registration for the service-worker.js file, activated and running. Even an empty JS file can be registered successfully.

## React to events

We can now update the service worker - add the following to see the events trigger for fetch, active and install.

```javascript
const cacheName = 'cache-v1';
const precacheResources = [
  '/',
  'index.html'
];

self.addEventListener('install', event => {
  console.log('Service worker install event!');
  event.waitUntil(
    caches.open(cacheName)
      .then(cache => {
        return cache.addAll(precacheResources);
      })
  );
});

self.addEventListener('activate', event => {
  console.log('Service worker activate event!');
});

self.addEventListener('fetch', event => {
  console.log('Fetch intercepted for:', event.request.url);
  event.respondWith(caches.match(event.request)
    .then(cachedResponse => {
        if (cachedResponse) {
          return cachedResponse;
        }
        return fetch(event.request);
      })
    );
});
```
Now return to the browser and refresh the site. Check the console to see that the service worker:

-   registered
-   installed
-   activated

>**Note:**  If you already registered the service worker previously, or are having trouble getting all the events to fire, unregister any service workers and refresh the page. If that fails, close all instances of the app and reopen it.

Next, terminate the local development server in your command line by running  `Ctrl + c`. Refresh the site again and observe that it loads even though the server is offline!

## Add The Manifest File
The `manifest.json` file tells the browser how to style and format some of the progressive aspects your app, such as the browser chrome, home screen icon, and splash screen. It can also be used to configure your web app to open in `standalone` mode, like a native app does (in other words, outside of the browser).
***Manifest.json***
```json
{
"name": "Service Worker",
"short_name": "Service Worker",
"lang": "en-UK",
"start_url": "/index.html",
"display": "standalone",
"theme_color": "#FF9800",
"background_color": "#FF9800",
"icons": [ ]
}
```
Then add these new lines to the `index.html`
```html
<link  rel="manifest"  href="manifest.json">
<meta  name="theme-color"  content="#FF9800">
```
> Note: Reload the site and run the audit, it will now have the basics of a PWA score.


