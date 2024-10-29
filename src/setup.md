# Setup
This section will guide you on how to setup BlueprintHTTP Server.

## C++
If you plan to use the C++ API, you have to first add the `BlueprintHttpServer` module to your project.
To do so, open `YourProject.Build.cs` and add the following line in the constructor:
```cs
PrivateDependencyModuleNames.AddRange(new string[] { "BlueprintHttpServer" });
```
Once it's done, you can include the C++ files:
```cpp
#include "BlueprintHttpServer.h"  // For the UBlueprintHttpServer class.
#include "BlueprintHttpsServer.h" // For the UBlueprintHttpsServer class.
```

## HTTPS
To use HTTPS, you have to get a certificate as well as a key file. You can generate a self-signed one for free with OpenSSL.

