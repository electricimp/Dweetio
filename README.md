# dweet.io
[dweet.io](http://dweet.io) is a 'rediculously simple messaging (and alerts)' system for the Internet of Things.

This API wraps the dweet.io API for non-locked Things.

## Callbacks
All methods (except **stream**) have an optional callback parameter. If a callback is supplied, the request will be made asyncronously, and the callback will be triggered once the request is fulfilled. If a callback is not supplied, the request will be made syncronously and the method will return a [HTTPResponse](http://electricimp.com/docs/api/httpresponse) object.

# constructor([baseUrl])
Call the constructor to instantiate a new Dweet.IO client. The base URL can be overridden if required (if no baseUrl is passed, the default ```https://dweet.io``` will be used).

```squirrel
client <- DweetIO();
```

# client.dweet(thing, data, [callback])
The **dweet** method can be used to send a dweet.

```sqiurrel
// asynchronous dweet
client.dweet("myThing", { "field1": 1, "field2": "test" }, function(resp) {
    server.log(resp.statuscode + ": " + resp.body);
});

// synchronous dweet
local resp = client.dweet("myThing", { "field1": 1, "field2": "test" });
```

## client.get(thing, [callback])
The **get** method returns the most recent dweet from the specified *thing*:

```squirrel
client.get("myThing", function(resp) {
    if (resp.statuscode != 200) {
        server.log("Error getting dweet: " + resp.statuscode + " - " + resp.body);
        return;
    }

    local data = http.jsondecode(resp.body)["with"][0];
    // do something with the data
});
```

## client.getHistory(thing, [callback])
The **getHistory** thing will return up to the last 500 dweets over a 24-hour period:

```squirrel
client.getHistory("myThing", function(resp) {
    if (resp.statuscode != 200) {
        server.log("Error getting dweets: " + resp.statuscode + " - " + resp.body);
        return;
    }

    local data = http.jsondecode(resp.body)["with"];
});
```

## client.stream(thing, callback)
The **stream** method opens a stream to the dweet service, and will execute the callback whenever new information is available for the specified *thing*. The **stream** method MUST be supplied with a callback, and unlike the other methods in this class, the stream callback is triggered with the *thing*'s data, not an HTTPResponse object:

```squirrel
client.stream("myThing", function(thing) {
    if ("status" in thing) {
        device.send("status", thing.status);
    }
});
```

# License
The dweet.io library is licensed under the [MIT License](./LICENSE).
