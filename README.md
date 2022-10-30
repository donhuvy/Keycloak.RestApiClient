# .NET / C# Keycloak.RestApiClient
This is a .NET / C# REST API client

* for the [Keycloak Admin REST API](https://www.keycloak.org/docs-api/19.0.3/rest-api/) 
* auto-generated by [OpenAPI Generator](https://openapi-generator.tech)
* using the OpenAPI definitions generated by [Keycloak OpenApi Generator](https://github.com/dahag-ag/keycloak-openapi)

## Documentation for API Endpoints

* Official documentation: [Keycloak Admin REST API](https://www.keycloak.org/docs-api/19.0.3/rest-api/)
* Generated Swagger UI: [Swagger Editor](https://editor.swagger.io/?url=https://raw.githubusercontent.com/dahag-ag/keycloak-openapi/main/OpenApiDefinitions/keycloak-19.0.0.json)

## Keycloak versions supported

| Keycloak.RestApiClient | Keycloak |
| ---------------------- | -------- |
| 19.n.n                 | 19.x.x   |

## Frameworks supported

- .NET Core >=1.0
- .NET Framework >=4.6
- Mono/Xamarin >=vNext

## Installation
Install from NuGet package
```powershell
Install-Package Schick.Keycloak.RestApiClient
```

## Getting Started

#### Method names

Method names are humanized:

`GET` on path `/{realm}/users` becomes `GetUsers(Async)`

`GET` on path `/{realm}/identity-provider/providers/{provider_id}` becomes `GetIdentityProviderProvidersByProviderId(Async)`

### Sample code

```csharp
using FS.Keycloak.RestApiClient.Api;
using FS.Keycloak.RestApiClient.Client;
using System;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    internal class Program
    {
        private static async Task Main(string[] args)
        {
            using var httpClient = new KeycloakHttpClient("<authServerUrl>", "<admin_user>", "<password>");
            using var usersApi = ApiClientFactory.Create<UsersApi>(httpClient);

            var users = await usersApi.GetUsersAsync("MyRealm");
            Console.WriteLine($"Users: {users.Count}");
        }
    }
}
```

## Advanced Usage
To use the API client with a HTTP proxy, setup a `System.Net.WebProxy`
```csharp
Configuration c = new Configuration();
System.Net.WebProxy webProxy = new System.Net.WebProxy("http://myProxyUrl:80/");
webProxy.Credentials = System.Net.CredentialCache.DefaultCredentials;
c.Proxy = webProxy;
```

### Connections
Each ApiClass (properly the ApiClient inside it) will create an instance of HttpClient. It will use that for the entire lifecycle and dispose it when called the Dispose method.

To better manager the connections it's a common practice to reuse the HttpClient and HttpClientHandler (see [here](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net) for details). To use your own HttpClient instance just pass it to the ApiClass constructor.

```csharp
HttpClientHandler yourHandler = new HttpClientHandler();
HttpClient yourHttpClient = new HttpClient(yourHandler);
var api = new YourApiClass(yourHttpClient, yourHandler);
```

If you want to use an HttpClient and don't have access to the handler, for example in a DI context in Asp.net Core when using IHttpClientFactory.

```csharp
HttpClient yourHttpClient = new HttpClient();
var api = new YourApiClass(yourHttpClient);
```
You'll loose some configuration settings, the features affected are: Setting and Retrieving Cookies, Client Certificates, Proxy settings. You need to either manually handle those in your setup of the HttpClient or they won't be available.

Here an example of DI setup in a sample web project:

```csharp
services.AddHttpClient<YourApiClass>(httpClient =>
   new PetApi(httpClient));
```