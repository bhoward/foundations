# Functional Programming

## Introduction to Scala

This guide assumes that you are already familiar with programming in Java and/or C++. You may not be an expert, but you are comfortable with writing classes and methods. We will by no means be learning all of Scala here, and when there are multiple ways to express something in Scala we will generally just choose one. The emphasis is on becoming able to read and write simple functions that manipulate the kinds of data models talked about in the Foundations class: lists, trees, graphs, *etc*. Although Scala has extensive support for an object-oriented style of programming, we will be focusing on its support for functional programming.

Before diving into the details, here is a short sales pitch for Scala. This is a quote from the main Scala website, http://www.scala-lang.org/:

> Scala is a general purpose programming language designed to express common programming patterns in a concise, elegant, and type-safe way. It smoothly integrates features of object-oriented and functional languages, enabling Java and other programmers to be more productive. Code sizes are typically reduced by a factor of two to three when compared to an equivalent Java application.

Scala is a real-world, commercial-strength language, being used at companies such as Twitter, LinkedIn, FourSquare, Novell, and many others; one of its attractions to these businesses is that it is very compatible with existing Java code, while also supporting more advanced programming techniques as found in functional languages such as Haskell, Standard ML, and F#, and dynamic languages such as Python and Ruby.

## Values and Expressions

The basic types of values are essentially the same as in Java, except by convention all of the type names are capitalized (*e.g.*, `Boolean` instead of `boolean`):

* **Integers** (`Int`): `0`, `42`,`0x2A` (hexadecimal, base-16)

* **Reals** (`Double`): `42.0`, `4.2e1`, `.42e+2`, `420e-1`

* **Booleans** (`Boolean`): `true`, `false`

* **Characters** (`Char`): `'*'`, `'\u002A'`, `'\n'` (newline), `'\''` (single-quote)

* **Strings** (`String`): `"Hello World"`, `"I said, \"Hello!\""`, `"line 1\nline 2"`

One convenient extension in Scala is the ability to specify strings that contain special characters, such as quotes and newlines, by enclosing them in triple quotes:

```
"""This is a string with several lines.
This is the second line, which contains a quotation: "Hello World".
This is the third line."""
```

Another, more recent, extension to the notation for strings is "string interpolation." This is actually a very general and extensible facility in the language, but in its simplest form, if you prefix a string literal with the letter `s`, then any occurrence of `$x` in the string will be replaced by the value of variable `x` (converted to a string, if necessary). Instead of a single variable, an entire expression `expr` will be interpolated if it is enclosed in braces: `${expr}`. Here is an example:

```
s"The value of x is $x, and x*7 is ${x*7}."
```

If `x` is 6, then this produces the string `"The value of x is 6, and x*7 is 42."`

The Scala standard library provides quite a few types of collections as well; here are examples of how to create instances of some of them:

* **Lists**: If `x1`, `x2`, `x3`, ... are values of some type `T`, then `List(x1, x2, x3, ...)` has type `List[T]` (this is akin to the Java parameterized type `List<T>`). Another name for the empty list is `Nil`, and values may be added to the front of a list using the `::` operator, so `1 :: 2 :: 3 :: Nil` is an equivalent way of constructing `List(1, 2, 3)`, of type `List[Int]`. The front element of a (non-empty) list `a` may be retrieved using the `head` operation: `a.head`; the list of everything except the head is `a.tail`. For example, `List(1, 2, 3).head` is `1`, while `List(1, 2, 3).tail` is `List(2, 3)`.

* **Tuples**: If `x1`, `x2`, `x3`, ... are values of types `T1`, `T2`, `T3`, ..., respectively, then `(x1, x2, x3, ...)` has type `(T1, T2, T3, ...)`. A common special case of this is the *pair*; for example, `(42, "Hello World")` is a pair of type `(Int, String)`. Another special case is the type `Unit`, which has the single value `()` -- that is, it is a tuple with no elements. The first element of a (non-empty) tuple `a` may be accessed as `a._1`; the second element is `a._2`, *etc*. It is often better to extract elements of a tuple with pattern matching; see below.

* **Arrays**:  If `x1`, `x2`, `x3`, ... are values of some type `T`, then `Array(x1, x2, x3, ...)` has type `Array[T]` (this is akin to the Java type `T[]`). Just as in Java and C++, arrays are zero-indexed. Element `i` of array `a` is named by the expression `a(i)`, so if `a.size` is `n`, then the elements of `a` are `a(0)` through `a(n-1)`. To create an array containing `n` copies of some value `x`, use `Array.fill(n)(x)`.

* **Sets**: The type `Set[T]` is very similar to `List[T]`, except that, just like a mathematical set, it does not keep track of the order in which the elements were inserted, and it does not keep duplicate elements. That is, `Set(1, 2, 3)` is the same as `Set(3, 1, 1, 3, 2)`. The fundamental operation on a set is to check whether it contains a particular value: `a.contains(x)` returns `true` if `x` is an element of `a`.[^1]

[^1]: A set may also be used as a function in Scala: `a(x)` is the same as `a.contains(x)`.

* **Maps**: A common data structure in dynamic languages is the "associative array", which behaves like an array with indices more general than the integers `0` to `n-1`. For example, it is often useful to be able to index into a collection using a string: after setting `age("George")` to `17`, we may retrieve George's age by providing the index `"George"` to the map `age`. The Scala type `Map[K, T]` provides this facility: if `k1`, `k2`, `k3`, ... are *keys* of type `K`, and `x1`, `x2`, `x3`, ... are values of type `T`, then `Map(k1 -> x1, k2 -> x2, k3 -> x3, ...)` will map each key to its corresponding value. If `age` is `Map("Alice" -> 9, "Susanna" -> 14, "George" -> 17)`, then `age("George")` will be `17`, as desired; the type of `age` is `Map[String, Int]`. By the way, the expression `"Alice" -> 9` is another way to write the pair `("Alice", 9)`. A map is essentially a set of pairs, stored in a data structure that makes it efficient to use the map as a function to look up the value corresponding to a key. Given a map `m`, the expression `m.keySet` produces a set containing all of the keys for which the map is defined; in the example, `age.keySet` will be the same as `Set("George", "Susanna", "Alice")` (remember that order does not matter in a set).

* **Options**: A common source of errors in Java is the convention of returning `null` to indicate that some object was unavailable (for example, given a Java map similar to the `age` example above, `age.get("Fred")` would return `null`). Scala attempts to avoid this by providing the "option" type: a value of type `Option[T]` is either `Some(x)`, where `x` is a value of type `T`, or it is the special value `None`. The advantage over returning `null` is that `None` is an actual object; attempting to call a method on it will not result in a null pointer exception. The option type is also a signal to the programmer to be prepared for the case of a non-existent object; in Java, it is too easy to ignore this possibility because *every* object type allows `null`. To retrieve the contained value from an option object, use the `get` method: `Some(x).get` yields `x`. Attempting to call `get` on `None` will throw an exception, so a useful variant is the `getOrElse` method: `a.getOrElse(y)` will return the contents of `a`, if any, or `y` if `a` was `None`. As with tuples (and lists), we will see below that it is often better style to use pattern matching on options.

The expressions of Scala are carried over largely unchanged from Java and C++. For example, `(0 <= x) && (x < 10)` is true when `x` is between 0 (inclusive) and 10 (exclusive). Common mathematical functions are available in the `math` object: `math.sqrt(x)`, `math.pow(x, n)`, and `math.max(x, y)` are examples, returning $$\sqrt{x}$$, $$x^n$$, and the maximum of `x` and `y`, respectively. The syntax for calling methods or accessing fields of an object is the same: `a.m(x, ...)` calls method `m` of `a` with arguments `x`, ..., while `a.f` accesses the field of `a` named `f`. Scala provides a few nice generalizations of this:
* if a method takes no parameters, you may omit the parentheses around the arguments (this removes the distinction between accessing a field and calling a no-parameter method -- think of the difference in Java between the `.length()` method on strings and the `.length` field on arrays; in Scala, both of these quantities may be accessed as `.length`[^2]);
* if a method takes exactly one parameter, then the dot and the parentheses may be omitted (for example, given a set `a`, the expression `a contains x` is the same as `a.contains(x)`; Scala allows methods to be used like operators in this fashion because in fact all of the operators, such as `+`, are really methods: `a + b` is the same as `a.+(b)`).

One last fact to notice about Scala expressions is that even "primitive" values are objects with methods: `42.toString` yields `"42"`.

[^2]: As an added convenience, all of the collections except tuples define both `.length` and `.size`, so you don't have to remember which defines which.

### Exercises
1. Evaluate the following expressions and explain what operations are being performed:
   1. `List(3, 1, 4) ++ List(2, 3, 5)`
   2. `Set(3, 1, 4) & Set(2, 3, 5)`
   3. `Set(3, 1, 4) | Set(2, 3, 5)`
   4. `Set(3, 1, 4) &~ Set(2, 3, 5)`
   5. `Set(3, 1, 4).subsets.toSet`

2. For each of the following expressions, predict what the result will be, then check your prediction by evaluating it in Scala:
   1. `Map(1 -> 1, 2 -> 4, 3 -> 9)(2)`
   2. `Map(1 -> 1, 2 -> 4, 3 -> 9)(4)`
   3. `Map(1 -> 1, 2 -> 4, 3 -> 9).get(4)`
   4. `Map(1 -> 1, 2 -> 4, 3 -> 9) get 2`
   5. `Map(1 -> 1, 2 -> 4, 3 -> 9).keySet`

## Control Structures

As with expressions, the control structures such as \texttt{if} and \texttt{while} are substantially the same in Scala as in Java and C++. For example, the following code fragment works exactly the same in all three languages:
\begin{verbatim}
while (n != 1) {
  if (n % 2 == 0) {
    // n is even
    n = n / 2;
  } else {
    // n is odd
    n = 3 * n + 1;
  }
}
\end{verbatim}
One minor difference is that Scala doesn't require the semicolons at the end of each simple statement, although they are allowed. A more significant innovation is that statements and control structures all produce values, so they may be used in expressions. The \texttt{while} loop only produces the value \texttt{()} of type \texttt{Unit} (which is equivalent to the \texttt{void} type in Java and C++), so that isn't particularly useful. However, the \texttt{if} statement evaluates to one of its two branches, depending on the test, so the above code may also be written
\begin{verbatim}
while (n != 1) {
  n = if (n % 2 == 0) n / 2 else 3 * n + 1
}
\end{verbatim}
Java and C++ provide the operator \texttt{?:}, as in \verb|(n % 2 == 0) ? (n / 2) : (3 * n + 1)|, for this purpose, but Scala's approach makes that unnecessary.

In a block of code surrounded by braces, the value produced is the value of the final statement. For example,
\begin{verbatim}
x = {
  while (j != 0) {
    val temp = j
    j = i % j
    i = temp
  }
  i
}
\end{verbatim}
The value eventually assigned to \texttt{x} is the final value of \texttt{i}, after some number of times through the loop (in fact, this code assigns the GCD of \texttt{i} and \texttt{j} to \texttt{x}; the \texttt{val} declaration will be discussed below). There is a \texttt{return} statement, which breaks out of a block and returns a value from the enclosing function (see below), but it is generally used only when the normal program flow must be altered, preventing control from reaching the end of the block.

Scala does \emph{not} provide the same \texttt{for} loop as its predecessors. Part of the reason for this is that it discourages writing code that relies on modifying, or ``mutating,'' values bound to variables (Scala doesn't even provide the operator \texttt{++}, which is found so frequently in counting \texttt{for} loops, although it does support the shortcut assignment operators such as \texttt{+=}). More importantly, though, Scala provides a very nice generalization of the \texttt{for} loop, which can iterate through many of the collection types in a uniform way. Given a collection $a$ (other than a tuple), the statement \texttt{for ($x$ <- $a$) $b$} will execute the body $b$ once for each value $x$ taken from $a$. The expression \texttt{$x$ <- $a$} is called a ``generator.'' For example,
\begin{verbatim}
for (n <- List(3, 1, 4, 1, 5)) {
  println(n)
}
\end{verbatim}
produces the output
\begin{verbatim}
3
1
4
1
5
\end{verbatim}
The typical C++ or Java counting loop, written as \texttt{for (i = 0; i < N; i++)}, becomes \texttt{for (i <- 0 until N)}. The expression \texttt{0 until N} produces a ``range'' collection, which is a sequence where the successive values differ only by a step value; by saying \texttt{until}, the range excludes the final value, \texttt{N}; inclusive ranges are possible with the \texttt{to} operator: \texttt{1 to 3} is the sequence 1, 2, 3. The \texttt{by} operator can be used to get a step other than 1: \texttt{10 to 1 by -1} is the range that counts from 10 down to 1. The value returned from this form of the \texttt{for} loop is \texttt{()}, just as with \texttt{while}.

An additional generalization of the \texttt{for} loop can be used to construct a new collection: the expression \texttt{for ($x$ <- $a$) yield $b$} evaluates the expression $b$ for each element $x$ in $a$, and gathers all of the results in a collection of the same kind as $a$. For example,
\begin{verbatim}
for (i <- List(3, 1, 4, 1, 5)) yield i * i
\end{verbatim}
produces the result \texttt{List(9, 1, 16, 1, 25)}.

The remaining ``control structure'' to be discussed in this section is the pattern match. This generalizes the \texttt{switch} statement of Java and C++. For example,
\begin{verbatim}
name match {
  case "Alice" => 9
  case "Susanna" => 14
  case "George" => 17
  case _ => -1
}
\end{verbatim}
Depending on the string value \texttt{name}, this expression will evaluate to \texttt{9} if it is \texttt{"Alice"}, \texttt{14} if it is \texttt{"Susanna"}, \texttt{17} if it is \texttt{"George"}, and \texttt{-1} otherwise. Many types may be matched on; one of the most useful for our purposes is the list:
\begin{verbatim}
myList match {
  case Nil => println("The list is empty")
  case h :: t => println(s"The head is $h and the tail is $t")
}
\end{verbatim}
Since every list is either empty (\texttt{Nil}) or the concatenation of a head element onto a tail list, \texttt{myList} will match one of the two cases. In the second case, the names \texttt{h} and \texttt{t} (which may be arbitrary variable names) will be bound to the corresponding parts of the list. This is an instance of pattern matching over ``case classes,'' which are described more below.

The ``wildcard'' pattern, \verb|_|, matches anything (this was seen above in the default case for matching names to ages). Here is another example for lists:
\begin{verbatim}
front = myList match {
  case Nil => sys.error("Empty list")
  case f :: _ => f
}
\end{verbatim}
If \texttt{myList} is empty, then the \texttt{sys.error} function will throw an exception with the given message; otherwise, the first element will be bound to \texttt{f} (which is a newly created variable, local to the match), then returned as the result of the match, and ultimately assigned to \texttt{front}.

As mentioned above, pattern matching is a good way to examine tuples and options. For example, if \texttt{x} is of type \texttt{(Int, String)}, then
\begin{verbatim}
x match {
  case (n, s) => "First is " + n + ", second is " + s
}
\end{verbatim}
will split up the tuple and bind the first element to \texttt{n} and the second to \texttt{s}. If \texttt{a} is of type \texttt{Option[T]}, then
\begin{verbatim}
a match {
  case Some(t) => t
  case None => y
}
\end{verbatim}
will have the same effect as \texttt{a.getOrElse(y)}.

Patterns may be combined and nested, so that several components have to match simultaneously. For example, suppose we want to take \texttt{p}, a pair of lists of numbers, and produce an option value that is \texttt{None} if either list is empty, and \texttt{Some($x\cdot y$)} if $x$ and $y$ are the heads of the (non-empty) lists:
\begin{verbatim}
p match {
  case (x :: _, y :: _) => Some(x * y)
  case _ => None
}
\end{verbatim}
Cases are tried in order, so less-specific patterns should come later in the match. For example, the following will return \verb|"A"| if the list \texttt{x} has at least two elements, \verb|"B"| if the list has only one element and it is \texttt{42}, \verb|"C"| if the list has only one element and it is \emph{not} \texttt{42}, and \verb|"D"| if the list is empty:
\begin{verbatim}
x match {
  case _ :: _ :: _ => "A"
  case 42 :: _ => "B"
  case _ :: _ => "C"
  case _ => "D"
}
\end{verbatim}
The third pattern, \verb|_ :: _|, would match any list with at least one element (note that each occurrence of the wildcard may match something different), but the first two cases have already covered lists with two or more elements and lists starting with \texttt{42}, so by the time the third pattern is tried it will only catch the single-element lists that are not \texttt{List(42)}.

\subsection*{Exercises}
\begin{enumerate}
\item Write and test a loop to print the squares of the numbers from \texttt{1} to \texttt{10}:
\begin{center}
\texttt{1 4 9 16 25 36 49 64 81 100}
\end{center}
Write it first as a \texttt{while} loop (you will need to declare a loop variable with a statement such as \verb|var i = 1|), then write it as a \texttt{for} loop. The \texttt{print} function outputs a string without advancing to the next line.

\item Do the same exercise as above, but use nested loops to print a $3\times 3$ multiplication table:
\begin{center}
\texttt{1 2 3}\\
\texttt{2 4 6}\\
\texttt{3 6 9}
\end{center}

\item The generalized \texttt{for} loop has a number of additional features. For example, nested loops may be combined into a single loop with a list of generators:
\begin{verbatim}
for (
  i <- Set(1, 2, 3);
  j <- Set('a', 'b', 'c')
) yield (i, j)
\end{verbatim}
There is actually a subtle difference between this and the nested version:
\begin{verbatim}
for (i <- Set(1, 2, 3)) yield
  for (j <- Set('a', 'b', 'c')) yield (i, j)
\end{verbatim}
Try each, identify the difference, and explain which one is more useful for performing a common operation on sets.

\item Write and test a \texttt{match} expression that takes a pair of integer lists and returns the smaller of the head elements. If only one list is non-empty, return its head; if both are empty, signal an error with a call to \texttt{sys.error}. For testing purposes, you will need to use \verb|List[Int]()| to enter an empty list. Thus, you should see the following results:
\begin{itemize}
\item \verb|(List(1, 2), List(3, 4, 5)) match ...| produces \texttt{1};
\item \verb|(List(1, 2), List(0, -1, 2)) match ...| produces \texttt{0};
\item \verb|(List(1, 2), List[Int]()) match ...| produces \texttt{1};
\item \verb|(List[Int](), List(3, 4, 5)) match ...| produces \texttt{3};
\item \verb|(List[Int](), List[Int]()) match ...| produces an error.
\end{itemize} 
\end{enumerate}

\section{Declarations}
\subsection{Variables}
As mentioned before, Scala supports, but does not require, a functional style of programming. Part of this is that it encourages the use of variables that are never mutated. If a variable is declared with the keyword \texttt{val}, this will be enforced---the compiler will not allow you to assign a new value to such a variable later in the program (this is akin to \texttt{final} variables in Java, and \texttt{const} in C++). If you really need a variable that may vary, then it should be declared with the keyword \texttt{var}, but good style is to use \texttt{var} only when necessary.

In both cases, a variable is declared by giving the name and its initial value:
\begin{verbatim}
val x = 42
var y = "Hello World"
\end{verbatim}
The type of the variable will be inferred from the type of the expression. This is usually the desired type, but it is possible to specify a (possibly different) type by putting it after the variable name, separated by a colon:
\begin{verbatim}
var myList: List[Int] = Nil
\end{verbatim}
In this case, we want to specify the more general type \texttt{List[Int]}, instead of the inferred type of \texttt{Nil} (which happens to also be \texttt{Nil}---see below), so that later on it will be legal to assign a different list (such as \verb|42 :: myList|) to \texttt{myList}. In this particular case, we could also have initialized \texttt{myList} to \texttt{List[Int]()}, which is an empty list of the correct type.

The \texttt{val} declaration is useful when introducing a temporary variable to hold an intermediate result in a computation. Scala supports using a limited form of pattern matching with \texttt{val} for the situation that the intermediate result is a compound data type such as a tuple, allowing the programmer to introduce convenient names for each element of the structure (or ignore some with wildcards). For example, lists have a method \texttt{splitAt($n$)} that returns a pair of lists; the first $n$ elements are in the first list, and the rest are in the second. Here is code using a pattern-matching \texttt{val} to name these two lists:
\begin{verbatim}
val (frontHalf, backHalf) = x.splitAt(x.length / 2)
\end{verbatim}

\subsection{Functions}
A function is declared much like a variable, except the keyword is \texttt{def} and the function name may be followed by a list of parameters:
\begin{verbatim}
def midpoint(a: Double, b: Double): Double = (a + b) / 2
\end{verbatim}
Unlike a variable declaration, where the right-hand-side is evaluated right away and used to initialize the variable, the right-hand-side of a function is an expression that will be evaluated each time the function is called, with the provided arguments bound to the parameter names. For example, when we call \texttt{midpoint(3.0, 5.4)}, the expression \texttt{(a + b) / 2} will be evaluated with \texttt{a} being \texttt{3.0} and \texttt{b} being \texttt{5.4}.

Remember that many constructs in Scala are expressions that produce values, including blocks of statements in curly braces where the value is the value of the last statement. This means that, even in a function whose body consists of several statements, Scala does not require a \texttt{return} statement---the value to return will be the value on the last line of the block.

A function does not need to take parameters. A useful example is a function to simulate rolling a six-sided die:
\begin{verbatim}
def roll: Int = (math.random * 6).toInt + 1
\end{verbatim}
Each time \texttt{roll} is called, the expression will be re-evaluated; since it includes a call to a random-number generator (\texttt{math.random} returns a number between 0.0 and 1.0), the result will randomly be one of the values 1 through 6.

Scala does not always require you to specify the return type of a function, because it can frequently infer it from the right-hand-side. However, one case where it is unable to do so is when the function is recursive; since we will frequently be working with recursion, it will be a good habit always to specify the return type. There is one exception to this, however; when a function returns a value of type \texttt{Unit}---that is, when we are executing the function solely for some side-effect, such as printing a message or sorting an array in place---then we will commonly use the abbreviation
\begin{verbatim}
def myFun(...) {
  ...
}
\end{verbatim}
instead of
\begin{verbatim}
def myFun(...): Unit = {
  ...
}
\end{verbatim}
This matches the conventional form of a ``void'' function in Java or C++, and emphasizes that we are not producing a value from the function body.

Strictly speaking, the functions described so far are really methods of some object, although exactly which object that is might be hidden by the context in which we are executing our code. As a functional language, Scala also supports true function values, which may be passed around, bound to variables, \textit{etc}. These are introduced with syntax of the form \texttt{($x$:\ $T$) => $b$}, which describes an anonymous function with parameter $x$ of type $T$ and function body $b$. For example, \texttt{(n:\ Int) => n * n} is a function which takes an integer and returns its square. Binding it to a variable, as follows, produces essentially the same effect as a named function declaration:
\begin{verbatim}
val square = (n: Int) => n * n
square(4) // yields 16
\end{verbatim}

\subsection{Classes}
As a means to encapsulate related fields and methods, Scala's classes are quite similar to Java's. Perhaps the most significant difference is in the form of the constructor: where Java declares a method with the same name as the class, Scala merges the constructor body with the class definition itself. This, together with other features of the language, frequently leads to a significant reduction in ``boilerplate'' code (that is, code which follows a standard, predictable pattern, and which contains very little new information). Here is an example:
\begin{verbatim}
// THIS IS JAVA
public class Triangle {
  private double width;
  private double height;
  private double hypotenuse;

  public Triangle(double width, double height) {
    this.width = width;
    this.height = height;
    this.hypotenuse = Math.sqrt(width * width + height * height);
  }

  public double getWidth() {
    return width;
  }

  public double getHeight() {
    return height;
  }

  public double getHypotenuse() {
    return hypotenuse;
  }
}
\end{verbatim}
In Scala, this would typically be written as follows:
\begin{verbatim}
class Triangle(val width: Double, val height: Double) {
  val hypotenuse = math.sqrt(width * width + height * height)
}
\end{verbatim}
The class definition header includes the constructor parameters \texttt{width} and \texttt{height}; by making them \texttt{val}s, they will be exposed as read-only fields of the object. The declarations in the body of the object are evaluated with the actual arguments bound to these parameters, so when the field \texttt{hypotenuse} is declared, it will be initialized with the appropriate value. Finally, because we have declared three \texttt{val} fields, there is no need for the getter methods which are so common in Java code; since a \texttt{val} is immutable, we do not have to worry about other code changing these fields and it is safe to make them public (which is the default in Scala).

An instance of the \texttt{Triangle} class is created just as in Java: \texttt{new Triangle(3.0, 4.0)} instantiates the class with the given constructor arguments, producing a \texttt{Triangle} object with a hypotenuse of \texttt{5.0}.

One aspect of a Scala class that is significantly different from Java is the notion of a static member. In Java, static members are shared among all instances of the class. Scala does not use this concept; instead, it has the notion of a ``companion object.'' First of all, Scala supports the definition of ``one-off'' objects---singletons, which are the only instance of their class. An example of this is the \texttt{math} object, which we may imagine being defined as follows:
\begin{verbatim}
object math {
  def max(a: Double, b: Double): Double = if (a > b) a else b
  def sqrt(x: Double): Double = ...
  ...
}
\end{verbatim}
This directly declares an object with the given methods; there is no need to instantiate it, because there will never be more than the one \texttt{math} object. In Java, this corresponds to the \texttt{Math} class having only static members, which are called through the class name (\texttt{Math.sqrt}) rather than through an instance.

Now suppose we want to have a class of objects with a shared static member. A common example of this is a ``factory'' method: a method that hides the details of constructing an object (often because the actual object returned will belong to a subclass that the client might not know about). In our \texttt{Triangle} class, suppose the Java version had the following method:
\begin{verbatim}
// THIS IS JAVA
public static Triangle makeIsoceles(double width) {
  return new Triangle(width, width);
}
\end{verbatim}
In Scala, we would put this method in the ``companion'' of the \texttt{Triangle} class, which is an object also named \texttt{Triangle}:
\begin{verbatim}
object Triangle {
  def makeIsoceles(width: Double): Triangle = new Triangle(width, width)
}
\end{verbatim}
In both cases, we may call \texttt{Triangle.makeIsoceles(1.0)} to produce a new triangle with width and height both 1; the advantage in Scala is that there is a cleaner separation between the operations supported by the entire class and the operations supported by an \emph{instance} of the class.

\subsection{Traits and Algebraic Data Types}
One more difference between Scala and Java is largely a matter of terminology, as far as it will matter to us. Where Java has ``interfaces'' that may be ``implemented'' by several classes, Scala has ``traits'' that may be ``extended'' by several classes. The effect is the same, although there are some more advanced features of traits that are difficult to achieve in Java.

For our purposes, traits are needed as part of the mechanism for defining ``algebraic data types.'' An algebraic data type is one where there may be several ways to construct simple values, and there may be several ways to build larger, more complex values out of smaller ones. The canonical examples here are lists and trees: starting from empty lists and empty trees or individual leaves, we may grow larger structures by adding values to the front of the list, or adding new parent nodes above some number of children.

Here is the definition of the algebraic data type of expression trees, which are either leaves with a numeric value or nodes with an operator and two expression trees as operands:
\begin{verbatim}
sealed trait Expr
case class Leaf(value: Double) extends Expr
case class Node(operator: String, left: Expr, right: Expr) extends Expr
\end{verbatim}
The first line declares the trait \texttt{Expr}, which is the base type being defined. The keyword \texttt{sealed} indicates that the following lines will specify \emph{all} of the ways in which values of this type may be created (unlike the situation with a Java interface, which may have new implementations supplied at any time). The remaining two lines give class definitions (with no bodies, because they have no non-standard methods) for the \texttt{Leaf} and \texttt{Node} classes of expression trees. The clause \texttt{extends Expr} on each declares that they are defining specific kinds of \texttt{Expr} values (just like saying \texttt{implements} in Java).

The keyword \texttt{case} at the front of each tells the Scala compiler to generate some extra code in these classes to support convenient object creation and pattern matching (for example, it creates a factory method with the name of the case class, so we may construct new objects without explicitly saying ``\texttt{new}''). It also turns each of the constructor parameters into \texttt{val}s, thus making them available as fields. Note that, because the \texttt{Expr} trait is \texttt{sealed}, we know that a pattern-match on expression trees only has to cover these two cases, either \texttt{Leaf} or \texttt{Node}.

The effect of this is that we may construct an expression tree by writing code such as \texttt{Node("+", Leaf(27), Node("*", Leaf(3), Leaf(5)))}, and we may use pattern matching to pull a tree back apart:
\begin{verbatim}
def eval(e: Expr): Double = e match {
  case Leaf(v) => v
  case Node("+", left, right) => eval(left) + eval(right)
  case Node("-", left, right) => eval(left) - eval(right)
  case Node("*", left, right) => eval(left) * eval(right)
  case Node("/", left, right) => eval(left) / eval(right)
  case Node(op, _, _) => sys.error("Invalid operator: " + op)
}
\end{verbatim}
Here is a trace of the execution of this function:
\begin{verbatim}
eval(Node("+", Leaf(27), Node("*", Leaf(3), Leaf(5))))
= eval(Leaf(27)) + eval(Node("*", Leaf(3), Leaf(5)))
= 27 + (eval(Leaf(3)) * eval(Leaf(5)))
= 27 + (3 * 5)
= 27 + 15
= 42
\end{verbatim}

Now we can see that the standard list type is an algebraic data type. We could define our own version as follows (specialized to integers for simplicity):
\begin{verbatim}
sealed trait MyList
case object Nil extends MyList
case class Cons(head: Int, tail: MyList) extends MyList
\end{verbatim}
The only new wrinkle here is the \texttt{case object}, but this should make sense, since there will only ever be the one empty list (it doesn't contain any mutable data, so there is no reason not to share it with every list). The real definition of the Scala list type involves a parameterized type in place of \texttt{Int}, plus some operator magic to allow the \texttt{Cons} operation to be called \texttt{::}, but this is the essence of it.

The final point to emphasize about these algebraic data types is that they are constructed recursively, and the natural way to define functions over them is to follow that recursion; each case class generally turns into a case in a match expression, with recursive calls corresponding to the places where smaller structures are included. The natural way to prove anything about such a type is to use structural induction, where the base cases are for the simple constructors and the induction steps are for the compound constructors. In a sense, all of these notions are equivalent ways of looking at the same thing.

\subsection*{Exercises}
\begin{enumerate}
\item Define a function \verb|minmax(a: Int, b: Int)| that returns a pair of numbers, where the first is the smaller of \texttt{a} and \texttt{b}, and the second is the larger. For example, \verb|minmax(17, 42)| and \verb|minmax(42, 17)| should both return the pair \verb|(17, 42)|.

\item Using the \texttt{minmax} function, define a function \verb|sort3(a: Int, b: Int, c: Int)| that returns a triple of numbers which are \texttt{a}, \texttt{b}, and \texttt{c} in non-decreasing order. For example, \verb|sort3(3, 1, 4)| should return the triple \verb|(1, 3, 4)|. \textit{Hint:} Use \texttt{minmax} to find the correct order of \texttt{a} and \texttt{b}, then use it two more times to place \texttt{c} in the correct relative position among variables \texttt{small}, \texttt{middle}, and \texttt{large}, then simply return the triple \verb|(small, middle, large)|.

\item Define a class \texttt{Person} with fields \texttt{givenName}, \texttt{familyName}, and \texttt{birthYear}. Define a computed \texttt{fullName} field that contains a string in the form \verb|"Howard, Brian"| (family name first), and define a method \verb|age(year: Int)| that returns the person's age on their birthday in the given year. Try to make this class definition as short as you can, taking advantage of Scala's boilerplate-reduction features. Finally, define a function that takes two \texttt{Person} objects and returns the older person.

\item Try to define a function on expression trees that counts the number of leaves. That is, \verb|numLeaves(Node("+", Leaf(27), Node("*", Leaf(3), Leaf(5))))| should return \texttt{3}. \textit{Hint:} Define it using a pattern match.
\end{enumerate}

