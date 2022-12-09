---
title: "Java Generics"
date: 2022-12-09T15:32:04-04:00
draft: false
---

A **to the point** tutorial on **Generics** in Java.

# Generic Classes

Instead of having this

````java
public record AStringHolder (String content) { }
````

where I can only do this

````java
AStringHolder holde = new AStringHolder("somecontent");
````

with **generics** I can do like this

````java
public record AContentHolder<T> (T content) { }
````

now we can put anything in it

````java
AContentHolder<String> aString = new AContentHolder<>("somecontent");

AContentHolder<Integer> aInteger = new AContentHolder<>(69);

AContentHolder<Boolean> aBoolean = new AContentHolder<>(false);

AContentHolder<Double> aDouble = new AContentHolder<>(45.90);
````

# Generic Methods

Instead of having this

````java
public void log(String information) {
    System.out.println("content information: " + information);
}
````

where I can only log strings

````java
log("some content")
log(String.valueOf(69))
log(String.valueOf(false));
log(String.valueOf(45.90));
````

with **generics** I can do like this

````java
public <T> void log(T information) {
    System.out.println("content information: " + information);
}
````
now we can put anything in it

````java
log("content");
log(69);
log(false);
log(45.90);
````

# Bounds in Generics

Instead of having this

````java
public record AContentHolder<T> (T content) { }
````

where I can put anything I want

````java
AContentHolder<String> aString = new AContentHolder<>("somecontent");
AContentHolder<Integer> aInteger = new AContentHolder<>(69);
````

with **bounded generic types** we can do like this

````java
public record AContentHolder<T extends Number> (T content) { }
````

and **limit the type** that can goes inside it

````java
AContentHolder<Integer> aInteger = new AContentHolder<>(69);
AContentHolder<Double> aDouble = new AContentHolder<>(45.90);
````

## Bounds with interfaces

We can limit our type by the interface it implements

````java
public record AContentHolder<T extends Serializable> (T content) { }
````

## Bounds with class and interfaces

We can also limit by both class and any number of interfaces

````java
public record AContentHolder<T extends Number & Serializable & Comparable<?>> (T content) { }
````

# Wildcards

Instead of having this class

````java
public record AContentHolder<T extends Number> (T content) { }
````

and a method limited to print only `Double` values

````java
public void truncate(AContentHolder<Double> holder) {
    System.out.println("truncated: " + holder.content().intValue());
}
````

we can have this

````java
public void truncate(AContentHolder<?> holder) {
    System.out.println("truncated: " + holder.content().intValue());
}
````

now we can put any number in it

````java
printContent(new AContentHolder<>(45.60D));
printContent(new AContentHolder<>(39.69F));
printContent(new AContentHolder<>(22));
````