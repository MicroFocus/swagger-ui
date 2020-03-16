# Microfocus Swagger UI

This project is a Microfocus branded version of the [Swagger UI](https://github.com/swagger-api/swagger-ui).

## Usage

It can be used from another Java project by including the following dependency:

```xml
<dependency>
    <groupId>com.github.microfocus</groupId>
    <artifactId>swagger-ui-dist</artifactId>
</dependency>
```

To override swagger configuration params include a **microfocus-config.js** file, similar to the one [here](./src/main/swagger/microfocus-config.js):

Swagger configuration params that can be set in microfocus-config.js:
 - url: "api-docs/swagger.yaml"
 - dom_id: "#swagger-ui
 - operationsSorter: "alpha"
 - tagsSorter: "alpha"
 - docExpansion: "none"
 - defaultModelsExpandDepth: -1
 - deepLinking: true

For a complete list of swagger configuration params refer [Swagger configuration params](https://github.com/swagger-api/swagger-ui/blob/master/docs/usage/configuration.md#core).
