A control server for G-Homa Wifi plugs written in [node](http://nodejs.org).

This library enables the use of the wifi plugs without the need to allow them to call home.

This library is intended to be used as part of a home automatisation solution. For example, it can be used together with a framework like [express.js](https://expressjs.com/) to switch the plugs on or off by simple http calls.

Installation
------------

```bash
$ npm install ghoma
```

Usage examples
--------------

This example shows the callback methods provided.

```js
var ghoma = require('ghoma');

// Uncomment this line to get a detailed console log output
// ghoma.log=console.log;

// A new plug was registered.
ghoma.onNew = function(ghoma) {
  console.log('Registerd    ' + ghoma.remoteAddress+"  "+ghoma.id);
  // Switch the plug on
  ghoma.on();
}

// Called when the plug switches on or off
ghoma.onStatusChange = function(ghoma) {
  console.log('New state of ' + ghoma.remoteAddress+' is '+ghoma.state+' triggered '+ghoma.triggered);
}

// Called when the plug connection to the server was lost
ghoma.onClose = function(ghoma) {
  console.log('Closed       ' + ghoma.remoteAddress);
}

/* Enable to listen for heartbeats of the plugs
ghoma.onHeartbeat = function(ghoma) {
  console.log('Heartbeat ' + ghoma.remoteAddress);
}
*/

// Start a listening server on this port
ghoma.startServer(4196);
```

A more comprehensive example shows the node-ghoma library in combination with the express framework. See [express_example.js](https://github.com/rodney42/node-ghoma/blob/master/express_example.js) in the git repository.


API description
---------------

### Notifications
These notifications are triggered. See also example above.

#### onNew(ghoma)
Called when a new plug is registered with the control server.

#### onStatusChange(ghoma)
Called if the plug was switched on or off.

#### onClose(ghoma)
Called if the connection to the plug was lost.

#### onHeartbeat(ghoma)
Called when a heartbeat event from the plug occurred.


### Methods
Methods provided by the module.

#### forEach(callback)
Iterate through each registered ghome plug object.

#### get(id)
Helper method to get a plug by id. The id is the hex representation of the short mac of the plug. It can be used to unique identify the plug.

### The ghoma object

The ghoma object is used as callback argument for each notification and as a result of method calls.

```js
{
  "state": "on",          // 'on' or 'off'
  "triggered": "local",   // 'local' or 'remote'. 'local' if the plug button was used.
  "remoteAddress": "::ffff:192.168.12.136",
  "remotePort": 12635,
  "initalized": "2016-07-24T20:57:00.525Z",  // First seen time
  "shortMac": {
    "type": "Buffer",
    "data": [
      ...
    ]
  },
  "id": "62ad10",   // id from the short mac
  "fullMac": {
    "type": "Buffer",
    "data": [
    ...
    ]
  },
  "prevstate": "unknown",  // The previous state is unknown if the plug was yet registered. 'on' or 'off' otherwise
  "statechanged": "2016-07-26T20:57:02.975Z"  // / Last state change
}
```

G-Homa installation
-------------------

With firmware version <=1.0.06 you can use the embedded web server to adjust the control server address. See the fhem wiki page for instructions.
With newer firmware version, this easy way to configure the plug is disabled. The only known way to run your own control server is to setup your own DNS and DHCP service. Then the DNS entry for 'plug.g-homa.com' must point to your own server and you are forced to use '4196' as listening port.

Further information
-------------------

The network protocol was deciphered by others - full credits go to them.
See the [FHEM Wiki Page](http://www.fhemwiki.de/wiki/G-Homa) (german)
and
[Full disclosure for gHoma on seclists](http://seclists.org/fulldisclosure/2015/May/45) (german).

The hardware producer site can be found
[here](http://www.g-homa.com/index.php/de/) (also in german).
