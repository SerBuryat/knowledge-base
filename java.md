>Write once, run everywhere…* 

Strongly typed and compiled OOP language corporated by Sun Microsystems (later Oracle).

Simple Java program to print "Hello world" in console:
`Main.java file`
``` java title:Main.java
public class Main {
	public static void main(String[] args) {
		System.out.println("Hello world");
	}
}
```

After compilation (`javac Main.java` command) `Main.java` file turns into `Main.class`*(bytecode)* file and can run with `java Main` command to print "Hello world".

For compile `.java` files need a **JDK(Java Development Kit)**.
For run `.class` files need a **JRE(Java Runtime Environment)** which include **JVM(Java Virtual Machine)** - **turn bytecode(`.class` files) to machine code** for run on exact system.
Every system *(PC, phone, in some kind hardware or software)* has it's own JVM.

Java program execution steps:
- Programmer write *java code*
- Compiler turn it in *bytecode(`.class` files)*
- JVM turns bytecode in *machine code* for run on *exact system*



