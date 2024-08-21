*Coroutines* are computer program components that allow **==execution to be suspended and resumed==**.
> *Java [[virtual thread]] has* `jdk.internal.vm.Continuation` *class to implement this behavior*

> *functions whose execution you can pause*

Implement **==cooperative multitasking==** model. ([[thread#Thread models]])

*Coroutine* has ==**internal mechanism of suspending/rerunning**== code. (except of [[fiber]] which has some scheduler)

>*when the invoker calls a coroutine, control immediately transfers into that coroutine; 
>when the coroutine yields/suspend, control immediately returns to its caller*
```
struct Caller(coroutine) {
	func todo() {
		coroutine.run(); // -> "Before yield!"
		coroutine.run(); // -> "After yield!"
	}
}

struct Coroutine {
	func run() {
		print("Before yield!")
		self.yield(); // -> returns to caller 
		print("After yield!")
	}
}
```
Just a simple abstraction of *coroutine* work. 
Instead of run code from start (as usuall function) it starts it from *yield()* point.