# Micro Focus Swagger UI

This project is a Micro Focus branded version of the [Swagger UI](https://github.com/swagger-api/swagger-ui).

## Usage

It can be used from another Java project by including the following dependency:

```xml
<dependency>
    <groupId>com.microfocus.webjars</groupId>
    <artifactId>swagger-ui-dist</artifactId>
</dependency>
```

To override swagger configuration params include a **microfocus-config.js** file, similar to the one [here](./src/main/resources/microfocus-config.js):

Swagger configuration params that can be set in microfocus-config.js:
 - url: "api-docs/swagger.yaml"
 - dom_id: "#swagger-ui
 - operationsSorter: "alpha"
 - tagsSorter: "alpha"
 - docExpansion: "none"
 - defaultModelsExpandDepth: -1
 - deepLinking: true

For a complete list of swagger configuration params refer [Swagger configuration params](https://github.com/swagger-api/swagger-ui/blob/master/docs/usage/configuration.md#core).

## Using this module in Spring Boot

[Here](https://github.com/CAFapi/caf-swagger) is an example of how this swagger-ui can be used in a Spring Boot application : 
1. Add the module as a runtime dependency.
2. Include a resource file called **microfocus-config.js** with the swagger configuration overrides, similar to the one [here](./src/main/resources/microfocus-config.js).
Make sure to overide the "url" param to point to your swagger contract.
3. To facilitate [serving static resources](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/ResourceHandlerRegistry.html) in Spring Boot override the `addResourceHandlers` function in 

```java
@Override
public void addResourceHandlers(final ResourceHandlerRegistry registry) {
    // configure the external-facing URI path by adding a resource handler
    registry.addResourceHandler("/swagger/**");
    // map that external-facing URI path internally to the physical path where the resources are actually located
    registry.addResourceLocations(             
                "classpath:/swagger/",
                 contractPath,
                 "classpath:/META-INF/resources/webjars/microfocus-swagger-ui-dist/1.0.0/") 
            .resourceChain(true)
            .addResolver(new PathResourceResolver());
}
```

## Using this module in Dropwizard

1. Add the module as a runtime dependency.
2. Include a resource file called **swagger-ui-config.js** to override the swagger configuration, similar to the one [here](./src/main/resources/microfocus-config.js).
Make sure to overide the "url" param to point to your swagger contract.
3. To facilitate serving static resources in Dropwizard add [AssetBundles](https://www.dropwizard.io/en/latest/manual/core.html#bundles)

```java
@Override
public void initialize(Bootstrap<T> bootstrap) {
    bootstrap.addBundle(new AssetsBundle(
        "/META-INF/resources/webjars/microfocus-swagger-ui-dist/1.0.0/", "/swagger/", "index.html", "swagger-ui"));
    bootstrap.addBundle(new AssetsBundle(
        "/swagger-ui-config.js", "/swagger/microfocus-config.js", null, "swagger-ui-config"));

    super.initialize(bootstrap);
}
```

## Using this module in Tomcat
1. Add the module as a provided dependency.
2. Include a resource file called **microfocus-config.js** with the swagger configuration overrides, similar to the one [here](./src/main/resources/microfocus-config.js).
Make sure to overide the "url" param to point to your swagger contract.
3. Repackage the swagger assets into the war file to be deployed in Tomcat.

**Unpack the swagger-ui artifact excluding the default configuration file**
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <goals>
                <goal>unpack</goal>
            </goals>
            <configuration>
                <artifactItems>
                    <artifactItem>
                        <groupId>com.github.microfocus</groupId>
                        <artifactId>swagger-ui-dist</artifactId>
                        <version>1.0.0</version>
                        <outputDirectory>${project.build.directory}/swagger-ui</outputDirectory>
                        <excludes>
                            META-INF/resources/webjars/microfocus-swagger-ui-dist/1.0.0/microfocus-config.js
                        </excludes>
                    </artifactItem>
                </artifactItems>
            </configuration>
        </execution>
    </executions>
</plugin>
```

**Package the war file with the swagger-ui assets and the swagger-ui configuration overide file**
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <configuration>
        <failOnMissingWebXml>false</failOnMissingWebXml>
        <webResources>
            <resource>
                <!--Include swagger-ui assets-->
                <directory>
                  ${project.build.directory}/swagger-ui/META-INF/resources/webjars/microfocus-swagger-ui-dist/1.0.0
                </directory>
                <targetPath>.</targetPath>
            </resource>
            <resource>
                <!--Include microfocus-config.js overide file from src/main/html-->
                <directory>src/main/html</directory>
                <targetPath>.</targetPath>
            </resource>
            <resource>
                <!--Include swagger contract-->
                <directory>
                  ${project.build.directory}/swagger-contract/com/hpe/darwin/tag/service/contract
                </directory>
                <targetPath>api-docs</targetPath>
            </resource>
        </webResources>
    </configuration>
</plugin>
```

