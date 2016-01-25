# Dweetio

[Dweet.io](http://dweet.io) describes itself as a “ridiculously simple messaging (and alerts)” system for the Internet of Things, and it really is very simple to use &ndash; especially with the library. It wraps the dweet.io API for public Things.

**To add this library to your project, add** `#require "Dweetio.class.nut:1.0.1"` **to the top of your agent code**

## Class Usage

### Constructor: DweetIO(*[baseURL]*)

Call the constructor to instantiate a new Dweet.IO client. It has a single, optional parameter: *baseURL*, the URL to which the data will be sent. The default is `https://dweet.io`.

```
client <- DweetIO();
```

## Class Methods

### Callbacks

All of the library’s methods except *stream()* have an optional callback function parameter. If a callback function is supplied, the request will be made asynchronously, and the callback will be triggered once the request is fulfilled. The callback function must include a single parameter of its own into which will be passed a table containing three fields: *statuscode*, *headers* and *body*. If a callback is not supplied, the request will be made synchronously and the method will return the same table outlined above.

### dweet(*thing, data[, callback]*)

The *dweet()* method can be used to send a dweet.

```
// Asynchronous dweet
client.dweet("myThing", {"field1" : 1, "field2" : "test"}, function(response) {
    server.log(response.statuscode + ": " + response.body);
})
```

```
// Synchronous dweet
local response = client.dweet("myThing", {"field1" : 1, "field2" : "test"});
server.log(response.statuscode + ": " + response.body);
```

### getLatest(*thing[, callback]*)

The *getLatest()* method returns the most recent dweet from the specified *thing*:

```
client.get("myThing", function(response) {
    if (response.statuscode != 200) {
	    server.log("Error getting dweet: " + response.statuscode + " - " + response.body);
	    return;
    }

    local data = http.jsondecode(response.body)["with"][0];
});
```

### getHistory(*thing[, callback]*)

The *getHistory()* method will return all of the most recent 24 hours’ dweets to the specified thing (up to a maximum of 500 dweets).

```
client.getHistory("myThing", function(response) {
    if (response.statuscode != 200) {
	    server.log("Error getting dweets: " + response.statuscode + " - " + response.body);
	    return;
    }

    local data = http.jsondecode(response.body)["with"];
});
```

### stream(*thing, callback*)

The *stream()* method opens a stream to the Dweet service and will execute the callback whenever new information is available for the specified *thing*. Unlike the library’s other methods, *stream()* method **must** be supplied with a callback and the stream callback is triggered with the *thing*’s data not a response table:

```
client.stream("myThing", function(thing) {
    if ("thing" in thing) {
	    device.send("status", thing.content);
    }
});
```

## License

The Dweetio library is licensed under the [MIT License](./LICENSE).
