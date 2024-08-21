`@AssertTrue` and `@AssertFalse` annotations helps to validate `Object` when there is custom logic and `@NotBlank`, `@NotEmpty` and other is not enough for field(s) validation.

`@AssertTrue` method must be `true` or `ValidationException` throws
``` java title:example.java
@AssertTrue(message = "'status' == false but must be == true")  
public boolean isStatusRequired() {  
    var status = notificationData().appointment().status();  
    return status  
}
```

`@AssertFalse` method must be `false` or `ValidationException` throws
``` java title:example.java
@AssertFalse(message = "'status' == true but must be == false")  
public boolean isStatusNotRequired() {  
    var status = notificationData().appointment().status();  
    return status;
}
```