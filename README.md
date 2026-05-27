# Test-Data-management-
{
  "name": "TestData Manager",
  "short_name": "TestData",
  "description": "Offline tracker for tractor R&D test applications — planned vs actual run hours/cycles, failure history and dashboards.",
  "start_url": "./index.html",
  "scope": "./",
  "display": "standalone",
  "display_override": ["standalone", "minimal-ui"],
  "orientation": "portrait",
  "background_color": "#0a0e14",
  "theme_color": "#0a0e14",
  "categories": ["productivity", "business", "utilities"],
  "lang": "en",
  "dir": "ltr",
  "icons": [
    {
      "src": "icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "icon-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "icon-maskable-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "maskable"
    },
    {
      "src": "icon.svg",
      "sizes": "any",
      "type": "image/svg+xml",
      "purpose": "any"
    }
  ],
  "shortcuts": [
    {
      "name": "Log Run",
      "short_name": "Log Run",
      "description": "Quickly log cumulative run hours/cycles",
      "url": "./index.html",
      "icons": [{ "src": "icon-192.png", "sizes": "192x192" }]
    },
    {
      "name": "Log Failure",
      "short_name": "Failure",
      "description": "Record a failure event",
      "url": "./index.html",
      "icons": [{ "src": "icon-192.png", "sizes": "192x192" }]
    }
  ]
}

/* TestData Manager — Service Worker
 * Caches the app shell so it works fully offline once installed.
 * Bump CACHE_VERSION when you publish a new build to invalidate old caches. */

const CACHE_VERSION = 'tdm-v3';
const APP_SHELL = [
  './',
  './index.html',
  './manifest.json',
  './icon.svg',
  './icon-192.png',
  './icon-512.png',
  './icon-maskable-512.png'
];

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_VERSION)
      .then((cache) => cache.addAll(APP_SHELL).catch(() => {
        /* If any single resource fails, cache what we can individually */
        return Promise.all(APP_SHELL.map((url) =>
          fetch(url).then((res) => res.ok ? cache.put(url, res) : null).catch(() => null)
        ));
      }))
      .then(() => self.skipWaiting())
  );
});

self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then((keys) =>
      Promise.all(keys.filter((k) => k !== CACHE_VERSION).map((k) => caches.delete(k)))
    ).then(() => self.clients.claim())
  );
});

/* Cache-first for the app shell — guarantees offline reload */
self.addEventListener('fetch', (event) => {
  const req = event.request;
  if (req.method !== 'GET') return;
  event.respondWith(
    caches.match(req).then((cached) => {
      if (cached) return cached;
      return fetch(req).then((res) => {
        if (res && res.status === 200 && new URL(req.url).origin === self.location.origin) {
          const copy = res.clone();
          caches.open(CACHE_VERSION).then((cache) => cache.put(req, copy));
        }
        return res;
      }).catch(() => cached);
    })
  );
});
