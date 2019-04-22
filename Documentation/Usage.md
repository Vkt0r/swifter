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

Declare new routes or endpoints is very easy ! The `HttpServer` contains a [`subscript`](https://github.com/httpswift/swifter/blob/a6a2ba276eeeeeb962addc3621aa417ffcd01537/Sources/HttpServer.swift#L35) that makes painless this process. The `subscript` declared expect an `String` value for the route and should return a optinal closure with the request and the response `((HttpRequest) -> HttpResponse)?`. To declare a new endpoint we can just do it in the following way:


```swift
server["/hello"] = { request in 
    return .ok(.html("You asked for \($0)")) 
}
```

In the above example we declared the endpoint `/hello` to return the `.ok` response. 

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
