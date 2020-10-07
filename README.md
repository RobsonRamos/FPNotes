# FPNotes
Functional programming notes and concepts
## Imperative Programming

Imperative programming is a programming paradigm that uses statements that change a program’s state. It consists of a series of commands for the computer to perform. It focuses on describing the details of how a program operates.

## What is FP? 

-	FP applications consist of only immutable values and pure functions. 
-	Pure function means that (a) a function’s output depends only on its input parameters, and (b) functions have no side effects, such as reading user input or writing output to a screen, disk, web service, etc. 

“Functional code is characterized by one thing: the absence of side effects. It (a pure function) doesn’t rely on data outside the current function, and it doesn’t change data that exists outside the current function. Every other ‘functional’ thing can be derived from this property. Use it as a guide rope as you learn.”  

## but why immutable values? 
The best FP code is like algebra, and in algebra you never re-use variables. And not re-using variables has many benefits. 

## Lambda calculus
Therefore, the term lambda calculus refers to “a formal way to think about functions.” 
That same Wikipedia link states this: 
“Lambda calculus provides a theoretical framework for describing functions and their evaluation. Although it is a mathematical abstraction rather than a programming language, it forms the basis of almost all functional programming languages today.” 
 
## Terminology
A common theme is that these terms generally come from mathematics fields like category theory. 
-	Functor: Things that can be mapped over  
-	Combinator: “a style of organizing libraries centered around the idea of combining things.” This refers to being able to combine functions together like a Unix command pipeline
-	Lambda: another word to anonymous function
-	Referential transparency (RT): If you like algebra, you’ll like RT. It’s said that an expression is referentially transparent if it can be replaced by its resulting value without changing the behavior of the program.  
## FP is like algebra
It’s important to understand what “algebra” is so you can really internalize this lesson. Unfortunately, trying to find a good definition of algebra is difficult because many people go right from the concept of “algebra” to “mathematics,” and that’s not what I have in mind. 
It’s easy to write those algebraic equations as pure functions in Scala/FP. Assuming that all the values are integers, they can be written as these functions in Scala: 
def f(x: Int) = x + 1
def f(x: Int, y: Int) = x + y
def f(a: Int, b: Int, c: Int, x: Int) = a*x*x + b*x + c  

## Statements and Expressions
When you write pure functional code, you write a series of expressions that combine pure functions. In addition to this code conforming to an FP style, the style also fits the definition of “Expression-Oriented Programming,” or EOP. This means that every line of code returns a result (“evaluates to a result”), and is therefore an expression rather than a statement.

order.calculateTaxes()
order.updatePrices() 

Those two lines of code are statements because they don’t have a return value; they’re just executed for their side effects. In FP and EOP you write those same statements as expressions, like this: 
val tax = calculateTax(order)
val price = calculatePrice(order) 

While that may seem like a minor change, the effect on your overall coding style is huge.  

 
## Partially-Applied Functions (and Currying) 
All that the theory of currying means is that a function that takes multiple arguments can be translated into a series of function calls that each take a single argument. In pseudocode, this means that an expression like this: 
```
result = f(x)(y)(z) is mathematically the same as something like this: 
f1 = f(x)
f2 = f1(y)
result = f2(z) 
```
That’s all it means. The Wikipedia page on Currying describes the theory of currying like this: 
In mathematics and computer science, currying is the technique of translating the evaluation of a function that takes multiple arguments into evaluating a sequence of functions, each with a single argument. 
They later state: There are analytical techniques that can only be applied to functions with a single argument. Practical functions frequently take more arguments than this.  
Partial Application: This is the process of applying a function to some of its arguments. A partially-applied function gets returned for later use. In other words, a PAF is a function that takes a function with multiple parameters and returns a function with fewer parameters.  
In the first step, you define a function with multiple parameter groups: 
```
scala> def plus(a: Int)(b: Int) = a + b
plus: (a: Int)(b: Int)Int 
```
Next, rather than giving the function all of the parameters in the two parameter groups it specifies, you give it (a) the parameter for the first group ( a ), and (b) a placeholder for the parameter in the second list, the ubiquitous underscore character: 
```
scala> def plus2 = plus(2)(_)
plus2: Int => Int 
```
The REPL output shows that this creates a new function named plus2 which has the type Int => Int . This means that plus2 takes an Int as input, and returns an Int as a result. At this point you can think of plus2 as looking like this: def plus(b: Int) = 2 + b plus2 has been “seeded” with the initial Int value 2 , and now it’s just sitting there, waiting for another Int value that it can add to it. Let’s give it another 2 : 
```
scala> plus2(2)
res0: Int = 4
 Here’s what it looks like when you give it a 3 : 
scala> plus2(3)
res1: Int = 5 
```
## Currying

You can create curried functions from “normal” Scala functions. For instance, you can start with a “normal” one-parameter group function like this: 
```
def add(x: Int, y: Int) = x + y 
```
Then they show that you can create a Function2 instance from add by adding an underscore after it, like this: 
```
scala> val addFunction = add _
addFunction: (Int, Int) => Int = <function2> 
```
Then they create a “curried” function from that Function2 instance: 
```
val addCurried = (add _).curried 
```
Now you can use the new curried function like this: addCurried(1)(2) 
As this shows, calling the curried method on the add function instance creates a new function that has two parameter groups. (So, a curried function can be thought of as a function with multiple parameter groups.) 


## Currying vs partially-applied functions 

The concepts of currying and partially-applied functions are closely related, but they aren’t exactly the same. As I wrote at the beginning, currying is defined like this:
A function that takes multiple arguments can be translated into a series of function calls that each take a single argument. 
This is particularly important in a language like Haskell, where all functions are technically curried functions. In Scala this is generally a theoretical thing that’s good to know about, and it’s good to know that you can create a curried function from an uncurried function, but these aren’t “core” features you absolutely need to know to write code in Scala. 
A partially-applied function on the other hand is a function that you manually create by supplying fewer parameters than the initial function defines. 

## Recursion 

Simply stated, a recursive function is a function that calls itself. That’s it. 
The next question that usually comes up right about now is, “Why do I need to write recursive functions? Why can’t I use for loops to iterate over lists?” The short answer is that algorithms that use for loops require the use of var fields, and as you know from our rules , functional programmers don’t use var fields. 

## Tail-Recursive Algorithms 

Algorithms with deep levels of recursion can crash with a StackOverflowError , all is not lost. With Scala you can work around this problem by making sure that your recursive functions are written in a tail-recursive style. A tail-recursive function is just a function whose very last action is a call to itself. When you write your recursive function in this way, the Scala compiler can optimize the resulting JVM bytecode so that the function requires only one stack frame as opposed to one stack frame for each level of recursion!  
When you make this change, the final code looks like this: 
```
// tail-recursive solution
def sum(list: List[Int]): Int = {
@tailrec
def sumWithAccumulator(list: List[Int], currentSum: Int): Int = {
list match {
case Nil => currentSum
case x :: xs => sumWithAccumulator(xs, currentSum + x)
}
}
sumWithAccumulator(list, 0)
} 
```
## State in FP aplications

In functional programming you don’t modify (mutate) existing objects, you create new objects with updated fields based on existing objects. What you do is (a) you copy the existing object to a new object, and (b) during the copy process you update any fields you want to change by supplying their new values. The way you do this in Scala/FP is with the copy method that comes with the Scala case class. 
Because functional programmers don’t mutate objects, when an object needs to be updated it’s necessary to follow a pattern which I describe as “update as you copy”.  

## For comprehension

The name “comprehension” comes from both Haskell and mathematics, with mathematics preceding Haskell by some large number of years. The book, Haskell, the Craft of Functional Programming, provides this introduction: “One of the features of a functional language is the list comprehension notation, which has no parallels in other paradigms.” Learn You a Haskell for Great Good states: “List comprehensions are a way to filter, transform, and combine lists. They’re very similar to the mathematical concept of set comprehensions Set comprehensions are normally used for building sets out of other sets.” 
A Scala for comprehension can contain the following three expressions: 
-	Generators: has the form pattern <- expression and results in an assignment

-	Definitions: have this general form: pattern = expression. A definition binds the pattern on the left to the value of expression on the right. 


-	Filters: Filters drop all elements from the iteration for which expression returns  

Here’s a simple example of each of these features in a Scala for comprehension: 

```
case class Person(firstName: String, lastName: String)

   val people = List(
                            Person("barney", "rubble"),
                            Person("fred", "flintstone")
   )
   val namesStartingWithB = for {
                            p <-people // generator
                            fname = p.firstName // definition
                            if (fname startsWith "b") // filter   
} yield fname.toUpperCase 
```

## Functional Error Handling 
Pure functions never throw exceptions. 
Think of it this way: the signature of a pure function is a binding contract with the consumer of that function. If the function signature says it returns an Int : 
```
def foo(a: String): Int = ... 
```

then it must return an Int . Importantly, it can’t return an Int most of the time and throw an exception some of the time. If a function was to behave like that, its signature would be a lie.  
Rather than throwing exceptions, the Scala/FP idiom is to handle exceptions inside your functions and return an Option. 

## Monad

Where “monad” used to be a scary name, now you know that it just means that a data type implements map and flatMap methods so it can be used in for expressions. I tend to call these things “wrappers,” and indeed, the IO monad is just another wrapper data type. The great thing about that box is that the for expression knows how to open it, and pull the String out of it.  

## Domain Model

In FP, the data and the operators on that data are two separate things; you aren’t forced to encapsulate them together like you do with OOP. The concept is like numerical algebra. When you think about whole numbers whose values are greater than or equal to zero, you have a set of possible values that looks like this: 
```
0, 1, 2 ... Int.MaxInt 
```

Ignoring the division of whole numbers, the possible operators on those values are: 
+, , * 
An FP design is implemented in a similar way:
- You have a set of values 
- You have a collection of operators that work on those values

Modeling the “data” portion of a domain model in Scala/FP is simple: 
-	Model the data as case classes with immutable fields.  
By separating the data from the operations on that data, the data attributes and relationships are clear. The data model is easy to read, like declaring the design for a relational database.  Where OOP practitioners describe their classes as “rich domain models” that encapsulate data and behaviors, FP data models can be thought of as “skinny domain objects.” This is because, the data models are defined as case classes with attributes, but no behaviors.  
 When it comes to modeling behaviors several different possible approaches: 
-	Put your functions in “Utils” classes 
-	Put your functions in companion objects 
-	Use a modular programming style 
-	Use a “functional objects” approach 

## Concurrency

Programming in a functional style makes the state presented to your code explicit, which makes it much easier to reason about, and, in a completely pure system, makes thread race conditions impossible: “Immutable objects are always thread-safe.” 
