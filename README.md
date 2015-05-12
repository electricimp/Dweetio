# dweet.io
[dweet.io](http://dweet.io) is a “rediculously simple messaging (and alerts)” system for the Internet of Things.

This API wraps the dweet.io API for non-locked Things.

**To add this library to your project, add** `#require "Dweetio.class.nut:1.0.0"` **to the top of your agent code**

## Class Usage

## Callbacks

All methods (except *stream()*) have an optional callback parameter. If a callback function is supplied, the request will be made asynchronously, and the callback will be triggered once the request is fulfilled. The callback function must inlcude a single parameter into which will be passed an a Squirrel table containing three fields: *statuscode*, *headers* and *body*. If a callback is not supplied, the request will be made synchronously and the method will return the same table outlined above.

## Constructor: DweetIO(*[baseURL]*)

Call the constructor to instantiate a new Dweet.IO client. The base URL can be overridden if required, but if no *baseUrl* is passed, the default ```https://dweet.io``` will be used.

```squirrel
client <- DweetIO()
```

## Class Methods

## dweet(*thing, data, [callback]*)

The *dweet()* method can be used to send a dweet.

```sqiurrel
// Asynchronous dweet

client.dweet("myThing", { "field1": 1, "field2": "test" }, function(resp) {
    server.log(resp.statuscode + ": " + resp.body)
})

// Synchronous dweet

local resp = client.dweet("myThing", { "field1": 1, "field2": "test" })
```

## get(*thing, [callback]*)

The *get()* method returns the most recent dweet from the specified *thing*:

```squirrel
client.get("myThing", function(response) {
    if (response.statuscode != 200) 
    {
        server.log("Error getting dweet: " + response.statuscode + " - " + response.body)
        return
    }

    local data = http.jsondecode(response.body)["with"][0]
    
    // Do something with the data
    
    . . .
})
```

## getHistory(*thing, [callback]*)

The *getHistory()* method will return up to the last 500 dweets to the specified thing over a 24-hour period:

```squirrel
client.getHistory("myThing", function(response) {
    if (response.statuscode != 200) 
    {
        server.log("Error getting dweets: " + response.statuscode + " - " + response.body)
        return
    }

    local data = http.jsondecode(response.body)["with"]
})
```

## stream(*thing, callback*)

The *stream()* method opens a stream to the dweet service and will execute the callback whenever new information is available for the specified *thing*. The *stream()* method **must** be supplied with a callback, and unlike the other methods in this class, the stream callback is triggered with the *thing*’s data not a response table:

```squirrel
client.stream("myThing", function(thing) {
    if ("status" in thing) 
    {
        device.send("status", thing.status)
    }
})
```

## License

The dweet.io library is licensed under the [MIT License](./LICENSE).
