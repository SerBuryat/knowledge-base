
In [[java]] **`Thread.class` represents OS(kernel) [[thread]]** and can be created/run: 
``` java title:example.java
...
var thread = new Thread(() -> System.out.println("Thread started..."));
thread.start();
...
```

>*java 21 inroduced [[virtual thread]]* which change a core concept of heavyweight thread in Java