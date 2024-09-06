**[[continuation]]-passing style (CPS) is a programming model** used to **manage control flow** in software applications. It provides a **way to handle *asynchronous* or *callback*-based programming** by explicitly **passing control from one function to another**.

CPS is a technique of code execution model in which **functions receive arguments representing their future behavior** - **these arguments are called *continuations* (*callback* or "rest of the computation")**.

The simplest implementation of CPS is passing *callback* function.

Example of *async* implementation via *callback* in java:
``` java title:CpsViaCallbacks.java
import java.util.function.Predicate;  
  
public class CpsViaCallbacks {  
  
    public static void main(String[] args) throws InterruptedException {  
        
        Runnable successCallback = () -> {  
            System.out.println("Success!");  
        };  
        
        
        Runnable errorCallback = () -> {  
            System.out.println("Error...");  
        };  
  
        var asyncJob = new Thread(() -> {  
            System.out.println("Starting `doCpsJob()`...");  
            try {  
                longRunningJob(  
                        string -> string.equals("correct argument"),  
                        successCallback,  
                        errorCallback  
                );  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
            System.out.println(" `doCpsJob()` is finished!");  
        });  

		// run async without blocking `main thread`
        asyncJob.start();  
  
        // code below don't blocked by `asyncJob`
        System.out.println("Running code which doesn't block by `asyncJob`");  
        Thread.sleep(1000);  
        System.out.println("Main thread logic done!");  
    }  
  
    public static void longRunningJob(Predicate<String> check, 
									  Runnable doOnSuccess, 
									  Runnable doOnError)  
										  throws InterruptedException {  
  
        // Here is the logic to get `somePreviousLogicResult`  
        Thread.sleep(5000);  
        var somePreviousLogicResult = "incorrect arg";  
  
        if(check.test(somePreviousLogicResult)) {  
            doOnError.run();  
        } else {  
            doOnSuccess.run();  
        }  
    }  
  
}
```