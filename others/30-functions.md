# Unit 30: Side Effect-Free Programming

## Learning Objectives

After this lecture, students should be familiar with:

- the concept of functions as side-effect-free programming constructs and its relation to functions in mathematics.
- understand the importance of writing code that is free of side effects
- how functions can be first-class citizens in Java through using local anonymous class
- how we can succinctly use a lambda expression or a method reference in place of using local anonymous class
- how we can use currying to generalize to functions with higher arity
- how we can create a closure with lambda and environment capture

## Functions

Recall that, a function, in mathematics, refers to a mapping from a set of inputs (_domain_) $X$ to a set of output values (_codomain_) $Y$.  We write $f: X \rightarrow Y$.  Every input in the domain must map to exactly one output but multiple inputs can map to the same output.  Not all values in the codomain need to be mapped.  

We know how to deal with mathematical functions very well.  There are certain rules that we follow when we reason about functions.  For instance, suppose we have an unknown $x$ and a function $f$, we know that applying $f$ on $x$, i.e., $f(x)$ does not change the value of $x$, or any other unknowns $y$, $z$, etc.  We say that mathematical functions have _no side effects_.  It simply computes and returns the value.

Another property of mathematical function is _referential transparency_.  Let $f(x) = a$.  Then in every formula that $f(x)$ appears in, we can safely replace occurances of $f(x)$ with $a$.  Conversely, everywhere $a$ appears, we can replace it with $f(x)$.  We can be guarantee that the resulting formulas are still equivalent.

These two fundamental properties of mathematical functions allow us to solve equations, prove theorems, and reason about mathematical objects rigorously.

Unfortunately, we can't always reason about our program the same way as we reason about mathematical functions.  For instance, consider the line:
```
a.get(0)
```

where `a` is an instance of `Array<T>`.  Suppose we know that `a.get(0)` is 5 for some `a`.  When we reason about the behavior of our code, we cannot replace (mentally) every invocation of `a.get(0)` with the value 5.  This is because the array `a` may not be immutable and therefore `a.get(0)` cannot be guaranteed to be the same.  

The reserve should be true as well.  Suppose we have a variable
```
T t = a.get(0);
```

Then everywhere in our code where we use `t`, we should be able to replace it with `a.get(0)`, and the behavior of the code should still be the same. This behavior is only guaranteed if `a.get(0)` has no side effects (such as modifying a field or print something to the standard output).

To be able to reason about our code using the mathematical reasoning techniques we are familiar with, it is important to write our code as if we are writing mathematical functions -- our methods should be free of side effects and our code should be referentially transparent.  Our program is then just a sequence of functions, chained and composed together.  To achieve this, functions need to be a _first class citizen_ in our program, so that we can assign functions to a variable, pass it as parameters, return a function from another function, etc, just like an integer.

# Pure Functions

Ideally, methods in our programs should behave the same as functions in mathematics.  Given an input, the function computes and returns an output.  A _pure_ function does nothing else -- it does not print to the screen, write to files, throw exceptions, change other variables, modify the values of the arguments.  That is, a pure function does not cause any _side effect_.  

Here are two examples of pure functions:

```Java
int square(int i) {
  return i * i;
}

int add(int i, int j) {
  return i + j;
}
```

and some examples of non-pure functions:
```Java
int div(int i, int j) {
  return i / j;  // may throw an exception
}

int incrCount(int i) {
  return this.count + i; // assume that count is not final.
                         // this may give diff results for the same i.
}

void incrCount(int i) {
  this.count += i; // does not return a value
                   // and has side effects on count
}

int addToQ(Queue<Integer> queue, int i) {
  queue.enq(i);  // has side effects on queue
}
```

A pure function must also be deterministic.  Given the same input, the function must produce the same output, _every single time_.  This deterministic property ensures referential transparency.

In the OO paradigm, we commonly need to write methods that update the fields of an instance or compute values using the fields of an instance.  Such methods are not pure functions.  On the other hand, if our class is immutable, then its methods must not have side effects and thus is pure.

In computer science, we refer to the style of programming where we build a program from pure functions as _functional programming_ (FP). Examples of functional programming languages include Haskell, OCaml, Erlang, Clojure, F#, and Elixir.

Many modern programming languages, such as Java, C++, Python, Rust, Swift, support this style of programming.  As these languages are not designed to be functional, we cannot build a program from only pure functions.  Java, for instance, is still an OO language at its core.  As such, we will refer to this style as _functional-style programming_.  We won't be able to write code consists of only pure functions in Java, but we can write methods that has no side effects and objects that are immutable, as much as possible.

## Function as First-Class Citizen in Java

Let's explore functions as a first-class citizen in Java.  We have seen some examples of this when we use the `Comparator` interface.

```Java
void sortNames(List<String> names) {
	Comparator<String> cmp = new Comparator<String>() {
	  public int compare(String s1, String s2) {
		return s1.length() - s2.length();
	  }
	};
	names.sort(cmp);
}
```

First, let's take a moment to appreciate the beauty of the `List::sort` method.  We can use this method to sort items of _any type_, in _any defined order_.  We achieve the generality of types with generics, and the generality of sorting order through passing in the comparison function as a parameter.  The latter the need to write one sorting method for every possible sorting order for a list of strings, (`sortAlphabeticallyIncreasing`, `sortByLengthDecreasing`, etc..)

The comparison function here is implemented _as a method in an anonymous class that implements an interface_.  We can think of an instance of this anonymous class as the function.  Since a function is now just an instance of an object in Java, we can pass it around, return it from a function, and assign it to a variable, just like any other reference type.

Let's look at another example.  Consider the following interface:
```Java
interface Transformer<T, R> {
  R transform(T t);
}
```

`Transformer<T, R>` is a generic interface with two type parameters: `T` is the type of the input, `R` is the type of the result.  It has one abstract method `R transform(T t)` that applies the function to a given argument.

We can use this interface to write any function that takes in a value and return another value.  (Java has a similar interface called, unsurprisingly, `[java.util.function.Function<T, R>](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/function/Function.html)`). For instance, a function that computes the square of an integer can be written as:
```Java
new Transformer<Integer, Integer>() {
  @Override
  public Integer transform(Integer x) {
    return x * x;
  }
};
```

We can write a method `chain` that composes two given computations together and
return the new computation:
```Java
// Use of PECS left as an exercise
<T, R, S> Transformer<T,R> chain(Transformer<T,S> t1, Transformer<S,R> t2) {
  return new Transformer<T,R>() {
	public R transform(T value) {
	  return t2.transform(t1.transform(value));
	}
  }
}
```

## Lambda Expression

While we have achieved functions as first-class citizens in Java, the code is verbose and ugly.  Fortunately, there is a much cleaner syntax to write functions that applies to interfaces with a single abstract method.

An interface in Java with only one abstract method is called a _functional interface_.  Both `Comparator` and `Transformer` are functional interfaces.  It is recommended that, if a programmer intends an interface to be a functional interface, they should annotate the interface with the `@FunctionalInterface` annotation.

```Java
@FunctionalInterface
interface Transformer<T, R> {
  R transform(T t);
}
```

A key advantage of a functional interface is that there is no ambiguity about which method is being overridden by an implementing subclass.

For instance, consider:
```Java
Transformer<Integer, Integer> square = new Transformer<>() {
  @Override
  public Integer transform(Integer x) {
    return x * x;
  }
};
Transformer<Integer, Integer> incr = new Transformer<>() {
  @Override
  public Integer transform(Integer x) {
    return x + 1;
  }
};
```

You can see that there is much boilerplate code in the two functions above.  Since we are assigning it to a variable of type`Transformer` interface, we don't have to write `new Transformer<>() { .. }`.  Since there is only one abstract method to overwrite, we don't have to write `@Override public Integer transform(..) { .. }`.
What remain after we eliminate the obvious and biolerplate are (i) the parameter `Integer x` and (ii) the body of `transform`, which is `{ return x * x; }`.  

We can use the Java arrow notation `->` to now link the parameter and the body:
```Java
Transformer<Integer, Integer> square = (Integer x) -> { return x * x; };
Transformer<Integer, Integer> incr = (Integer x) -> { return x + 1; };
```

You might notice that the type of the parameter is redundant as well since the type argument to `Transformer` already tells us this function takes in an `Integer`. We can further simplify it to:
```Java
Transformer<Integer, Integer> square = (x) -> { return x * x; };
Transformer<Integer, Integer> incr = (x) -> { return x + 1; };
```

or simply:
```Java
Transformer<Integer, Integer> square = x -> { return x * x; };
Transformer<Integer, Integer> incr = x -> { return x + 1; };
```

where there is only one parameter.

Since the body has only a single return statement, we can simplify it further:
```Java
Transformer<Integer, Integer> square = x -> x * x;
Transformer<Integer, Integer> incr = x -> x + 1;
```

Now, that's much better!

The expressions above, including `x -> x * x`, are called _lambda expressions_.  You can recognize one by the use of `->`.   The left-hand side lists the parameters (use `()` if there is no parameter), while the right-hand side is the computation.  We do not need the type in cases where Java can infer the type, nor need the return keyword and the curly brackets when there is only a single return statement.

!!! note "lambda"
    Alonzo Church invented lambda calculus ($\lambda$-calculus) in 1936, before electronic computers, as a way to express computation.  In $\lambda$-calculus, all functions are anonymous.  The term lambda expression originated from there.

### Method Reference

Lambda expression is useful for specifying a new anonymous method.  Sometimes, we want to use an existing method as a first-class citizen instead.

Recall the `distanceTo` method in `Point`, which takes in another point as a parameter and returns the distance between this point and the given point.

```Java
class Point {
	:
	public double distanceTo(Point p) {
		:
	}
}
```

We can write our `Transformer` like this using an anonymous class:
```Java
Point origin = new Point(0, 0);
Transformer<Point, Double> dist = new Transformer<>() {
	@Override
	public Double transform(Point p) {
		return origin.distanceTo(p);
	}
}
```

or using a lambda expression:
```Java
Point origin = new Point(0, 0);
Transformer<Point, Double> dist = p -> origin.distanceTo(p);
```

but since `distanceTo` takes in one parameter and returns a value, it already fits as a transformer, and we can write it as:
```Java
Point origin = new Point(0, 0);
Transformer<Point, Double> dist = origin::distanceTo;
```

The double-colon notation `::` is used to specify a _method reference_.  We can use method references to refer to a (i) static method in a class, (ii) instance method of a class or interface, (iii) constructor of a class.  Here are some examples (and their equivalent lambda expression)

```Java
Box::of            // x -> Box.of(x)
Box::new           // x -> new Box(x)
x::compareTo       // y -> x.compareTo(y)
A::foo             // (x, y) -> x.foo(y) or (x, y) -> A.foo(x,y)
```

The last example shows that the same method reference expression can be interpreted in two different ways.  The actual interpretation depends on how many parameters `foo` takes and whether `foo` is a class method or an instance method.  When compiling, Java searches for the matching method, performing type inferences to find the method that matches the given method reference.  A compilation error will be thrown if there are multiple matches or if there is ambiguity in which method matches.

## Curried Functions

Mathematically, a function takes in only one value and returns one value (e.g., `square` above).  In programming, we often need to write functions that take in more than one argument (e.g., `add` above).
Even though `Transformer` only supports function with a single parameter, we can build functions that take in multiple arguments.  Let's look at this mathematically first.  Consider a binary function $f: (X, Y) \rightarrow Z$.  We can introduce $F$ as a set of all functions $f': Y \rightarrow Z$, and rewrite $f$ as $f: X \rightarrow F$, of $f: X \rightarrow Y \rightarrow Z$.

A trivial example of this is the `add` method that adds two `int` values.
```Java
int add(int x, int y) {
  return x + y;
}
```

This can be written as
```Java
Transformer<Integer, Transformer<Integer, Integer>> add = x -> y -> (x + y);
```

To calculate 1 + 1, we call
```Java
add.transform(1).transform(1);
```

Let's break it down a little, `add` is a function that takes in an `Integer` object and returns a unary `Function` over `Integer`.  So `add.transform(1)` returns the function `y -> 1 + y`.  We could assign this to a variable:
```Java
Transformer<Integer,Integer> incr = add.transform(1);
```

Note that `add` is no longer a function that takes two arguments and returns a value.  It is a _higher-order function_ that takes in a single argument and returns another function.

The technique that translates a general $n$-ary function to a sequence of $n$ unary functions is called _currying_.  After currying, we have a sequence of _curried_ functions.  

!!! note "Curry"
    Currying is not related to food but rather is named after computer scientist Haskell Curry, who popularized the technique.

How is currying useful?  Consider `add(1, 1)` -- we have to have both arguments available at the same time to compute the function.  With currying, we no longer have to.  We can evaluate the different arguments at a different time (as `incr` example above).  This feature is useful in cases where some arguments are not available until later.  We can _partially apply_ a function first.  This is also useful if one of the arguments does not change often, or is expensive to compute.  We can save the partial results as a function and continue applying later.  We can dynamically create functions as needed, save them, and invoke them later.

## Lambda as Closure

In the example, we showed earlier,
```Java
Point origin = new Point(0, 0);
Transformer<Point, Double> dist = origin::distanceTo;
```

the variable `origin` is captured by the lambda expression `dist`.  Just like in local and anonymous classes, a captured variable must be either explicitly declared as `final` or is effectively final.

A lambda expression stores more than just the function to invoke -- it also stores the data from the environment where it is defined.  We call such a construct that stores a function together with the enclosing environment a _closure_.

Being able to save the current execution environment, and then continue to compute it later, adds new power to how we can write our program.  We can make our code cleaner with fewer parameters to pass around and less duplicated code.  We can separate the logic to do different tasks in a different part of our program easier.

We will see more examples of this later.
