# Index

- [Intro](#intro)
- [Spring Core](#spring-core)
  - [Inversion of Control](#inversion-of-control)
  - [Dependency Injection](#dependency-injection)
  - [Bean](#bean)
  - [Autowiring](#autowiring)
  - [Dispatcher Servlet](#dispatcher-servlet)
  - [Build tools](#build-tools)
- [Spring Boot Core](#spring-boot-core)
  - [Actuator](#actuator)
  - [Aspect-Oriented Programming (AOP)](#aspect-oriented-programming-aop)


# Intro

Version: 4


# Spring Core


## Inversion of Control

It creates and manages objects (beans), injects dependencies and manages their life cycles

```java
@Service
class PaymentService {
    void pay() {
        System.out.println("Payment completed");
    }
}

@Service
class OrderService {

    @Autowired
    PaymentService paymentService;  // Spring INJECTS it

    void placeOrder() {
        paymentService.pay();
    }
}
```


## Dependency Injection

Spring gives (injects) the required objects (dependencies) into your class instead of you creating them manually with new

```java
@Service
class NotificationService {

    private final EmailService emailService;

    // Constructor Injection
    public NotificationService(EmailService emailService) {
        this.emailService = emailService;
    }

    void notifyUser() {
        emailService.sendEmail();
    }
}
```


## Bean

- object managed by the Spring IoC Container
- Spring creates, initialises, injects dependencies and destroys it when application stops

Life Cycle

- Container Started: The Spring IoC container is initialized.
- Bean Instantiated: The container creates an instance of the bean.
- Dependencies Injected: The container injects the dependencies into the bean.
- Custom init() method: If the bean implements InitializingBean or has a custom initialization method specified via @PostConstruct or init-method.
- Bean is Ready: The bean is now fully initialized and ready to be used.
- Custom utility method: This could be any custom method you have defined in your bean.
- Custom destroy() method: If the bean implements DisposableBean or has a custom destruction method specified via @PreDestroy or destroy-method, it is called when the container is shutting down.

Scopes

- Singleton: Only one instance will be created for a single bean definition per Spring IoC container, and the same object will be shared for each request made for that bean.
- Prototype: A new instance will be created for a single bean definition every time a request is made for that bean.
- Request: A new instance will be created for a single bean definition every time an HTTP request is made for that bean.
- Session: Scopes a single bean definition to the lifecycle of an HTTP Session.
- Global-Session: Scopes a single bean definition to the lifecycle of a global HTTP Session.


## Autowiring

process where Spring automatically injects the required dependency into a class

```java
@Service
class OrderService {

    @Autowired
    private PaymentService paymentService;

}
```


## Dispatcher Servlet

- Front Controller in a Spring web application
- entry point for all incoming HTTP requests
- handles incoming HTTP requests and delegates them to the appropriate components


## Build tools

Maven

- Uses a pom.xml file to define tasks and dependencies


# Spring Boot Core


## Actuator

built-in module in Spring Boot that helps you monitor, debug, and manage your application in production

- Application health
- Metrics (memory, CPU, DB connections, etc.)
- Logs
- Environment variables
- Beans
- Thread dumps
- HTTP traces
- Custom business metrics


## Aspect-Oriented Programming (AOP)

helps you write clean, modular code by separating cross-cutting concerns — logic that applies across multiple classes/methods

Example cross-cutting concerns:

- Logging
- Security checks
- Validation
- Transaction management
- Caching
- Performance monitoring
- Exception handling

Types

- @Before- run before method
- @After- run after method
- @AfterReturning- run after success
- @AfterThrowing- run after exception
- @Around- run before & after method

```java
package com.example.demo.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class FullAopExample {

    // Pointcut: any method in com.example.demo.service package
    @Pointcut("execution(* com.example.demo.service.*.*(..))")
    public void serviceLayer() {}

    // 1️⃣ BEFORE ADVICE (Runs before the method)
    @Before("serviceLayer()")
    public void beforeAdvice(JoinPoint jp) {
        System.out.println("➡️ @Before: Method = " + jp.getSignature().getName());
    }

    // 2️⃣ AFTER ADVICE (Runs always, success or failure)
    @After("serviceLayer()")
    public void afterAdvice(JoinPoint jp) {
        System.out.println("➡️ @After: Method = " + jp.getSignature().getName());
    }

    // 3️⃣ AFTER RETURNING (Only on success)
    @AfterReturning(
            pointcut = "serviceLayer()",
            returning = "result"
    )
    public void afterReturningAdvice(JoinPoint jp, Object result) {
        System.out.println("✔️ @AfterReturning: Method = " 
                           + jp.getSignature().getName() 
                           + ", Result = " + result);
    }

    // 4️⃣ AFTER THROWING (Runs only when an exception happens)
    @AfterThrowing(
            pointcut = "serviceLayer()",
            throwing = "ex"
    )
    public void afterThrowingAdvice(JoinPoint jp, Exception ex) {
        System.out.println("❌ @AfterThrowing: Method = " 
                           + jp.getSignature().getName()
                           + ", Exception = " + ex.getMessage());
    }

    // 5️⃣ AROUND ADVICE (Runs before & after, allows modifying behavior)
    @Around("serviceLayer()")
    public Object aroundAdvice(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("🔄 @Around (Before): Method = " + pjp.getSignature().getName());

        long start = System.currentTimeMillis();

        // Execute actual method
        Object result = pjp.proceed();

        long end = System.currentTimeMillis();
        System.out.println("🔄 @Around (After): Method = " + pjp.getSignature().getName()
                + ", Execution Time = " + (end - start) + " ms");

        return result;
    }
}
```
