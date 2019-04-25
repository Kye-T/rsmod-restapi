# rsmod-restapi
Integrate REST API into your game engine easily with an RestApiService

Currently at version 1.0.1

# Introduction
An API wrapper for RS MOD. Designed to give your webapp or equivilent the ability to view live in-game data and statistics. You can also add new routes and controllers for actions.

V1.0.1 does not yet utilize oAuth. It is soon to be added in future updates.

# Getting Started
- Clone the repository and upload the files into their corresponding places in your RS MOD local project.
- Inside of your game.yml or, for distributing, game.example.yml add the following to your services:

```
services:
  - class: gg.rsmod.plugins.service.restapi.RestApiService
```

There are many configurations you can alter based on your needs. Here are a list of configurations you can change when declaring the class:

- `origin` - This is the remote origins that can access your web server (CORS), Default `*`
- `methods` - This is the methods (GET/PUT/POST/DELETE) that remote origins can use on your web server (CORS), Default `GET, POST`
- `headers` - This is the headers that remote origins can request on your web server (CORS), Default `X-PINGOTHER, Content-Type`

In future updates, oAuth will be utilized thus giving the ability to add the configuration to protect routes:

- `auth` - This is disabled by Default as it is not used in V1.0.1 <= versions.
