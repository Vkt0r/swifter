# Usage

## How to start ?
```swift
import Swifter 

do {
    let server = HttpServer()
    server["/hello"] = { .ok(.html("You asked for \($0)"))  }
    try server.start()
} catch {
    print("Server start error: \(error)")
}
```

In the above example the server would start by default in the port `8080` and it will run in `localhost`, in case we want to initiate it in another port we should specify it in the [`start`](https://github.com/httpswift/swifter/blob/a6a2ba276eeeeeb962addc3621aa417ffcd01537/Sources/HttpServerIO.swift#L72) function in the following way:

```swift
import Swifter 

do {
    ...
    try server.start(portNumber)
} catch {
    ...
}
```

## Declaring new endpoints or routes

Declare new routes or endpoints is very easy! The `HttpServer` contains a [`subscript`](https://github.com/httpswift/swifter/blob/a6a2ba276eeeeeb962addc3621aa417ffcd01537/Sources/HttpServer.swift#L35) that makes painless this process. The `subscript` declared expect an `String` value for the route and should return a optinal closure with the request and the response `((HttpRequest) -> HttpResponse)?`. To declare a new endpoint we can just do it in the following way:


```swift
server["/hello"] = { request in 
    return .ok(.html("You asked for \($0)")) 
}
```

In the above example is not specified any HTTP method for the new `/hello` endpoint to returning the `.ok` response, but this can be done using the different `MethodRoutes` availables in the library. To specify the type of HTTP method we can use the `subscript` availables in the `MethodRoutes` like the following example:

```swift
server.GET["/hello"] = { request in
    return .ok(.html("You asked for \($0)")) 
}
```

The above example would only match a `GET` request to the `/hello` endpoint.

### Wildcards Path Segments

The library support the use of wildcards (`*`) in the declaration of routes allowing to match any segment were the wildcard is specified, for example:

```swift
server.GET["/hello/*/1/2/3"] = { request in
    return .ok(.html("You asked for \($0)")) 
}
```

The above route would allow to replace anything in the route were the wildcard exist. For example the route `/hello/foo/1/2/3`.

We can use too wildcards for multiple path segments allowing to match an arbitrary number of segments until the next one is matched, for example:

```swift
server.GET["/a/**/e/f/g"] = { request in
    return .ok(.html("You asked for \($0)")) 
}
```

The above route would match any route that starts with the `/a` segment, have anything in the middle and end with the `/e/f/g` segment (e.g `/a/b/c/d/e/f/g`).

>Note that the route `/a/e/f/g` would not be matched with the above route as the wildcards need to matched and aren't optional.

## HttpResponses

We covered most of the default HTTP responses and can be found it in the [`HttpResponse`](https://github.com/httpswift/swifter/blob/a6a2ba276eeeeeb962addc3621aa417ffcd01537/Sources/HttpResponse.swift#L80) `enum ` :

```swift
case switchProtocols([String: String], (Socket) -> Void)
case ok(HttpResponseBody), created, accepted
case movedPermanently(String)
case movedTemporarily(String)
case badRequest(HttpResponseBody?), unauthorized, forbidden, notFound
case internalServerError
case raw(Int, String, [String:String]?, ((HttpResponseBodyWriter) throws -> Void)? )
```

The `HttpResponse` is composed by the following functions:

```swift
/// Define the status code to return in the response
func statusCode() -> Int 

/// Define the phrase to return in the response
func reasonPhrase() -> String

/// Define the HTTP headers to return in the response
func headers() -> [String: String]

/// Define the content to set in the body of the response
func content() -> (length: Int, write: ((HttpResponseBodyWriter) throws -> Void)?)

/// Define the Socket session and returns the handler for the session
func socketSession() -> ((Socket) -> Void)?
```

## Files & Directories
