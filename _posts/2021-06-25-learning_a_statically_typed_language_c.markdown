---
layout: post
title:      "Learning a statically typed language C#"
date:       2021-06-25 17:49:35 +0000
permalink:  learning_a_statically_typed_language_c
---


Up until now, I have mostly been coding in JavaScript, Ruby, and Python.  But recently, I had to do a coding challenge in C#, which is relatively unlike those languages and has more in common with languages like C, C++, and Java. Unfortunately I did not get that position, but I got a good introduction to C# along the way and an insight into a new kind of programming language.

**Typing discipline**

A big difference between these languages, from the programmer's perspective, is in typing discipline. Most languages work with data types such as integers, floating point numbers, booleans, strings. etc. Statically typed languages require the programmer to explicitly declare a type when declaring a variable, and also for function parameters, return values, and so forth. Dynamically typed languages do not require the programmer to declare types, implicitly determining the type based on context. 

**Assigning variables with types**

For instance, a dynamically typed language can see that `x = 2` is an integer and `y = "hello"` is a string. Depending on the language, they can often convert types as needed, like `2 + "2"` equals 22 in JavaScript. In statically typed languages, you cannot simply assign variables in the manner of `x = 2` or `y = "hello` . You must explicitly declare the type: `int x = 2` or  `string y = "hello` 

The advantage of dynamically typed languages is that they are easier to use, especially for new programmers, and save you the complexity of declaring and casting types. A disadvantage that can be fixed with statically typed languages is that bugs are common when wrong types are input. What if you tried to take x to the power of y `x ** y` with both variables assigned above? In JavaScript you get `undefined`. In other langauges you might get a syntax error. Both of these might cause hard to track problems and unexpected errors (even with the error, you might not know where it is coming from). While, in C#, if you multiply x by y as follows:

```

      int x = 2;
      string y = "hello";
      Console.WriteLine(x * y);
```

You get a clear error that shows you have inconsistent types:

```
main.cs(8,25): error CS0019: Operator `*' cannot be applied to operands of type `int' and `string'
```

Whenever a problem of inconsistent types arises in your program, an error like this stops it from compiling and forces you to fix the type bug.

**Example of types, variables, and methods**

The following is a simple "hello world" program but it illustrates how C# uses types:
```
class Program {
    static void Main(string[] args) {
        Console.WriteLine("Hello, world!");
  
    }
}
```
All programs in C# need a `Main` method which runs when you execute the program. `static` means that the method is a class method rather than an instance method. `void` is a special "data type" that means the program returns nothing. `string[] args` means the method can take in an array of strings as an argument.

Here is an example that illustrates how types work with functions:
```
class Program {

    public static int add_numbers(int x, int y) {
      int result = x + y;
      return result;
    }
    static void Main(string[] args) {
      Console.WriteLine(add_numbers(4, 5));
    }

}
```
The `add_numbers` method takes in two integers, x and y, and returns an integer. (`public` allows it to be called outside the class, and `static` means it can be called directly without creating an instance). As written, the WriteLine will call `add_numbers` with arguments 4 and 5, it will return 9. All the types are correct.

But what if you called the function with 4 and (string) "5"? Then you get errors that tell you the types are wrong, the int y cannot take in a string argument:
```
main.cs(10,25): error CS1502: The best overloaded method match for `Program.add_numbers(int, int)' has some invalid arguments
main.cs(10,40): error CS1503: Argument `#2' cannot convert `string' expression to type `int'
```
Method overloading is a more advanced functionality in which you can write additional code to handle different types of arguments, and the first error notes that I haven't defined one.

If you tried to return a non-int from the function, say a string as follows:
```
public static int add_numbers(int x, int y) {
      int result = x + y;
      string hello = "hello";
      return hello;
    }
```
You also get an error about incompatible types:
```
main.cs(8,14): error CS0029: Cannot implicitly convert type `string' to `int'
```
These examples show how static typing can protect you from bugs caused by inconsistent types.

**References**
[C# REPL on Replit](https://replit.com/languages/csharp)
[Learn C# in One Day](https://www.amazon.com/Beginners-Hands-Project-Coding-Project-ebook/dp/B016Z18MLG)
[C# documentation](https://docs.microsoft.com/en-us/dotnet/csharp/)


