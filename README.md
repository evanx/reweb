# reweb

Redis-based caching proxy for web pages.

<img src="https://raw.githubusercontent.com/evanx/reweb/master/docs/readme/main2.png"/>

## Use case

We require a local proxy to cache requests to Google Maps API into Redis.

## Usage

## Installation

### Docker

We can build and run via Docker:
```
docker build -t reweb https://github.com/evanx/reweb.git
```
See https://github.com/evanx/reweb/blob/master/Dockerfile
```
FROM mhart/alpine-node:latest
ADD package.json .
RUN npm install --silent
ADD lib lib
ENV NODE_ENV production
CMD ["node", "lib/index.js"]
```

We might simply run with `--network=host` i.e. using our host's Redis:
```
docker run --network=host --restart unless-stopped -d \
  -e httpPort=8851 \
  reweb
```


### git clone

Alternatively you can `git clone` etc:
```
git clone https://github.com/evanx/reweb.git
cd reweb
npm install
apiKey=$MAPS_API_KEY npm start
```

## Redis keys

We scan keys:
```
redis-cli --scan --pattern 'cache-reweb:*:json'
```
where we find keys e.g.
```
cache-reweb:64bdaff72bfc67deb55326022371ffef3ace9c7b
```
where keys are named using the SHA of the request path and query.

Check the TTL:
```
redis-cli ttl cache-reweb:64bdaff72bfc67deb55326022371ffef3ace9c7b
```
```
(integer) 1814352
```

## Config spec

See `lib/spec.js` https://github.com/evanx/reweb/blob/master/lib/spec.js
```javascript
```

## Implementation

See `lib/main.js` https://github.com/evanx/reweb/blob/master/lib/main.js
```javascript
```

### Analytics

```javascript
```
where for Mobile browsers we format the metrics in HTML. In our desktop browser, we typically have JSON formatter extension installed, and so can view JSON responses. But that is not the case on mobile, and perhaps we want to manually monitor the metrics on our mobile phone.

Incidently, we use a related module for basic HTML formatting: https://github.com/evanx/render-html-rpf

### Appication archetype

Incidently `lib/index.js` uses the `redis-koa-app` application archetype.
```
require('redis-koa-app')(
    require('../package'),
    require('./spec'),
    async deps => Object.assign(global, deps),
    () => require('./main')
).catch(console.error);
```
where we extract the `config` from `process.env` according to the `spec` and invoke our `main` function.

See https://github.com/evanx/redis-koa-app.

This provides lifecycle boilerplate to reuse across similar applications.

<hr>
https://twitter.com/@evanxsummers
