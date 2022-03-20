# Spring Core
## 1. Inversion of Control
[Inversion of control (IoC)](https://en.wikipedia.org/wiki/Inversion_of_control) is a programming _**principle**_. IoC inverts the flow of control as compared to traditional control flow. In IoC, custom-written portions of a computer program receive the control from a generic flow.

There are several basic techniques to implement IoC: [(_Illustrating images_)](https://www.tutorialsteacher.com/Content/images/ioc/ioc-patterns.png)
+ Dependency injection
+ Template method design pattern
+ ...

## 2. Bean
## 3. Transaction Manager

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

## 4. BeanFactory vs ApplicationContext

+ The `BeanFactory` provides the configuration framework and basic functionality
+ The `ApplicationContext` extends the `BeanFactory` and provides more functoions for enterprise-specific functionality.

## 5. Other 

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
  
This is where the magic comes in: 
All @Configuration classes are subclassed at startup-time with `CGLIB`. the child method checks the container first for any cached (scoped) beans before it creates a new instance.

</details>

