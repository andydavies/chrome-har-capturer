chrome-har-capturer
===================

Capture HAR files from a remote Chrome instance.

Install
-------

    npm install chrome-har-capturer

Usage
-----

Start Google Chrome with the option `--remote-debugging-port=9222`.

The following snippet loads an array of URLs serially and generate a cumulative
HAR file, just like the Record button in the
[Network Panel of Chrome Developer Tools][4].

```javascript
var fs = require('fs');
var chc = require('chrome-har-capturer');
var c = chc.load(['https://github.com',
                  'http://reddit.com',
                  'http://www.reddit.com/help/faq']);
c.on('end', function(har) {
    fs.writeFileSync('out.har', JSON.stringify(har));
});
c.on('error', function() {
    console.error('Unable to connect to Chrome');
});
```

### Use the bundled utility

    Usage: chrome-har-capturer [options] URL...

    Options:
      --host, -h     Remote Debugging Protocol host                [default: "localhost"]
      --port, -p     Remote Debugging Protocol port                [default: 9222]
      --output, -o   Dump generated HAR to file instead of stdout
      --verbose, -v  Enable verbose output

This module comes with a utility that can be used to generate a cumulative HAR
file from a list of URLs.

Install globally with:

    npm install -g chrome-har-capturer

Load the same URL list as the above example with:

    chrome-har-capturer -o out.har \
        https://github.com \
        http://reddit.com \
        http://www.reddit.com/help/faq

API
---

### load(urls, [options])

Connects to a remote instance of Google Chrome using the
[Remote Debugging Protocol][3] and loads a list of URLs serially. Returns an
instance of the `Client` class.

`urls` is either an array or a single URL.

`options` is an object with the following optional properties:

- `host`: [Remote Debugging Protocol][3] host. Defaults to `localhost`.
- `port`: [Remote Debugging Protocol][3] port. Defaults to `9222`.
- `chooseTab`: Callback used to determine which remote tab attach to. Takes the
  JSON array returned by `http://host:port/json` containing the tab list and
  must return the numeric index of a tab. Defaults to a function that always
  returns the first one (`function (tabs) { return 0; }`).

### setVerbose([verbose])

Enable or disable verbose prints for debugging purposes.

`verbose`: Verbosity flag. Defaults to `true`.

### Class: Client

#### Event: 'pageStart'

    function (url) {}

Emitted when Chrome is about to load `url`.

#### Event: 'pageEnd'

    function (url) {}

Emitted when Chrome has finished loading `url`.

#### Event: 'pageError'

    function (url) {}

Emitted when Chrome has failed loading `url`. Failed URLs will not appear in the
cumulative HAR object.

#### Event: 'end'

    function (har) {}

Emitted when every given URL has been loaded. `har` is the cumulative HAR object
in JSON format.

#### Event: 'error'

    function () {}

Emitted when `http://host:port/json` can't be reached.

Resources
---------

- [HAR 1.2 Spec][1]
- [HAR Viewer][2]
- [Chrome Developer Tools: Remote Debugging Protocol v1.0][3]

[1]: http://www.softwareishard.com/blog/har-12-spec/
[2]: http://www.softwareishard.com/blog/har-viewer/
[3]: https://developers.google.com/chrome-developer-tools/docs/protocol/1.0/
[4]: https://developers.google.com/chrome-developer-tools/docs/network
