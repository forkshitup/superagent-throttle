# superagent-throttle

![nodei.co](https://nodei.co/npm/superagent-throttle.png?downloads=true&downloadRank=true&stars=true)

![npm](https://img.shields.io/npm/v/superagent-throttle.svg)

A plugin for [superagent](https://github.com/visionmedia/superagent)
that throttles requests. Useful for rate or concurrency limited APIs.

## Notice

I'm working on an es6 branch which will be merged into master, please take a
look at that before creating a PR against this master branch.

## Features

 * This doesn't just delay requests by an arbitrary number of ms, but
   intelligently manages requests so they're sent as soon as possible whilst
   staying beneath rate limits.
 * Can make serialised subqueues on the fly.
 * Follows [superagent](https://github.com/visionmedia/superagent)
   `.use(throttle.plugin())` architecture
 * Hackable
 * Can use multiple instances

## Install

```
  npm i --save superagent-throttle
```

## Basic Usage

    const request     = require('superagent')
    const Throttle    = require('superagent-throttle')

    let throttle = new Throttle({
      active: true,     // set false to pause queue
      rate: 5,          // how many requests can be sent every `ratePer`
      ratePer: 10000,   // number of ms in which `rate` requests may be sent
      concurrent: 2     // how many requests can be sent concurrently
    })

    request
    .get('http://placekitten.com/100/100')
    .use(throttle.plugin())
    .end((err, res) => { ... })

## Events

    const request     = require('superagent')
    const Throttle    = require('superagent-throttle')

    let throttle = new Throttle()
    .on('sent', (request) => { ... }) // sent a request
    .on('received', (request) => { ... }) // received a response
    .on('drained', () => { ... }) // received last response

## Serialised Sub Queues

When using API's to update a client, you may want some serialised requests which
still count towards your rate limit, but do not block other requests. You can
do that by passing a uri (not necessarily a valid url) to `throttle.plugin`, for
those requests you want to serialise, and leave it out for other async requests.
This can be done on the fly, you don't need to initialise subqueues first.

    let endpoint = 'http://example.com/endpoint'
    request
    .get(endpoint)
    .set('someData': someData)
    .use(throttle.plugin(endpoint))
    .end(callback)

it's common to use an endpoint for the uri, simply to serialise requests to that
endpoint without interfering with requests to other endpoints

## Options

 * `active`: whether or not the queue is paused. (default: true)
 * `rate`: how many requests can be sent every `ratePer`. (default: 40)
 * `ratePer`: number of ms in which `rate` requests may be sent. (default: 40000)
 * `concurrent`: how many requests can be sent concurrently. (default: 20)

Options can be set after instantiation using the `options` method.

```javascript

    var throttle = new require('./index')({ active: false }) // start paused
    throttle.options('active', true) // unpause

```

## Advanced Usage

See [theMovieDbApi](https://github.com/leviwheatcroft/moviedb-api/blob/master/index.js)

## Api

See the [fancy annotated code](http://leviwheatcroft.github.io/superagent-throttle/docs/index.js.html).

## Changelog

### 0.2.1

 * fixed bug where errored requests are not cleared from concurrency count
   (possibly related to issue #6)

### 0.2.0

 * Removed extraneous dependencies
 * Fancy ES6 Class definition
 * Added unit tests
 * Event emitter
 * breaks 0.1.0 syntax



## Author

Levi Wheatcroft <levi@wht.cr>

## Contributing

Contributions welcome; Please submit all pull requests against the master
branch.

## License

 - **MIT** : http://opensource.org/licenses/MIT
