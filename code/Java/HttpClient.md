New `java.net.http.HttpClient` since Java 11.

> [HTTP client API that implements HTTP/2 and Web Socket](https://www.baeldung.com/java-9-http-client)

Has blocking request `HttpClient.send()` and non-blocking request `HttpClient.sendAsync()`.

> *Under the hood `HttpClient` use `java.nio.channels.Selector` for non-blocking requests and `java.net.http.HttpClientImpl.SelectorManager` as a main loop*
> ![[HttpClientImpl.SelectorManager.png]]

``` java title:send-sync
private static void requestSync() throws IOException, InterruptedException {  
    var resp = HttpClient.newHttpClient()  
            .send(  
                    HttpRequest.newBuilder()  
                            .uri(URI.create(REQUEST_URL))  
                            .GET()  
                            .build(),  
                    BodyHandlers.ofString()  
            )  
            .body();  
    System.out.println("Sync response: " + resp);  
}
```

``` java title:send-async
private static void requestAsync() {  
    HttpClient.newHttpClient()  
            .sendAsync(  
                    HttpRequest.newBuilder()  
                            .uri(URI.create(REQUEST_URL))  
                            .GET()  
                            .build(),  
                    BodyHandlers.ofString()  
            )  
            .thenAccept(resp -> {  
                System.out.printf(  
                        "Async response (Thread: %s): %s",  
                        Thread.currentThread().getName(),  
                        resp.body()  
                );  
                System.out.println();  
            });  
}
```
>*If `requestAsync()` just run in `main()` thread it will print nothing cause `thenAccept()` non-blocking and `main()` doesnt wait it.*
>*For testing add `Thread.sleep(...)` for `main()` thread waiting for answer*