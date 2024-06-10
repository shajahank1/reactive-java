# Rest api vs functional api in react
> In the context of building APIs with Spring WebFlux (the reactive web framework for Spring), there are two primary ways to define routes and handle requests: annotated (REST) APIs and functional APIs. Both approaches can be used to build reactive applications, but they differ in their style and the way routes are defined and handled. Here's a comparison:

## Annotated (REST) API
### Characteristics
Uses annotations such as @RestController, @GetMapping, @PostMapping, etc., to define request mappings.
Follows a declarative style similar to traditional Spring MVC.
Easier to understand and use for developers familiar with Spring MVC.
```
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/{id}")
    public Mono<User> getUserById(@PathVariable String id) {
        return userService.getUserById(id);
    }

    @GetMapping
    public Flux<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @PostMapping
    public Mono<User> createUser(@RequestBody User user) {
        return userService.createUser(user);
    }
}
`
## Functional API
#### Characteristics
Uses RouterFunction and HandlerFunction to define routes and handle requests.
Follows a functional style.
More flexible and composable but can be harder to read and understand for those new to the functional approach.
```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.reactive.function.server.*;

@Configuration
public class UserRouter {

    @Bean
    public RouterFunction<ServerResponse> route(UserHandler userHandler) {
        return RouterFunctions
            .route(RequestPredicates.GET("/users/{id}"), userHandler::getUserById)
            .andRoute(RequestPredicates.GET("/users"), userHandler::getAllUsers)
            .andRoute(RequestPredicates.POST("/users"), userHandler::createUser);
    }
}
```
```
import org.springframework.stereotype.Component;
import org.springframework.web.reactive.function.server.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@Component
public class UserHandler {

    private final UserService userService;

    public UserHandler(UserService userService) {
        this.userService = userService;
    }

    public Mono<ServerResponse> getUserById(ServerRequest request) {
        String userId = request.pathVariable("id");
        return userService.getUserById(userId)
            .flatMap(user -> ServerResponse.ok().bodyValue(user))
            .switchIfEmpty(ServerResponse.notFound().build());
    }

    public Mono<ServerResponse> getAllUsers(ServerRequest request) {
        Flux<User> users = userService.getAllUsers();
        return ServerResponse.ok().body(users, User.class);
    }

    public Mono<ServerResponse> createUser(ServerRequest request) {
        return request.bodyToMono(User.class)
            .flatMap(user -> userService.createUser(user))
            .flatMap(createdUser -> ServerResponse.ok().bodyValue(createdUser));
    }
}
```
## Comparison
### Feature	Annotated (REST) API	Functional API
- Style	Declarative, uses annotations	Functional, uses functions
- Readability	Easier for developers familiar with Spring MVC	Can be harder for those new to functional programming
- Flexibility	Less flexible, follows a more rigid structure	More flexible, allows for more complex routing logic
- Composition	Limited composition, each handler is typically self-contained	High composition, can easily combine and reuse route handlers
- Complexity	Lower for simple use cases	Higher, especially for more complex routing and handling
## When to Use Which
### Annotated (REST) API:

- Best suited for developers who are already familiar with Spring MVC.
- Ideal for straightforward CRUD operations and simpler applications.
- Easier to read and maintain for those not deeply familiar with functional programming concepts.
### Functional API:

- Offers greater flexibility and composability, making it suitable for more complex applications.
- Ideal for applications requiring dynamic or programmatically defined routes.
- Preferred in cases where a functional style aligns better with the overall design of the application.
> Both approaches can coexist in the same application, allowing you to use the best tool for each specific part of your project.

## what is practical advantage of function api over  rest api  with real world use case

> Practical Advantages of Functional API Over REST API in Real-World Use Cases
While both the annotated (REST) API and the functional API can be used to build reactive applications with Spring WebFlux, there are specific scenarios where the functional API offers practical advantages. Here are some of the key advantages along with real-world use cases:

### 1. Dynamic Routing
Advantage: Functional APIs allow for more dynamic and programmable routing configurations.
Use Case: In an API gateway or a highly customizable routing service where routes may need to be added or modified dynamically based on external configurations or conditions.

Example:

```
import org.springframework.web.reactive.function.server.*;

@Configuration
public class DynamicRouterConfig {

    @Bean
    public RouterFunction<ServerResponse> dynamicRoutes(DynamicRouteService routeService) {
        return RouterFunctions.route(RequestPredicates.GET("/dynamic/{id}"), routeService::handleRequest)
            .andOther(dynamicAdditionalRoutes());
    }

    private RouterFunction<ServerResponse> dynamicAdditionalRoutes() {
        // Dynamically create routes based on some conditions
        return RouterFunctions.route(RequestPredicates.GET("/additional"), request -> 
            ServerResponse.ok().bodyValue("Additional route"));
    }
}
```
### 2. Higher Composition and Reusability
- Advantage: Functional APIs encourage higher composition and reusability of route handlers and middlewares.
- Use Case: In a microservices architecture where certain behaviors (like authentication, logging, error handling) need to be applied consistently across multiple services.

Example:

```
import org.springframework.web.reactive.function.server.*;

@Configuration
public class ComposableRouterConfig {

    @Bean
    public RouterFunction<ServerResponse> composedRoutes(UserHandler userHandler, LoggingHandler loggingHandler) {
        return RouterFunctions
            .route(RequestPredicates.GET("/users/{id}"), loggingHandler.log(userHandler::getUserById))
            .andRoute(RequestPredicates.GET("/users"), loggingHandler.log(userHandler::getAllUsers))
            .andRoute(RequestPredicates.POST("/users"), loggingHandler.log(userHandler::createUser));
    }
}

@Component
public class LoggingHandler {
    public HandlerFunction<ServerResponse> log(HandlerFunction<ServerResponse> handler) {
        return request -> {
            System.out.println("Handling request: " + request.uri());
            return handler.handle(request);
        };
    }
}
```
### 3. Enhanced Middleware Support
- Advantage: Functional APIs make it easier to implement and apply middleware for request pre-processing and post-processing.
- Use Case: Implementing cross-cutting concerns like authentication, authorization, logging, and metrics collection.

Example:

```
import org.springframework.web.reactive.function.server.*;

@Configuration
public class MiddlewareRouterConfig {

    @Bean
    public RouterFunction<ServerResponse> middlewareRoutes(AuthHandler authHandler, UserHandler userHandler) {
        return RouterFunctions
            .route(RequestPredicates.GET("/secure/users/{id}"), authHandler.auth(userHandler::getUserById))
            .andRoute(RequestPredicates.GET("/secure/users"), authHandler.auth(userHandler::getAllUsers));
    }
}

@Component
public class AuthHandler {
    public HandlerFunction<ServerResponse> auth(HandlerFunction<ServerResponse> handler) {
        return request -> {
            // Perform authentication check
            if (isAuthenticated(request)) {
                return handler.handle(request);
            } else {
                return ServerResponse.status(HttpStatus.UNAUTHORIZED).build();
            }
        };
    }

    private boolean isAuthenticated(ServerRequest request) {
        // Authentication logic
        return true; // Simplified for example purposes
    }
}
```
### 4. Functional Composition
- Advantage: Functional APIs leverage the power of functional programming, allowing for complex transformations and compositions.
- Use Case: Creating APIs that need to compose multiple data sources or processing steps dynamically.

Example:

```
import org.springframework.web.reactive.function.server.*;

@Configuration
public class FunctionalCompositionConfig {

    @Bean
    public RouterFunction<ServerResponse> composedHandlers(UserHandler userHandler, OrderHandler orderHandler) {
        return RouterFunctions
            .route(RequestPredicates.GET("/users/{id}/orders"), request -> 
                userHandler.getUserById(request.pathVariable("id"))
                    .flatMap(user -> orderHandler.getOrdersForUser(user.getId()))
                    .collectList()
                    .flatMap(orders -> ServerResponse.ok().bodyValue(orders))
            );
    }
}

@Component
public class OrderHandler {

    public Flux<Order> getOrdersForUser(String userId) {
        // Fetch orders for the user
        return Flux.just(new Order(userId, "Order1"), new Order(userId, "Order2"));
    }
}
```
### 5. Conditional Routing
Advantage: Functional APIs make it easier to define routes conditionally based on the request or application state.
Use Case: Implementing feature flags or A/B testing where the availability of routes can change dynamically.

Example:

```
import org.springframework.web.reactive.function.server.*;

@Configuration
public class ConditionalRouterConfig {

    @Bean
    public RouterFunction<ServerResponse> conditionalRoutes(FeatureToggleService featureToggleService, UserHandler userHandler) {
        return RouterFunctions.route()
            .GET("/featureX", request -> 
                featureToggleService.isFeatureXEnabled() ? userHandler.handleFeatureX(request) : ServerResponse.notFound().build()
            )
            .build();
    }
}

@Component
public class FeatureToggleService {

    public boolean isFeatureXEnabled() {
        // Determine if feature X is enabled
        return true; // Simplified for example purposes
    }
}
```
### Conclusion
> The functional API approach provides several practical advantages in real-world use cases, including dynamic routing, higher composition and reusability, enhanced middleware support, functional composition, and conditional routing. These features make the functional API more flexible and powerful for building complex, dynamic, and highly customizable reactive applications. However, it also comes with a steeper learning curve and may be more difficult to maintain for those not familiar with functional programming paradigms.
