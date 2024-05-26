---
title: "Spring Basics Part II: Adding Beans to Spring Context"
date: 2024-05-23T16:29:45-04:00
description: ""
tags: [Spring, Java, Backend, System Design]
featured_image: "/img/spring.jpg"
images: []
categories: Spring
comment: true
draft: false
---

## Introduction

The **_context_** is like a place in the memory of the Spring app in which all object instances are added and managed. Spring uses the instances in the context to connect the app to various functionalities.

By default, the Spring doesn't know any of the instances defined in the app. And the Spring context is initially empty. These instances should be added to the context to enable Spring to see them. 

These object instances are named **_"beans"_**.

In a real-world app, not every object will be added to the Spring context, but only the instances that we expect Spring to manage.

&nbsp;

## Add Beans to Spring Context

&nbsp;

#### Create a Spring context

**_spring-context_** dependency should be added to the project to enable the context functionality.

```xml
<dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-context</artifactId> 
    <version>5.2.6.RELEASE</version> 
</dependency>
```

Then we can create an instance of the Spring context:

```java
@Getter
@Setter
public class Cat { 
    private String name;
}

public class Main {
    public static void main(String[] args) {
        var context = new AnnotationConfigApplicationContext();
    }
}
```

We use the `AnnotationConfigApplicationContext` class to create the Spring context instance, which is the implementation that uses **_annotations_**, the most used approach today.

Beans can be added into the context in three ways:
- Using the @Bean annotation 
- Using stereotype annotations 
- Programmatically

&nbsp;

#### Using the `@Bean` annotation

The steps that add a bean to the Spring context using the `@Bean` annotation are:

1. Define a configuration class for the project. A Spring configuration class is characterized by the fact that it is annotated with the `@Configuration` annotation.

```java
@Configuration
public class ProjectConfig {}
```

2. Add a method to the configuration class that returns the object instance we want to add to the context, and annotate the method with the `@Bean` annotation. This annotation instructs Spring to call this method when at context initialization and add the returned value to the context. The method’s name also becomes the bean’s name, which means it should be a noun rather than a verb by convention. Most often they have the same name as the class.

```java
@Configuration
public class ProjectConfig {
    @Bean
    Cat cat() { 
        var c = new Cat(); 
        c.setName("Koko"); 
        return c; 
    }
}
```

3. Make Spring use the configuration class defined. When creating the Spring context instance, send the configuration class as a parameter to instruct Spring to use it.

```java
public class Main {
    public static void main(String[] args) {
        var context =
                new AnnotationConfigApplicationContext(ProjectConfig.class);
    }
}
```

We can verify the `Cat` instance is indeed part of the context now by `getBean` method. This method gets a reference of a bean from the context without need to do any explicit casting. If a bean does not exist, Spring will throw an exception.

```java
public class Main {
    public static void main(String[] args) {
        var context =
                new AnnotationConfigApplicationContext(ProjectConfig.class);
        
        Cat c = context.getBean(Cat.class);
        System.out.println(c.getName());
    }
}
```

Multiple beans of the same type can be added to the Spring context by using multiple methods annotated with `@Bean`. Each instance will have a unique identifier, which is used to refer to them correctly.

```java
@Configuration
public class ProjectConfig {
    @Bean
    Cat cat1() { 
        var c = new Cat(); 
        c.setName("Koko"); 
        return c; 
    }

    @Bean
    Cat cat2() {
        var c = new Cat();
        c.setName("Riki");
        return c;
    }

    @Bean
    Cat cat3() {
        var c = new Cat();
        c.setName("Miki");
        return c;
    }
}
```

In such case, `getBean(Cat.class)` will not get the beans from the context because Spring cannot guess which instance is referred to. We need to refer precisely to one of the instances by the bean's name. The first parameter of `getBean()` is now the name of the instance.

```java
public class Main {
    public static void main(String[] args) {
        var context =
                new AnnotationConfigApplicationContext(ProjectConfig.class);
        
        Cat c = context.getBean("cat2", Cat.class);
        System.out.println(c.getName());
    }
}
```

By default, the bean's name is the same as the method's name, but we can also give another name to the bean:

```java
@Bean(name = "miki") 
@Bean(value = "miki") 
@Bean("miki")
```

We can also define one bean as **_primary_** when we have multiple beans of the same class in the Spring context:

```java
@Bean
@Primary
Cat cat1() { 
    var c = new Cat(); 
    c.setName("Koko"); 
    return c; 
}
```

&nbsp;

#### Using stereotype annotations

Stereotype annotations can be added above the class for which we need to have an instance in the Spring context. The most basic one of steretype annotations is `@Component`. When the app creates a Spring context, Spring creates instances of the classes that marked as a component and adds instances of them to the context.

The steps that add a bean to the Spring context using the stereotype annotations are:

1. Mark the classes with `@Component` annotation for which we want Spring to add an instance to its context.

```java
@Getter
@Setter
@Component 
public class Cat { 
    private String name;
}
```

2. Add `@ComponentScan` annotation over the configuration class. By default, Spring doesn't search for classes annotated with stereotype annotations. With the `@ComponentScan` annotation, we tell Spring where to look for these classes by enumerating the packages.

```java
@Configuration 
@ComponentScan(basePackages = "main") 
public class ProjectConfig {}
```

We can continue use `getBean` to verify the instance of class `Cat` has been added as a bean in the context. Notice in this case, cat's name would be `null`, because Spring just creates the instance of the class, but it's our duty if we want to change the instance. 

```java
public class Main {
    public static void main(String[] args) {
        var context =
                new AnnotationConfigApplicationContext(ProjectConfig.class);
        
        Cat c = context.getBean(Cat.class);
        System.out.println(c.getName());
    }
}
```

Let's compare the advantages and disadvantages of the two ways to add beans the Spring context:

1. With `@Bean`, We have full control over the instance  annotation, which means we can create and config the instance and just hand it to the Spring. While with stereotype annotations, we only have control over the instance after it has been created by the Spring.
> Sometimes we want to execute some instructions after the creation of the instance with stereotype annotations, we can add `@PostConstruct` to a method defined in the component class. This method would be called after the Spring executes the constructor and creates the instance.

2. With `@Bean`, multiple instances of the same class can be created and added to the context. While with stereotype annotations, only one instance of one class will be added to the context.

3. `@Bean` can be used to add any instances to the context, even the class of the instance are outside the app (like classes from external libraries or built-in libraries). While stereotype annotations can only be used to classes that the application owns.

4. With `@Bean`, separate methods for beans should be written, while stereotype annotations do not add extra boilerplate code to the app.

In real-world scenarios, stereotype annotations are used much more than the `@Bean` annotation due to the less code writing. 

&nbsp;

#### Programmatically

Sometimes, we want to implement a custom logic of adding beans to the context and the `@Bean` or the stereotype annotations are not enough for our needs. `registerBean()` method of the `ApplicationContext` instance can be used to add a bean to the Spring context using a programmatic approach. 

```java
if (condition) {
    registerBean(b1);
} else {
    registerBean(b2);
}
```

`registerBean` has four parameters:

```java
<T> void registerBean(
        String beanName,  // name for the bean
        Class<T> beanClass,  // class of the bean
        Supplier<T> supplier,  // supplier returning the bean instance
        BeanDefinitionCustomizer... customizers);  // config bean characteristics, can be omitted
```

Here is an example using the `registerBean()` to add a bean to the Spring context:

```java
public class Main {
    public static void main(String[] args) {
        var context =
                new AnnotationConfigApplicationContext(ProjectConfig.class);
        
        Cat c = new Cat();
        c.setName("Kiki");
        
        Supplier<Cat> catSupplier = () -> c;
        
        context.registerBean(
                "cat", Cat.class, catSupplier
        );
```

&nbsp;

## Summary 

Adding object instance (beans) to the Spring context is the first step to learn Spring. Spring can only see the instances added to its context.

Beans can be added to the context in 3 ways: `@Bean` annotation, stereotype annotations, and programmatically.
- `@Bean` annotation can enable us to create multiple instances of the same class, and added them through customized methods, which is more flexible, yet it requires extra code writing.
- Stereotype annotations requires less code writing, but it only creates one bean for one internal class of the application.
- `registerBean()` can implement custom logic for adding beans.
