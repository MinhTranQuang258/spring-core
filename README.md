# Spring Core
### Inversion of Control

<details>
  <summary>Concepts</summary>
  </br>
  
  [Inversion of control (IoC)](https://en.wikipedia.org/wiki/Inversion_of_control) is a design principle used in software development to decouple components and increase modularity.
  
  There are several basic techniques to implement IoC: [(_Illustrating images_)](https://www.tutorialsteacher.com/Content/images/ioc/ioc-patterns.png)
  + Dependency injection
  + Template method design pattern
  + ...

  **Dependency Injection (DI)**

  DI is a specific technique for achieving IoC. It is a software design pattern that promotes loose coupling between classes by passing dependencies. It's a common and popular implementation of the IoC principle.

</details>
<details>
  <summary>Inversion of Control (IoC) vs Traditional Style</summary>
  </br>
  
  + In the traditional style of programming, a class typically creates its own dependencies. This leads to tightly coupled components and makes testing and maintenance difficult.
  + In the IoC style, dependencies are injected into a class, promoting loose coupling and better testability.

</details>
<details>
  <summary>Example</summary>
  </br>
  
  Imagine a car. In a traditional approach, the Car class is responsible for creating an instance of the Engine class, it leads to tight coupling between the `Car` and `Engine` class. But with IoC, the `Car` doesn't create the `Engine`, Car instance receives the Engine instance from an external source (like a car factory). IoC reduces coupling and increases modularity.

_Tranditional Approach_
  ```
  public class Car {
      private Engine engine;
  
      public Car() {
          engine = new Engine();
      }
  
      public void start() {
          engine.start();
      }
  }
  ```

_IoC Approach with Dependency Injection_
  ``` 
  public class Car {
      private Engine engine;
  
      public Car(Engine engine) {
          this.engine = engine;
      }
  
      public void start() {
          engine.start();
      }
  }
  ```
</details>

<details>
  <summary>Who is dependency in IoC example</summary>
  </br>
  
```
public class Car {
    private Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

Consider a `Car` class. It depends on an `Engine` to function. Therefore, the `Engine` is a dependency of the `Car`.

+ The Car class is the dependent class.
+ The Engine class is the dependency.

The key point in IoC is that the `Car` doesn't create the `Engine` itself; it's provided (_injected_) from an external source.

</details>

### Spring Boot Layers
<details>
  <summary>Concepts</summary>
  </br>
  
  1. Presentation Layer – Authentication & Json Translation
  2. Business Layer – Business Logic, Validation & Authorization
  3. Persistence Layer – Storage Logic
  4. Database Layer – Actual Database

</details>

### Bean

<details>
  <summary>The lifecyle of bean</summary>
  </br>

  + Spring creates a new instance of the bean class using the constructor.
  + Spring injects the required dependencies into the bean using dependency injection techniques.
  + If the bean has a `@PostConstruct` annotation, the annotated method is called.
  + The bean is now ready to be used
  + when bean is destroy, If the bean has a @PreDestroy annotation, the annotated method is called.
  
</details>

<details>
  <summary>Type of beans</summary>
  </br>

  + **Singleton:** A single instance of the bean is created and shared across the entire application.
  + **Prototype:** A new instance of the bean is created every time it is requested.
  + **Request:** A single instance of the bean is created and available for each HTTP request.
  + **Session:** A single instance of the bean is created and available for each HTTP session.
  + **Application:** A single instance of the bean is created and shared across the entire ServletContext.
  + **WebSocket:** A single instance of the bean is created and available for each WebSocket session.

</details>
<details>
  <summary>Singleton vs Prototype scope</summary>
  </br>

  **Singleton:**
  
  + A singleton-scoped bean is instantiated only once per Spring IoC container. All requests for that bean will return the same instance.
  
  ```
  @Configuration
  public class AppConfig {
      @Bean
      @Scope("singleton")
      public MyBean myBean() {
          return new MyBean();
      }
  }
  ```
  _Setup singleton bean_

  ```
  ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
  MyBean bean1 = context.getBean(MyBean.class);
  MyBean bean2 = context.getBean(MyBean.class);
  
  System.out.println(bean1 == bean2); // Output: true
  ```

  **Prototype:**
  
  + A prototype-scoped bean means that a new instance of the bean is created every time it is requested from the Spring container.

  ```
  @Configuration
  public class AppConfig {
      @Bean
      @Scope("prototype")
      public MyBean myBean() {
          return new MyBean();
      }
  }
  ```

  ```
  ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
  MyBean bean1 = context.getBean(MyBean.class);
  MyBean bean2 = context.getBean(MyBean.class);
  
  System.out.println(bean1 == bean2); // Output: false
  ```

  _The scoped bean injection problem._

  By default, Spring beans are singletons. The problem arises when we try inject beans of different scopes. For example, a prototype bean into a singleton.

  
</details>
<details>
  <summary>The scoped bean injection problem</summary>
  </br>
  By default, Spring beans are singletons. The problem arises when we try inject beans of different scopes. For example, a prototype bean into a singleton.
  
  ```
  @Configuration
  public class AppConfig {
  
      @Bean
      @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
      public PrototypeBean prototypeBean() {
          return new PrototypeBean();
      }
  
      @Bean
      public SingletonBean singletonBean() {
          return new SingletonBean();
      }
  }
  ```

  ```
  public class SingletonBean {

    @Autowired
    private PrototypeBean prototypeBean; //The bean is initialized only once. 

    ...
  }
  ```

</details>
<details>
  <summary>Singleton scope vs pattern</summary>
  </br>

  + **Singleton pattern:** There is only once instance of Singleton pattern on the JVM.
  + **Singleton scope:** The Singleton scope only uniquie on the bean name.

</details>

### Transaction Manager
<details>
  <summary>Propagation</summary>
  </br>
  
+ REQUIRED: The REQUIRED propagation is default mode.
+ SUPPORTS: If a transaction exists, then the existing transaction will be used. If there isn't a transaction, it is executed non-transactional.
+ MANDATORY: If there is an active transaction, then it will be used. If there isn't an active transaction, then Spring throws an IllegalTransactionStateException exception.
+ NEVER: Spring throws an exception if there's an active transaction.

**Note**: `@Transactional` will have no effect if used to annotate private, protected, default methods. The proxy generator will ignore them.
</details>
<details>
  <summary>How @Transactional work?</summary>
  </br>

  + You can place the `@Transactional` annotation on a class or method. When applied at the class level, it applies to all public methods of the class.
  + When a method annotated with `@Transactional` is called, Spring creates a proxy that wraps the method call. This proxy manages the transaction lifecycle, including starting the transaction before the method execution and committing or rolling back the transaction after the method execution.
  + By default, transactions are rolled back on unchecked exceptions (subclasses of `RuntimeException`) and errors. However, you can specify that a transaction should roll back on checked exceptions.
  + To enable `@Transactional` support, you need to configure `@EnableTransactionManagement`.

  ```
  @Transactional(rollbackFor = {SQLException.class, IOException.class})
  public void myMethod() throws SQLException, IOException {
      // business logic that might throw these checked exceptions
  }
  ```
</details>

### Validation

<details>
  <summary>Common Validation Annotations</summary>
  </br>

  + `@NotNull`: Ensures that the annotated field is not null.
  + `@NotEmpty`: Ensures that the annotated collection, map, or array is not empty.
  + `@NotBlank`: Ensures that the annotated string is not null and the trimmed length is greater than zero.
  + `@Size:`: Validates that the annotated element’s size is within the specified boundaries.
  + `@Min:`: Ensures that the annotated element is a number and its value is greater than or equal to the specified minimum.
  + `@Max:`: Ensures that the annotated element is a number and its value is less than or equal to the specified maximum.
  + `@Pattern:`: Ensures that the annotated string matches the specified regular expression.
  + `@Email:`: Validates that the annotated string is a valid email address.
  + `@Past:`: Ensures that the annotated date is in the past.
  + `@Future:`: Ensures that the annotated date is in the future.
</details>
<details>
  <summary>Custom Validation Annotations</summary>
  </br>

  ```
  @Documented
  @Target(ElementType.FIELD)
  @Retention(RetentionPolicy.RUNTIME)
  @Constraint(validatedBy = NumberValidation.class)
  public @interface IsNumber {
  
    String message() default "Invalid number";
    Class<?>[] groups() default {};  // Include this line
    Class<? extends Payload>[] payload() default {};  // Include this line
  }
  ```
  _Besides, the `message` attribute, the custom annotation also must have 2 attributes (`groups`, `payload`)._
  ```
  public class NumberValidation implements ConstraintValidator<IsNumber, String> {

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
      return NumberUtils.isParsable(value);
    }
  }
  ```

  ```
  @Data
  public class GoldRequest {
    @IsNumber
    private String value;
  }
  ```
  ```
  @RestController
  public class GoldController {
  
    @PostMapping("/test")
    public void test(@RequestBody @Valid GoldRequest goldRequest) {
      // do something
    }
  }
  ```
</details>
<details>
  <summary>@Valid vs @Validated</summary>
  </br>

  `@Valid`: Typically used to validate request bodies.
  ```
  public class User {
    @NotNull
    @Size(min = 2, max = 30)
    private String name;

    @NotNull
    @Email
    private String email;

    // Getters and setters
  }
  ```
  ```
  @RestController
  @RequestMapping("/api/users")
  public class UserController {
  
      @PostMapping
      public ResponseEntity<User> createUser(@RequestBody @Valid User user) {
          // Business logic to create a user
          return ResponseEntity.ok(user);
      }
  }
  ```

  `@Validated`: Often used to validation difference groups based on business.
  ```
  public class User {
    @NotNull(groups = BasicInfo.class)
    @Size(min = 2, max = 30, groups = BasicInfo.class)
    private String name;

    @NotNull(groups = BasicInfo.class)
    @Email(groups = BasicInfo.class)
    private String email;

    @NotNull(groups = AdvancedInfo.class)
    @Min(value = 18, groups = AdvancedInfo.class)
    private Integer age;

    // Getters and setters
  }
  ```
  ```
  @Service
  @Validated
  public class UserService {
  
      public User createUser(@Validated(BasicInfo.class) User user) {
          // Business logic to create a user
          return user;
      }
  
      public User updateUser(@Validated(AdvancedInfo.class) User user) {
          // Business logic to update a user
          return user;
      }
  }
  ```
In this example, the @Validated annotation is used to validate the User object with specific validation groups (BasicInfo and AdvancedInfo).
</details>

### Spring event

<details>
  <summary>Types of Events</summary>

  + **Built-in Events:** Spring provides several built-in events such as `ContextRefreshedEvent`, `ContextStartedEvent`, `ContextStoppedEvent`, and `ContextClosedEvent`.
  + **Custom Events:** You can create your own custom events by extending the ApplicationEvent class (for versions before Spring 4.2) or simply using any object as an event (from Spring 4.2 onwards).

</details>
<details>
  <summary>Using Spring Events</summary>
  
  ```
  import org.springframework.context.ApplicationEvent;

  public class CustomEvent extends ApplicationEvent {
      private String message;
  
      public CustomEvent(Object source, String message) {
          super(source);
          this.message = message;
      }
  
      public String getMessage() {
          return message;
      }
  }
  ```
  ```
  import org.springframework.context.event.EventListener;
  import org.springframework.stereotype.Component;
  
  @Component
  public class CustomEventListener {
      @EventListener
      public void handleCustomEvent(CustomEvent event) {
          System.out.println("Received custom event - " + event.getMessage());
      }
  }
  ```
</details>
<details>
  <summary>Sync & Async in Spring event</summary>

  **Synchronous Events: **
  By default, Spring events are handled synchronously. This means that when an event is published, the publisher thread waits for all event listeners to process the event before continuing.
  
  **Asynchronous Events: **
  To handle events asynchronously, you can use the `@Async` annotation. This allows the event listener to process the event in a separate thread, freeing up the publisher thread to continue its work without waiting for the listeners to complete.

  ```
  @Configuration
  @EnableAsync
  public class AsyncConfig {
      // Configuration details
  }
  ```
  ```
  @Component
  public class AsyncEventListener {
      @EventListener
      @Async
      public void handleEvent(CustomEvent event) {
          System.out.println("Handling event asynchronously: " + event.getMessage());
      }
  }
  ```
</details>
<details>
  <summary>Exmaple withh Bootstrap phases</summary>
  
  ![](images/bootstrap.png)

</details>


### Configuration precedence
<details>
  <summary>The configuration precedence</summary>
  </br>
  
  ![](images/configuration_priority.png)
  
  + <b>{specific-location}(s)</b>/<b>{application-name}-{profiles}(s).properties</b>
  + {specific-location}(s)/{application-name}-{profiles}(s).yml
  + {specific-location}(s)/{application-name}-{profiles}(s).yaml
  + {specific-location}(s)/application-{profiles}(s).properties
  + ...
  + file:./config/{application-name}-{profiles}(s).properties
  + ...
  + file:./config/application-{profiles}(s).properties
  + ...
  + {specific-location}(s)/{application-name}.properties

  Ref: https://stackoverflow.com/questions/65286686/difference-between-classpath-some-packages-vs-file-some-url-when-configuring-s

</details>

### Handle exceptions in Spring

<details>
  <summary>Using @ControllerAdvice</summary>
  </br>

  **Create the Global Exception Handler:**

  ```
  @ControllerAdvice
  public class GlobalExceptionHandler {
  
      @ExceptionHandler(MyCustomException.class)
      public ResponseEntity<String> handleMyCustomException(MyCustomException ex) {
          return new ResponseEntity<>(ex.getMessage(), HttpStatus.BAD_REQUEST);
      }
  
      @ExceptionHandler(Exception.class)
      public ResponseEntity<String> handleGeneralException(Exception ex) {
          return new ResponseEntity<>("An error occurred", HttpStatus.INTERNAL_SERVER_ERROR);
      }
  }
  ```

  If have any `MyCustomException` or `Exception` are thrown by application. They will be caught by `GlobalExceptionHandler` to handle exception.

</details>
<details>
  <summary>Using @ExceptionHandler</summary>
  </br>
  
  Assume, we want to handle a custom exception called `UserNotFoundException`.
  
  ```
  @RestController
  @RequestMapping("/users")
  public class UserController {
  
      @GetMapping("/{id}")
      public ResponseEntity<User> getUserById(@PathVariable Long id) {
          User user = findUserById(id);
          if (user == null) {
              throw new UserNotFoundException("User not found with id: " + id);
          }
          return new ResponseEntity<>(user, HttpStatus.OK);
      }
  
      // Simulate a method to find a user by ID
      private User findUserById(Long id) {
          // Logic to find user by ID
          return null; // For demonstration, always return null
      }
  
      @ExceptionHandler(UserNotFoundException.class)
      public ResponseEntity<String> handleUserNotFoundException(UserNotFoundException ex) {
          return new ResponseEntity<>(ex.getMessage(), HttpStatus.NOT_FOUND);
      }
  }
  ```
</details>
<details>
  <summary>@ExceptionHandler vs @ControllerAdvice</summary>
  </br>

   `@ExceptionHandler`: 
   + Handles exceptions within a specific controller.
   + More suitable for handling exceptions specific to a single controller.

  `@ExceptionHandler`: 
  + Handles exceptions globally across all controllers.
  + Handles common exceptions that can occur in multiple controllers.
  
</details>
<details>
  <summary>Handle circular dependencies exception</summary>
  </br>

  A circular dependency occurs when two or more components depend on each other directly or indirectly, creating a loop. 

  _Example:_ Consider two Spring beans, `BeanA` and `BeanB`, where `BeanA` depends on `BeanB` and `BeanB` depends on `BeanA`

  **Solutions:**
  + **Redesign:** Often, circular dependencies indicate a design flaw.
  + **Setter Injection:** Use setter injection instead of constructor injection to break the cycle.
  + **`@Lazy` Annotation:** Use the `@Lazy` annotation to delay the initialization of one of the beans.

</details>

### Asynchronous processing

<details>
  <summary>How @Async work</summary>
  </br>

  When you annotate a method with @Async, Spring Boot creates a proxy around the method. When an @Async method is called, the caller does not wait for the method to complete. Instead, the method runs asynchronously in the background. By default, Spring Boot uses a `SimpleAsyncTaskExecutor`, but you can configure a custom `TaskExecutor` to manage the threads. 

</details>
<details>
  <summary>Thread pool in @Async</summary>
  </br>

  By default, Spring uses a simple thread pool configuration for methods annotated with @Async. If you don’t provide a custom `TaskExecutor`, Spring will use a `SimpleAsyncTaskExecutor`.
  + **SimpleAsyncTaskExecutor -** This executor does not reuse threads and creates a new thread for each task. It is suitable for simple use cases and testing but not recommended for production.
  + **ThreadPoolTaskExecutor -** If you define a `ThreadPoolTaskExecutor` bean, Spring will use it instead of the default.

  ```
  @Configuration
  @EnableAsync
  public class AsyncConfig {
  
      @Bean(name = "asyncExecutor")
      public Executor asyncExecutor() {
          ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
          executor.setCorePoolSize(5);
          executor.setMaxPoolSize(10);
          executor.setQueueCapacity(500);
          executor.setThreadNamePrefix("AsyncThread-");
          executor.initialize();
          return executor;
      }
  }
  ```
</details>

<details>
  <summary>Return types in @Async</summary>
  </br>

  + Void: For methods that do not return a value, simply annotate the method with `@Async`.
  + Return Type: If you need to return a result from an asynchronous method, you can use `Future`, `ListenableFuture` or `CompletableFuture`.

  _Example:_

  ```
    @Async
    public CompletableFuture<String> asyncMethodWithCompletableFuture() {
        return CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                // Handle exception
            }
            return "Hello World!";
        });
    }
  ```

  ```
  public void testAsyncMethod() throws InterruptedException, ExecutionException {
    Future<String> future = asyncMethodWithCompletableFuture();

    // Do something else while the async method is running

    String result = future.get(); // This will block until the result is available
    System.out.println("Result from async method: " + result);
  }
  ```
  _Handling the result_

</details>

### BeanFactory vs ApplicationContext

+ The `BeanFactory` provides the configuration framework and basic functionality
+ The `ApplicationContext` extends the `BeanFactory` and provides more functoions for enterprise-specific functionality.
### Understanding AOP Proxies

<details>
  <summary>Understanding</summary>
  </br>
  Let's come up with a sample to clearly understand what a the AOP proxies is
  
  Consider first the scenario have a un-proxied, nothing-special-about-it, straight object reference:
  
  ```
  public class SimplePojo implements Pojo {

     public void foo() {
        // this next method invocation is a direct
        call on the 'this' reference
        this.bar();
     }

     public void bar() {
        // some logic...
     }
  }
  ```
  ```
  public class Main {

     public static void main(String[] args) {

        Pojo pojo = new SimplePojo();

        // this is a direct method call on the 'pojo' reference
        pojo.foo();
     }
  }
  ```
  When the reference (`pojo`) that client code has is a proxy
  ```
  public class Main {

     public static void main(String[] args) {

        ProxyFactory factory = new ProxyFactory(new SimplePojo());
        factory.addInterface(Pojo.class);
        factory.addAdvice(new RetryAdvice());

        Pojo pojo = (Pojo) factory.getProxy();

        // this is a method call on the proxy!
        pojo.foo();
     }
  }
  ```
  
  The key thing to understand here is the `pojo` is a proxy instance, not a Pojo object. So when `pojo` invoke the `foo()` method, the proxy will be able to delegate to all of the interceptors (advice) that are relevant to that particular method call. 
  
  Interceptors may be used to log, do actions before and after the target method (`foo()`).
  
  However, once the call has finally reached the target object, the `SimplePojo` reference in this case, any method calls `this.` such as `this.bar()` or `this.foo()`,  are going to be invoked against the `this` reference, and not the _proxy_. In other word, in this case the `pojo` instance (`SimplePojo`) is being used, not a `pojo` proxy.
  
  Ref: https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/aop.html
  
  Ref: https://jenkov.com/tutorials/java-reflection/dynamic-proxies.html
</details>

### Spring Actuator
<details>
  <summary>Health Checks</summary>
  </br>

  + **Enable Health Endpoint:** `management.endpoints.web.exposure.include=health`
  + **Custom Health Indicators:** You can create custom health indicators by implementing the `HealthIndicator` interface.

  ```
  @Component
  public class CustomHealthIndicator implements HealthIndicator {
      @Override
      public Health health() {
          // Custom logic to determine health status
          boolean healthy = checkHealth();
          if (healthy) {
              return Health.up().withDetail("Custom Health", "All systems go!").build();
          } else {
              return Health.down().withDetail("Custom Health", "Something went wrong!").build();
          }
      }
  
      private boolean checkHealth() {
          // Custom health check logic
          return true;
      }
  }
  ```
  
</details>
<details>
  <summary>Metrics</summary>
  </br>

  + **Configure Metrics Exposure:** `management.endpoints.web.exposure.include=health,info,metrics,prometheus`
  + **Access Metrics:** Once your application is running, you can access the metrics at `http://localhost:8080/actuator/metrics` for general metrics and `http://localhost:8080/actuator/prometheus` for Prometheus-specific metrics.
  
</details>
<details>
  <summary>Environment Information</summary>
  </br>

  + **Configure Endpoints:** By default, the environment endpoint (`/actuator/env`) is not exposed. You need to configure `management.endpoints.web.exposure.include=env`.
  + **Security Considerations:** `management.endpoint.env.show-values=WHEN_AUTHORIZED`
  
</details>
<details>
  <summary>Loggers</summary>
  </br>

  To configure loggers at runtime in Spring Boot Actuator
  + **Enable the Loggers Endpoint:**
  ```
  management.endpoints.web.exposure.include=loggers
  management.endpoint.loggers.enabled=true
  ```
  + **Access and Configure Loggers:**
    + **View Log Levels:** To view the current log levels, you can make a `GET` request to `http://localhost:8080/actuator/loggers`. This will return a list of all loggers and their current levels.
    + **Change Log Levels:** To change the log level of a specific logger, you can make a `POST` request to `http://localhost:8080/actuator/loggers/{loggerName}` with a JSON payload specifying the new level.
  
</details>
