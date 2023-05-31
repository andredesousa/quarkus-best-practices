# Quarkus Best Practices

Quarkus is a Java framework tailored for deployment on Kubernetes.
These tips are based on Quarkus documentation, books, articles and professional experience.

## Table of Contents

1. [Follow code conventions](#follow-code-conventions)
2. [Follow a package naming convention](#follow-a-package-naming-convention)
3. [Use Maven/Gradle wrapper](#use-mavengradle-wrapper)
4. [Use Dev services](#use-dev-services)
5. [Use Quarkus extensions](#use-quarkus-extensions)
6. [Use OpenAPI and Swagger UI](#use-openapi-and-swagger-ui)
7. [Use code generators](#use-code-generators)
8. [Use database migrations](#use-database-migrations)
9. [Use resources only for routing](#use-resources-only-for-routing)
10. [Use services for business logic](#use-services-for-business-logic)
11. [Use repository pattern](#use-repository-pattern)
12. [Use validators](#use-validators)
13. [Use DTOs](#use-dtos)
14. [Use caching](#use-caching)
15. [Provide a global exception handling](#provide-a-global-exception-handling)
16. [Avoid global state and mutability](#avoid-global-state-and-mutability)
17. [Remove unused code](#remove-unused-code)
18. [Expose health checks and metrics](#expose-health-checks-and-metrics)
19. [Externalize all configurations](#externalize-all-configurations)
20. [Hide your secrets](#hide-your-secrets)
21. [Analyse your code](#analyse-your-code)
22. [Check dependencies for vulnerabilities](#check-dependencies-for-vulnerabilities)
23. [Compile natively](#compile-natively)
24. [Prefer REST Client Reactive](#prefer-rest-client-reactive)
25. [Test continuously](#test-continuously)

## Follow code conventions

Code conventions are base rules that allow the creation of a uniform code base across an organization.
Following them does not only increase the uniformity and therefore the quality of the code.
[Oracle Code Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-contents.html) and [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html) are the two main coding styles for Java.
[Checkstyle](https://checkstyle.sourceforge.io/) is a tool to help programmers find class design problems, method design problems, and others.
It also can check code layout and formatting issues.
[Prettier](https://prettier.io/) combined with [Spotless](https://github.com/diffplug/spotless) can be used to enforce a consistent code style.
Code conventions must be dynamic and adaptable for each team and project.

## Follow a package naming convention

Proper packaging will help to understand the code and the flow of the application easily.
You can structure your application with meaningful packaging.
You can use the following naming convention for your packages:

- The `entity` package contains the database entities of the application.
- The `repository` package contains all the repositories-related classes.
- The `service` package contains all the business logic-related classes.
- The `resource` package contains all resources classes of the application.
- Other common packages are `config`, `mapper`, `filter`, `exception`, etc.

This style is very convenient in small-size microservices.
If you are working on a huge code base, a feature-based approach can be used.

## Use Maven/Gradle wrapper

The recommended way to execute any Maven/Gradle build is with the help of the wrapper ([Maven Wrapper](https://maven.apache.org/wrapper/) and [Gradle Wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html)).
Instead of installing many versions of it in the operating system, you can just use the project-specific wrapper script.
The wrapper is a script that invokes a declared version of Maven/Gradle, downloading it beforehand if necessary.
As a result, developers can get up and running with a Maven/Gradle project quickly without having to follow manual installation processes saving time.

## Use Dev services

Quarkus supports the automatic provisioning of unconfigured services in development and test mode.
From a developer's perspective this means that if you include an extension and don't configure it then Quarkus will automatically start the relevant service (usually using [Testcontainers](https://www.testcontainers.org/) behind the scenes) and wire up your application to use this service.
It includes databases, brokers like Kafka or RabbitMQ, and some other tools.
This feature is called [Dev Services](https://quarkus.io/guides/dev-services).
If you want to disable all Dev Services you can use the `quarkus.devservices.enabled=false` config property.

## Use Quarkus extensions

Quarkus has a very active and expanding ecosystem with hundreds of extensions available.
Think of [Quarkus extensions](https://quarkus.io/extensions/) as your project dependencies.
Quarkus's mission is to transform your entire application including the libraries it uses, into an artifact that uses significantly less resources than traditional approaches.
Quarkus extensions configure, boot and integrate a framework or technology into your Quarkus application.
You can use [code.quarkus.io](https://code.quarkus.io/) to help you create a new Quarkus project and choose possible dependencies according to your needs.

## Use OpenAPI and Swagger UI

[OpenAPI Specification](https://swagger.io/specification/) is the factual standard for creating REST APIs.
An OpenAPI definition can then be used by documentation generation tools to display the API, code generation tools to generate servers and clients in various programming languages, testing tools, and many other use cases.
[Swagger UI](https://swagger.io/tools/swagger-ui/) allows anyone to visualize and interact with the API's resources without having any of the implementation logic in place.
It's automatically generated from your OpenAPI Specification.
Quarkus provides the Smallrye OpenAPI extension compliant with the MicroProfile OpenAPI specification in order to generate your API OpenAPI v3 specification.
You just need to add [quarkus-smallrye-openapi](https://mvnrepository.com/artifact/io.quarkus/quarkus-smallrye-openapi) dependency to your project, or you can read [Using OpenAPI and Swagger UI](https://quarkus.io/guides/openapi-swaggerui) guide for more details.

## Use code generators

Java is a great language, but it can sometimes get too verbose for common tasks.
[Lombok](https://projectlombok.org/) is a Java library that is used to minimize or remove the boilerplate code.
Just by using the annotations, you can save space and readability of the source code.
[MapStruct](https://mapstruct.org/) is a code generator that greatly simplifies the implementation of mappings between Java bean types based on a convention over configuration approach.
Also, OpenAPI Specification can be used by code generation tools to generate servers and clients for Java and Quarkus applications.
You can use [Quarkus OpenAPI Generator](https://quarkus.io/extensions/io.quarkiverse.openapi.generator/quarkus-openapi-generator) extension or [OpenAPI Generator](https://openapi-generator.tech/docs/plugins) plugin for Maven/Gradle.

## Use database migrations

Database migrations is a process of making changes to database schema during a development process.
You wouldn't develop app code without version control.
The same should be true for database changes. Migrations are most commonly written in SQL.
Quarkus provides [Flyway](https://quarkus.io/extensions/io.quarkus/quarkus-flyway) or [Liquibase](https://quarkus.io/extensions/io.quarkus/quarkus-liquibase) extensions for database migrations.
You can read [Using Flyway](https://quarkus.io/guides/flyway) or [Using Liquibase](https://quarkus.io/guides/liquibase) guides for more details.
If you are using Liquibase, you will have defined your database as a set of Liquibase change sets, in XML, YAML, or JSON , it can be easily used as a source of meta information by the [jOOQ](https://www.jooq.org/) code generator.

## Use resources only for routing

Resources are dedicated to routing. Resources are the ultimate target of requests, then requests will be handed over to the service layer and processed by the service layer.
They are stateless and all business logic should not place on them.
Resources should deal with the HTTP layer of the application and oriented around a business capability.
See the code snippet of a resource:

```java
@Path("/user")
public class UserResource {
    @Inject
    protected UserService userService;

    @GET
    public List<User> findAll() {
        return userService.findAll();
    }
}
```

## Use services for business logic

The business logic of the application goes here with validations, caching, etc.
Build your services around business capabilities/domains/use-cases.
Services communicate with the persistence layer and receive the results. Services are singleton and annotated with `@ApplicationScoped`.
See the code snippet of a service:

```java
@ApplicationScoped
public class UserService {
    @Inject
    protected UserRepository userRepository;

    public List<User> findAll() {
        return userRepository.listAll();
    }
}
```

## Use repository pattern

Repositories are a very popular pattern for Java-based persistence layers.
They encapsulate the database operations you can perform on entity objects and aggregates.
That helps to separate the business logic from the database operations and improves the reusability of your code.
Quarkus recommends [Panache](https://quarkus.io/guides/hibernate-orm-panache) for implementing repositories.
See the code snippet of a repository:

```java
@ApplicationScoped
public class UserRepository implements PanacheRepository<User> {}
```

## Use validators

REST APIs need to validate the data it receives, and Quarkus provides rich built-in support for validating REST API request objects.
[Quarkus Hibernate Validator](https://quarkus.io/extensions/io.quarkus/quarkus-hibernate-validator) extension offers standard validation primitives such as `@NotNull`, `@NotBlank`, `@Size`, or `@Email` and if you need anything more advanced, you can easily implement your custom annotations.
See the code snippet of a DTO:

```java
public class User {
    @NotNull
    @Size(min = 4, max = 20)
    public String username;

    @NotNull
    @Size(min = 8, max = 20)
    public String password;
}
```

You just need to add [quarkus-hibernate-validator](https://mvnrepository.com/artifact/io.quarkus/quarkus-hibernate-validator) dependency to your project, or you can read [Validation with Hibernate Validator](https://quarkus.io/guides/validation) guide for more details.

## Use DTOs

While it is also possible to directly expose the database entities on REST endpoints to send/receive client data, this is not the best approach.
It creates high coupling between the persistence models and the API models.
The better approach is defining a separate [Data Transfer Object](https://www.baeldung.com/java-dto-pattern) (DTO) that represents the API resource class which is mapped from a database entity or multiple entities. To do this mapping, you can use [MapStruct](https://mapstruct.org/).
With DTOs, you can build different views from your domain models, allowing you to create other representations of the same domain but optimizing them to the clients' needs without affecting your domain design.

## Use caching

Caching is an important factor when talking about application performance.
Quarkus offers a set of annotations that can be used in a CDI managed bean to enable caching abilities.
You just need to add [quarkus-cache](https://mvnrepository.com/artifact/io.quarkus/quarkus-cache) dependency to your project, or you can read [Application Data Caching](https://quarkus.io/guides/cache) guide for more details.
When a method annotated with `@CacheResult` is invoked, Quarkus will compute a cache key and use it to check in the cache whether the method has been already invoked.
If you are not satisfied with default caching, you can use [Hazelcast](https://hazelcast.com/), [Redis](https://redis.io/), or any other distributed caching implementations.
Quarkus provides [Hazelcast](https://quarkus.io/extensions/com.hazelcast/quarkus-hazelcast-client) or [Redis](https://quarkus.io/extensions/io.quarkus/quarkus-redis-cache) extensions for caching as an alternative to default caching.

## Provide a global exception handling

Besides the classic 404 error page, you should also look at what our application returns in case of an uncaught exception.
Normally, exceptions will be translated to a 500 error (Internet Server Error) and written to the log.
There are two approaches to [centralized error handling](https://marcelkliemannel.com/articles/2021/centralized-error-handling-and-a-custom-error-page-in-quarkus/) in Quarkus.
Both have their advantages and disadvantages.
See the code snippet of a Global Exception Mapper:

```java
@Provider
public class GlobalExceptionMapper implements ExceptionMapper<Exception> {
    @Override
    public Response toResponse(Exception exception) {
        if (exception instanceof ClientErrorException) {
            return ((ClientErrorException) exception).getResponse();
        } else {
            return Response.status(Status.INTERNAL_SERVER_ERROR).entity(exception.getMessage()).build();
        }
    }
}
```

The second approach is a Global Error Response Filter.
With a provider class that implements `ContainerResponseFilter` interface, you can create a filter that modifies all responses going out from the server.

## Avoid global state and mutability

Problems caused by parallel execution of programs are nerve-wrackingly elusive and often times extremely difficult to debug.
First, always remember the "global state" issue.
If you're creating a multithreaded application, absolutely anything that is globally modifiable should be closely monitored and, if possible, removed altogether.
Immutability comes directly from functional programming and, adapted to OOP, states that class mutability and changing state should be avoided.
This, in short, means foregoing setter methods and having private final fields on all your model classes.
This way you can be certain that no contention problems arise and that accessing object properties will provide the correct values at all times.

## Remove unused code

Unused code or dead code is any code which will never be executed.
It may be some condition, loop or any file which was simply created but wasn't used in our project.
It is a problem because that code has no sense, and you can drop it.
Quarkus detects and removes unused beans to help you on your way to a subatomic application.
If something goes wrong, it's also possible to disable this optimization via `quarkus.arc.remove-unused-beans=false` config property.
Less code also increases maintenance, IDE performance and makes it easier to understand.
Also, you can use tools like [OpenRewrite](https://docs.openrewrite.org/) or [Spotless](https://github.com/diffplug/spotless) to remove unused code.

## Expose health checks and metrics

Quarkus supports you with readiness/liveness health checks via [SmallRye Health](https://quarkus.io/guides/smallrye-health).
It allows applications to provide information about their state to external viewers which is typically useful in cloud environments where automated processes must be able to determine whether the application should be discarded or restarted.
Depending on the HTTP status code returned from a GET request, the agent will act when it receives an "unhealthy" response.
For application metrics, you can use [SmallRye Metrics](https://quarkus.io/guides/smallrye-metrics).
The metrics can be read remotely using the JSON or OpenMetrics format to be processed by additional tools such as [Prometheus](https://prometheus.io/) and stored for analysis and visualization.

## Externalize all configurations

Externalized configuration can be changed dynamically without having to repackage the source code.
Quarkus application and Quarkus itself are configured using the same mechanism which is based on an implementation of the [MicroProfile Configuration](https://download.eclipse.org/microprofile/microprofile-config-3.0/microprofile-config-spec-3.0.html) specification.
Quarkus reads [configuration properties](https://quarkus.io/guides/config-reference) from system properties, environment variables, *.env* file, *application.properties* and *META-INF/microprofile-config.properties* in descending ordinal.
Quarkus provides *prod*, *test* and *dev* profiles.
You can define properties for multiple profiles inside a single *application.properties* using the syntax `%{profile-name}.config.name`.
You can also define profile-specific files like `application-{profile}.properties`.

## Hide your secrets

Every application must handle secrets such as database credentials, cryptographic keys, or other sensitive data that would allow an attacker to impersonate the application.
When those secrets are provided in a configuration file, they may be accessed by the wrong person in many ways.
Using a dedicated secrets management system, such as [Vault](https://www.vaultproject.io/), ensures you are following the security best practices.
In local development, you can use [.env file](https://quarkus.io/guides/config-reference#env-file).
This file can be placed in the root of the project, but it is advised to not check it in to version control.

## Analyse your code

To ensure long-term code maintainability, you should follow best coding practices and style guide rules.
A linter is a static code analysis tool used to flag programming errors, bugs, stylistic errors, and suspicious constructs.
[Checkstyle](https://checkstyle.sourceforge.io/) is a tool to help programmers find class design problems, method design problems, and others.
[PMD](https://pmd.github.io/) finds common programming flaws like unused variables, empty catch blocks, unnecessary object creation, and so forth.
[SpotBugs](https://spotbugs.github.io/) is used to perform static analysis on Java code.
It looks for instances of "bug patterns".
You can combine these tools to achieve better results.
These checks can also be done by [SonarQube](https://www.sonarqube.org/).

## Check dependencies for vulnerabilities

It is important to ensure that there are no known vulnerabilities throughout your application's dependency tree.
Therefore, you should frequently update your application's dependencies to the latest versions.
You can use [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) to detect publicly disclosed vulnerabilities contained within a project's dependencies.
To implement it, just add [dependency-check-maven](https://jeremylong.github.io/DependencyCheck/dependency-check-maven/index.html) in your *pom.xml* file or [org.owasp.dependencycheck](https://jeremylong.github.io/DependencyCheck/dependency-check-gradle/index.html) in your *build.gradle* file.
When you choose a base image for your project, you indirectly assume the risk of all the container security concerns that the base image is bundled with.
[Trivy](https://github.com/aquasecurity/trivy) is a simple and comprehensive vulnerability scanner for Docker images and other artifacts.

## Compile natively

Native images provide key advantages, such as instant startup, instant peak performance, and reduced memory consumption.
The native executable for your application will contain the application code, required libraries, Java APIs, and a reduced version of a VM.
[Building a Native Executable](https://quarkus.io/guides/building-native-image) with Quarkus requires using a distribution of [GraalVM](https://www.graalvm.org/).
It is also possible to test native executables using `@QuarkusIntegrationTest` annotation. Further details and solutions to common problems can be found in the [Tips for writing native applications](https://quarkus.io/guides/writing-native-applications-tips) guide.

## Prefer REST Client Reactive

Reactive programming is a programming paradigm where the focus is on developing asynchronous and non-blocking applications.
RESTEasy Reactive is a new [Jakarta REST](https://jakarta.ee/specifications/restful-ws/3.1/jakarta-restful-ws-spec-3.1.html) implementation written from the ground up to work with [Vert.x](https://vertx.io/) and is therefore fully reactive and tightly integrated with Quarkus.
You should be able to use it in place of any Jakarta REST implementation.
You just need to add [quarkus-resteasy-reactive](https://mvnrepository.com/artifact/io.quarkus/quarkus-resteasy-reactive) and [quarkus-rest-client-reactive](https://mvnrepository.com/artifact/io.quarkus/quarkus-rest-client-reactive) dependencies to your project to start writing your resources, or you can read [Writing REST Services with RESTEasy Reactive](https://quarkus.io/guides/resteasy-reactive) and [Using the REST Client](https://quarkus.io/guides/rest-client-reactive) guides for more details.

## Test continuously

If you have no tests or an inadequate amount, then every time you ship code, you won't be sure that you didn't break anything.
Always write tests for every new feature/module you introduce.
Quarkus supports [continuous testing](https://quarkus.io/guides/continuous-testing), where tests run immediately after code changes.
This allows you to get instant feedback on your code changes.
Quarkus detects which tests cover which code and uses this information to only run the relevant tests when code is changed.
You can run those tests from the [Dev UI](https://quarkus.io/guides/dev-ui) console as well.
Also, it provides annotations like `@QuarkusTest` and `@QuarkusIntegrationTest` that control the testing framework.
You can read [Testing Your Application](https://quarkus.io/guides/getting-started-testing) guide for more details.

## Bibliography

- [5 design patterns for microservices with Quarkus and MicroProfile](https://eldermoraes.com/5-design-patterns-for-microservices-with-quarkus-and-microprofile/)
- [10 Quarkus REST Client Best Practices](https://climbtheladder.com/10-quarkus-rest-client-best-practices/)
- [Application Data Caching](https://quarkus.io/guides/cache)
- [Building a Native Executable](https://quarkus.io/guides/building-native-image)
- [Centralized Error Response Handling and a Custom Error Page in Quarkus](https://marcelkliemannel.com/articles/2021/centralized-error-handling-and-a-custom-error-page-in-quarkus/)
- [Configuration Reference Guide](https://quarkus.io/guides/config-reference)
- [Massive performance without headaches](https://quarkus.io/blog/resteasy-reactive-faq/)
- [Quarkus Security overview](https://quarkus.io/guides/security-overview-concept)
- [Quarkus Tips, Tricks and Techniques](https://piotrminkowski.com/2021/10/12/quarkus-tips-tricks-and-techniques/)
- [Security Tips and Tricks](https://quarkus.io/guides/security-customization)
- [Simplified Hibernate ORM with Panache](https://quarkus.io/guides/hibernate-orm-panache)
- [Standardize code formatting with Spotless](https://dev.to/ankityadav33/standardize-code-formatting-with-spotless-2bdh)
- [Testing Your Application](https://quarkus.io/guides/getting-started-testing)
- [Unused Beans and Why We Remove Them](https://quarkus.io/blog/unused-beans/)
- [Using OpenAPI and Swagger UI](https://quarkus.io/guides/openapi-swaggerui)
- [Validation with Hibernate Validator](https://quarkus.io/guides/validation)
