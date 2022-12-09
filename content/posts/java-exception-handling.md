---
title: "Java Exception Handling"
date: 2022-12-09T15:32:04-04:00
draft: false
---

A **to the point** tutorial on **Exception Handling** in Java.

# Checked Exceptions

With the code below

````java
Files.writeString(Paths.get("file.txt"), "content");
````

we get the following compile error

````java
Unhandled exception: java.io.IOException
````

because the method signature of `writeString` method defines a `throws` clause,

````java
public static Path writeString(...) throws IOException
````

with an `IOException` that extends `Exception`.

````java
public class IOException extends Exception
````

When this happens we are dealing with a `checked exception`, that means, a problem that needs to be handled at compile time with a `try-catch` block.

````java
try {
    Files.writeString(Paths.get("file.txt"), "content");
} catch (IOException exception) {
    exception.printStackTrace();
}
````
Then the error will be gone.

# Unchecked Exceptions

The following code 

````java
String[] strings = new String[10];
System.out.println(strings[10]);
````

does not generates any compile error, but when it runs, it throws an error.

````java
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException
````

This happens because we are trying to access an index that does not exists in the array and the error thrown extends from `RuntimeException`.

````java
public class IndexOutOfBoundsException extends RuntimeException
````

Any exception extending from `RuntimeException` will not generate any error during compile time, but it will at run time. We can still catch the exception to prevent the application from stopping.

````java
try {
    String[] strings = new String[10];
    System.out.println(strings[10]);    
} catch (IndexOutOfBoundsException exception) {
    exception.printStackTrace();
}
````

# Custom Exceptions

Any class you create extending from either `Exception` or `RuntimeException` is a custom exception

````java
public class CustomerNotFoundException extends RuntimeException {

    public CustomerNotFoundException(int id) {
        super("Customer with id " + id + " not found");
    }

}
````

or

````java
public class FailedToReadConfigurationException extends Exception {

    public FailedToReadConfigurationException(Throwable cause) {
        super("Was not possible to read configuration file", cause);
    }

}
````

## Choosing checked or unchecked

`Checked` exceptions are usefull when you know that a problem can happen in a method call and you want to enforce the exception handling in that case. In this case you know of a possible way to keep the application running even in this error state.

`Uncheked` exceptions are usefull when you really can't do anything about this error state so you just throw the exception and finish the application. Most of the time you will also catch this kind of exception, but wouldn't proceed with execution, probably will just print an error message to the user.

Checkout [Philipp Hauer's take on the subject](https://phauer.com/2015/checked-exceptions-are-evil/), it might give some extra ideas to you. This is a subjective topic, it does not has any concrete answers. It really depends on the case.

# Exception Rethrowing

Instead of having this

````java
public void causeError() {
    try {
        Files.writeString(Paths.get("file.txt"), "content");
    } catch (IOException exception) {
        exception.printStackTrace();
    }
}
````

we can have this

````java
public void causeError() throws IOException {
    Files.writeString(Paths.get("file.txt"), "content");
}
````

to deal with checked exceptions without using a `try-catch` block. But now the method that calls this one will have to deal with the exception or retrhow it.

````java
try {
    causeError();
} catch (IOException exception) {
    //do something
}
````

This is usefull when you need to get an exception from a low level layer like in a Repository to a more high level layer like in a Presenter or GUI.

# Finally block

This block is used to run a piece of code regardless if the try or catch blocks run. The finally block will always run in the end.

````java
try {
    Files.writeString(Paths.get("file.txt"), "content");
} catch (IOException exception) {
    exception.printStackTrace();
} finally {
    System.out.println("Content saved finished execution");
}
````

# Try-with-resources

Instead of doing this to deal with resources that needs to be closed

````java
FileReader reader = null;
try {
    reader = new FileReader(new File("file.txt"));
} catch (FileNotFoundException exception) {
    //deal with exception
} finally {
    try {
        reader.close();
    } catch (IOException | NullPointerException exception) {
        //deal with exception
    }
}
````

we can do like this

````java
try (FileReader reader = new FileReader(new File("file.txt"))) {
    //do something with reader
} catch (IOException exception) {
    //deal with exception
}
````

note that in the first case the `close` method needs to be called in the end in case the resource was opened and it can also throws an exception. With the `try-with-resources` block, we remove the boilerplate and make the `reader` variable available only in the `try` block so when this scope ends, it will get automatically closed.