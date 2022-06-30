# Spring Core
## Inversion of Control
[Inversion of control (IoC)](https://en.wikipedia.org/wiki/Inversion_of_control) is a programming _**principle**_. IoC inverts the flow of control as compared to traditional control flow. In IoC, custom-written portions of a computer program receive the control from a generic flow.

There are several basic techniques to implement IoC: [(_Illustrating images_)](https://www.tutorialsteacher.com/Content/images/ioc/ioc-patterns.png)
+ Dependency injection
+ Template method design pattern
+ ...

## Bean
## Transaction Manager
## Configuration priority
<details>
<summary>Overview</summary>

![](images/configuration_priority.png)

</details>
<details>
<summary>Explain</summary>

+ 
+ 
+ 
+ 
+ 

</details>

#### Transaction characteristics

+ Atomicity: A transaction must be a unit that should either succeed or fail
+ Consistency: 
+ Isolation:
+ Durability: 

#### How does @Transactional work ?
<details>
<summary><b><u>Propagation</u></b></summary>

+ REQUIRED: The REQUIRED propagation is default mode.
+ SUPPORTS: If a transaction exists, then the existing transaction will be used. If there isn't a transaction, it is executed non-transactional.
+ MANDATORY: If there is an active transaction, then it will be used. If there isn't an active transaction, then Spring throws an IllegalTransactionStateException exception.
+ NEVER: Spring throws an exception if there's an active transaction.
+ 
</details>

**NOTE**: `@Transactional` will have no effect if used to annotate private, protected, default methods. The proxy generator will ignore them.

## BeanFactory vs ApplicationContext

+ The `BeanFactory` provides the configuration framework and basic functionality
+ The `ApplicationContext` extends the `BeanFactory` and provides more functoions for enterprise-specific functionality.
## Understanding AOP Proxies

<details>
<summary>Understanding</summary>

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

## Other 

#### How Java-based Configuration (@Configuration) Works Internally
<details>
<summary><b><u>Expand</u></b></summary>

  ```
  @Configuration
  public class AppConfig {

      @Bean
      public ClientService clientService1() {
          ClientServiceImpl clientService = new ClientServiceImpl();
          clientService.setClientDao(clientDao()); \\ clientDao bean
          return clientService;
      }

      @Bean
      public ClientService clientService2() {
          ClientServiceImpl clientService = new ClientServiceImpl();
          clientService.setClientDao(clientDao()); \\ clientDao bean
          return clientService;
      }

      @Bean
      public ClientDao clientDao() {
          return new ClientDaoImpl();
      }
  }
  ```
  
This is where the magic comes in: <br/>
All @Configuration classes are subclassed at startup-time with `CGLIB`. The child method checks the container first for any cached (scoped) beans before it creates a new instance.

</details>

