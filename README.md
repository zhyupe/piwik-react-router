# Piwik React Router

[![npm package](https://img.shields.io/npm/v/piwik-react-router.svg?style=flat-square)](https://www.npmjs.org/package/piwik-react-router)
[![dependency status](https://img.shields.io/david/peer/joernroeder/piwik-react-router.svg?style=flat-square)](https://david-dm.org/joernroeder/piwik-react-router)
[![Build Status](https://travis-ci.org/joernroeder/piwik-react-router.svg?branch=master)](https://travis-ci.org/joernroeder/piwik-react-router)
[![Coverage Status](https://coveralls.io/repos/github/joernroeder/piwik-react-router/badge.svg?branch=master)](https://coveralls.io/github/joernroeder/piwik-react-router?branch=master)

[Piwik](https://piwik.org) analytics component for [react-router](https://github.com/rackt/react-router)


## Installation

	npm install piwik-react-router --save
or

	yarn add piwik-react-router


## Features

- asynchronous loading of Piwik
- javascript error tracking
- low level access to [`piwik.push()`](#push-args)


## Usage

Simply create your instance with the same `url` and `siteId` as described in the piwik [documentation](https://developer.piwik.org/guides/tracking-javascript-guide) and connect it to your `history` object.

_Starting with v2.0 react-router won't provide a default history. [Why?](https://github.com/rackt/react-router/blob/master/upgrade-guides/v2.0.0.md#no-default-history)_.

```jsx
var PiwikReactRouter = require('piwik-react-router');

var piwik = PiwikReactRouter({
	url: 'your-piwik-installation.com',
	siteId: 1
});

<Router history={piwik.connectToHistory(history)}>
	<Route path="/" component={MyComponent} />
</Router>
```

If you're using react-router prior to v1.0 please head over to the [react-router0.13.x branch](https://github.com/joernroeder/piwik-react-router/tree/react-router0.13.x).

For the url-option you can also include `http://` or `https://` in the beginning of the url, if you piwik installation is on ssl, but your react-site is not, or visa versa.


## Options

### enableLinkTracking: `true`

[Link tracking](http://developer.piwik.org/api-reference/tracking-javascript#using-the-tracker-object) to track outgoing and download links is enabled by default.


### userId: `undefined`

[User ID](http://developer.piwik.org/api-reference/tracking-javascript#using-the-tracker-object) to associate every request with a username or email. More information here: [User ID](http://piwik.org/docs/user-id/)


### updateDocumentTitle: `true`

Updates the document title before adding a new page view as the title may changed during the route rendering. Make sure you call `piwik.track` __after__ React has rendered the `<Handler />` to make this work correctly.
If you don't know how to update the title, check out the great [react-document-title](https://github.com/gaearon/react-document-title) or [react-helmet](https://github.com/nfl/react-helmet) module.


### trackErrors: `false`

By enabling this option occurring javascript errors will be tracked as a `JavaScript Error` piwik event.

see [http://davidwalsh.name/track-errors-google-analytics](http://davidwalsh.name/track-errors-google-analytics) for further details


### ignoreInitialVisit: `false`

By enabling `ignoreInitialVisit` it connects to the history without tracking the initial visit.


### injectScript: `true`

By disabling `injectScript` the piwik.js script will not be injected automatically to allow a separate loading.


### clientTrackerName: `'piwik.js'`

The name of the `piwik.js` static resource on the Piwik server. Set this option if the Piwik instance uses a different name.


### serverTrackerName: `'piwik.php'`

The name of the `piwik.php` script on the Piwik server. Set this option if the Piwik instance uses a different name.


## API

### track (Location location)

Adds a page view from a history [Location](https://github.com/rackt/history/blob/master/docs/Location.md) if the `path` changed.

`path = location.pathname + location.search`


### push (args)

Pushes the specified args to the Piwik tracker the same way as you're using the `_paq.push(args);` directly. You can use this method as the low-level api to call methods from the [Piwik API](http://developer.piwik.org/api-reference/tracking-javascript#list-of-all-methods-available-in-the-tracking-api), [track events](http://piwik.org/docs/event-tracking/#tracking-events) or call [custom functions](http://developer.piwik.org/guides/tracking-javascript-guide).


### trackError (error, [eventName])

Tracks the given error as a new [Piwik Event](http://piwik.org/docs/event-tracking/#tracking-events) for the given event name. If you don't specify any name here it will fallback to `JavaScript Error`.


### connectToHistory(history)

Adds a listener to the passed in `history` and triggers `track(location)` whenever the history changes. Connecting to the history also tracks the location of the initial visit since release `0.7.0` if not manually disabled via [`ignoreInitialVisit`](#ignoreinitialvisit-false)

### disconnectFromHistory()

Disconnects Piwik from a previous connected history. Returns whether it could successfully disconnect.


## A note on serverside rendering

Piwik tracking is disabled for serverside rendering and all API methods are replaced with noop-functions so you don't have to worry about it.
