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
