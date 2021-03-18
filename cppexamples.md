
# C++
This section contains C++ examples to get you started using this plugin with C++.

## Creating an HTTP Server
You can easily create an HTTP server with the `UBlueprintHttpServer::CreateHttpServer()` method.
```cpp
// Keep somewhere the HttpServer as an UPROPERTY to prevent
// it from being garbage collected.
UBlueprintHttpServer* HttpServer =  UBlueprintHttpServer::CreateHttpServer();
```

## Creating an HTTPS Server
You can easily create an HTTPS server with the `UBlueprintHttpsServer::CreateHttpsServer()` method.
```cpp
const FString CertLocation = TEXT("path/to/my/cert.pem");
const FString KeyLocation  = TEXT("path/to/my/key.pem");

// We should keep it as an UPROPERTY() somewhere.
UBlueprintHttpServer* const HttpServer = UBlueprintHttpsServer::CreateHttpsServer(CertLocation, KeyLocation);
```

?> `UBlueprintHttpsServer` inherits from `UBlueprintHttpServer`. You should program using the `UBlueprintHttpServer` class to easily switch from HTTP to HTTPS without breaking your code.

## Listening for a Route
To listen for a route, call the method matching the verb of the route. The available verbs are the following:

|Verb|
|:---|
|`GET`|
|`PUT`|
|`POST`|
|`PATCH`|
|`DELETE`|
|`OPTIONS`|

Here is an example listening for two routes:
```cpp
// We can use method chaining on the object as each route method
// returns a pointer to the server.
HttpServer

// A simple GET.
// We bind a lambda expression but you can bind UObject's UFunctions as well.
// We choose to execute the callback on Game Thread. Note that it makes the request 
// wait for the Game Thread to execute the callback for a few milliseconds.
-> Get(TEXT("/data/"), FHttpServerRouteCallback::CreateLambda([](const FBlueprintHttpRequest& Request, FBlueprintHttpResponse& Response) -> void
{
    Response.SetBody(TEXT("Hello World from GET."));
    Response.Send();
}), /* bExecuteOnGameThread */ true)

// Routes can be regex expressions.
// Here we match all routes of the form "/users/{number}".
-> Post(TEXT("/users/(\d+)"), FHttpServerRouteCallback::CreateLambda([](const FBlueprintHttpRequest& Request, FBlueprintHttpResponse& Response) -> void
{
    Response.AddHeader(TEXT("YourIpIs"), Request.GetRemoteAddress());
    Response.SetBody(TEXT("Hello World from POST."));
    Response.Send();
}));
```

## Serving Static Files
To serve directly static files, we use the `AddMountPoint` method. It can be used as showed:
```cpp
// Mounts a folder to the server.
HttpServer->AddMountPoint(TEXT("/static/"), TEXT("C:/www/"));
```

!> This method is not thread-safe, you can't call it once the server is started.

## Starting the Server
Once the routes are correctly setup, we start serving our clients by calling the `Listen` method:
```cpp
// Listen for all incoming clients at 127.0.0.1:8080.
HttpServer->Listen(TEXT("127.0.0.1"), 8080, FHttpServerListenCallback::CreateLambda([](const bool bServerStarted) -> void
{
    if (!bServerStarted)
    {
        // Handle error.
        // Additional information is available in the output log.
    }
});
```


