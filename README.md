# rsmod-restapi
Integrate REST API into your game engine easily with an RestApiService.

Currently at version 1.0.2

# Introduction
An API wrapper for RS MOD. Designed to give your webapp or equivilent the ability to view live in-game data and statistics. You can also add new routes and controllers for actions.

V1.0.2 now uses oAuth! See the [#oAuth] examples.

# Getting Started
- Clone the repository and upload the files into their corresponding places in your RS MOD local project.
- Inside of your game.yml or, for distributing, game.example.yml add the following to your services:

```
services:
  - class: gg.rsmod.plugins.service.restapi.RestApiService
    routes:
      - class: gg.rsmod.plugins.service.restapi.controllers.PlayersController
        origin: "*"
        headers: "Content-Type"
        methods: "GET"
```

There are many configurations you can alter based on your needs. Here are a list of configurations you can change when declaring the class:

- `origin` - This is the remote origins that can access your web server (CORS), Default `*`
- `methods` - This is the methods (GET/PUT/POST/DELETE) that remote origins can use on your web server (CORS), Default `GET, POST`
- `headers` - This is the headers that remote origins can request on your web server (CORS), Default `X-PINGOTHER, Content-Type`

# Creating your own routes

You can easily create robust routes by creating a new route controller inside the `gg.rsmod.plugins.service.restapi.controllers` package.

```
package gg.rsmod.plugins.service.restapi.controllers

class demoController : Controller() {
    override fun init(world: World, routeProperties: ServerProperties) {
        Router.get(uri = "/demo", routeProperties = routeProperties) { req, res ->
            val jsonBuilder = JsonObject()
            jsonBuilder.addProperty("Hello", "World")
            Gson().toJson(jsonBuilder)
        }
    }
}
```

To bind your route to your RestApiService, be sure to add it to your server services configuration.

```
services:
  - class: gg.rsmod.plugins.service.restapi.RestApiService
    routes:
      - class: gg.rsmod.plugins.service.restapi.controllers.demoController
        origin: "127.0.0.1"
        headers: "*"
        methods: "*"
```

# oAuth

V1.0.2 introduces the ability to map routes to oAuth! You can generate an oAuth account by calling the `Router.generateAuth()` method, including your access scope. This will give you a `AuthCreateData` response with your unique client id and secret.

```
val auth: AuthCreateData = Router.generateAuth(AuthScopes.GAME_DATA)
println(auth.clientId)
println(auth.clientSecret)
```

Protecting your routes with oAuth is simple! You can use the `Router.validateAuth()` method which takes in the `Request` and a list of Auths that CAN access this route.

```
package gg.rsmod.plugins.service.restapi.controllers.demoController

class demoController : Controller() {
    override fun init(world: World, routeProperties: ServerProperties) {
        Router.get(uri = "/demo", routeProperties = routeProperties) { req, res ->
            val jsonBuilder = JsonObject()
            when (Router.validateAuth(req, mutableListOf(AuthScopes.GAME_DATA))) {
                AuthResponseData.OK -> {
                    jsonBuilder.addProperty("Hello", "World")
                }
                else -> {
                    jsonBuilder.addProperty("Error", "503, Forbidden")
                }
            }
            Gson().toJson(jsonBuilder)
        }
    }
}
```

Your HTTP request would look similar to this CURL request:

```
curl --header "RSMOD-CLIENT-ID: 1" --header "RSMOD-SECRET: XXXXXXXXXXXXX" http://localhost:4567/demo
```
