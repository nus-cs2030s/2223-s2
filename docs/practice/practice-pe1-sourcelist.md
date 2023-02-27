# Practice PE1 Question: SourceList

### Adapted from PE1 of 20/21 Semester 1

## Instructions to grab Practice PE Question:

1. Accept the practice question [here](https://classroom.github.com/a/2b41EPry) 
2. Log into the PE nodes and run `~cs2030s/get-pe1-list` to get the practice question.
3. There is no submission script

You should see the following in your home directory.
   
   - The files `Test1.java`, `Test2.java`, `Test3.java` and `CS2030STest.java` for testing your solution.
   - The skeleton files for this question: `EmptyList.java`, `SourceList.java`, and `Pair.java`.
   - The following files are also provided: `IntegerToString.java` , `GreaterThanTwo.java`, `BooleanCondition.java` and `Transformer.java`

## Background

In this question, you will create a generic list using a `Pair` class which is similar to what you saw in lectures. *Note:* This generic list is different and unrelated to the `java.util.List` interface.  We will not use that here.  

We will call this generic list `SourceList` (after the Source language used in the module CS1101S).

This question also uses the `Transformer` and `BooleanCondition` interfaces from Lab 4.  The code for these two classes has been provided for you.

Remember the `Pair` class from lectures with a `first` and a `second` value. The implementation of `Pair` used in this question is different from that in the lectures, in that it has only one type parameter `T`.  Using this implementation it is possible to create a list using pairs: The generic list `SourceList` is just a `Pair` object, whose second value is either itself a `Pair` object, or an `EmptyList` object.

This chain of pairs constitutes a `SourceList`. Each chain of pairs is terminated with an `EmptyList` object.

Consider some examples:

```
jshell> SourceList<Integer> list = new EmptyList<>();
jshell> list
list ==> EmptyList

jshell> SourceList<String> list = new Pair<>("Hello", new EmptyList<>());
jshell> list
list ==> Hello, EmptyList

jshell> SourceList<Integer> list = new Pair<>(1, new Pair<>(2, new Pair<>(3, new EmptyList<>())));
jshell> list
list ==> 1, 2, 3, EmptyList

jshell> list.getFirst()
$.. ==> 1
jshell> list.getSecond().getFirst()
$.. ==> 2
jshell> list.getSecond().getSecond().getFirst()
$.. ==> 3
jshell>
```

You have been provided with the following skeletons:

- The `SourceList` interface: `SourceList.java`
- The `EmptyList` class: `EmptyList.java`
- The `Pair` class: `Pair.java`

Familiarise yourself with these files.

The `Pair::toString` method has already been implemented for you.  Read and understand how this method works by recursively calling the `toString` method of the next pair in the pair chain.  

You will now implement more methods for your `SourceList` interface in the `Pair` class.

## Implement the `length` method

An important `SourceList` operation is to calculate the length of the list.

Implement the `length` method which takes in no arguments and returns an `int` which is the length of the list.

Consider some examples:

```
jshell> SourceList<String> strList = new Pair<>("AAA", new Pair<>("AA", new Pair<>("A", new EmptyList<>())))
jshell> strList.length()
$.. ==> 3

jshell> EmptyList<Integer> intEmpty = new EmptyList<>()
jshell> intEmpty.length()
$.. ==> 0
```

## Implement the `equals` method

Implement the `equals` method which takes in an argument of type `Object` and returns a `boolean`. This equals method should return `true` when the two `SourceList` are equal, and `false` otherwise.
A `SourceList` is equal if, in all `Pairs` of the `SourceList`, the first values are equal.  All `EmptyLists` are equal.

Consider the following example:

```
jshell> Object intList1 = new Pair<>(1, new Pair<>(2, new Pair<>(3, new EmptyList<>())));
jshell> Object intList2 = new Pair<>(1, new Pair<>(2, new Pair<>(3, new EmptyList<>())));
jshell> intList1.equals(intList1)
$.. ==> true
jshell> intList1.equals(intList2)
$.. ==> true
jshell> intList1.equals(new Pair<>("1", new Pair<>("2", new Pair<>("3", new EmptyList<>()))))
$.. ==> false
jshell> intList1.equals(new EmptyList<>())
$.. ==> false
jshell> new EmptyList<Integer>().equals(new EmptyList<String>())
$.. ==> true
jshell>
```

*Note:* you may only use one `@SuppressWarnings` for this method. Nowhere else in your source code may you use any `@SuppressWarnings`.

You can test your code by running the `Test1.java` provided.  Make sure your code follows the CS2030S Java style.

```
$ javac Test1.java
$ java Test1
$ java -jar ~cs2030s/bin/checkstyle.jar -c ~cs2030s/bin/cs2030_checks.xml *.java
```

## Implement the `filter` method

Implement the `filter` method which takes in a parameter of type `BooleanCondition` and returns a new `SourceList` containing only the elements which match the `BooleanCondition`. *Note:* that this should create new pairs and not change the current `SourceList`. Consider some examples:

```
jshell> SourceList<Integer> intList = new Pair<>(1, new Pair<>(2, new Pair<>(3, new Pair<>(4, new EmptyList<>()))));
jshell> intList.filter(new GreaterThanTwo())
$.. ==> 3, 4, EmptyList

jshell> intList
intList ==> 1, 2, 3, 4, EmptyList
jshell> intList.filter(new GreaterThanTwo()) == intList
$.. ==> false

jshell> SourceList<Integer> l = intList.filter(new GreaterThanTwo())
jshell> SourceList<String> l = intList.filter(new GreaterThanTwo())
|  Error:
|  incompatible types: SourceList<java.lang.Integer> cannot be converted to SourceList<java.lang.String>
|  SourceList<String> l = intList.filter(new GreaterThanTwo());
|                         ^----------------------------------^

jshell> new EmptyList<Integer>().filter(new GreaterThanTwo())
$.. ==> EmptyList

jshell> SourceList<Integer> l = new EmptyList<Integer>().filter(new GreaterThanTwo())
jshell> SourceList<String> l = new EmptyList<Integer>().filter(new GreaterThanTwo())
|  Error:
|  incompatible types: SourceList<java.lang.Integer> cannot be converted to SourceList<java.lang.String>
|  SourceList<String> l = new EmptyList<Integer>().filter(new GreaterThanTwo());
|                         ^---------------------------------------------------^

jshell> intList.filter(new GreaterThanTwo()).filter(new GreaterThanTwo());
$.. ==> 3, 4, EmptyList
jshell> new EmptyList<Integer>().filter(new GreaterThanTwo()).filter(new GreaterThanTwo())
$.. ==> EmptyList
```

Make sure your `filter` method is as flexible as you can make it.

```
jshell> class A implements BooleanCondition<Object> {
   ...>   public boolean test(Object o) {
   ...>     return false;
   ...>   }
   ...> }
jshell> intList.filter(new A());
jshell>
```

You can test your code by running the `Test2.java` provided.  Make sure your code follows the CS2030S Java style.

```
$ javac Test2.java
$ java Test2
$ java -jar ~cs2030s/bin/checkstyle.jar -c ~cs2030s/bin/cs2030_checks.xml *.java
```

## Implement the `map` method

Implement the `map` method which takes in a parameter of type `Transformer` and returns a new `SourceList` where all elements of the new `SourceList` are the result of applying the `Transformer` to all the elements of the original `SourceList`. *Note:* that this should create new pairs and not change the current `SourceList`.

Consider the following examples:

```
jshell> SourceList<Integer> intList = new Pair<>(1, new Pair<>(2, new Pair<>(3, new Pair<>(4, new EmptyList<>()))))
jshell> intList.map(new IntegerToString())
$.. ==> "1", "2", "3", "4", EmptyList
jshell> intList
intList ==> 1, 2, 3, 4, EmptyList

jshell> SourceList<Integer> intList = new Pair<>(1, new Pair<>(2, new Pair<>(3, new Pair<>(4, new EmptyList<>()))))
jshell> SourceList<String> l = intList.map(new IntegerToString())

jshell> SourceList<Integer> l = intList.map(new IntegerToString())
|  Error:
|  incompatible types: inference variable U has incompatible bounds
|      equality constraints: java.lang.Integer
|      lower bounds: java.lang.String
|  SourceList<Integer> l = intList.map(new IntegerToString());
|                          ^--------------------------------^

jshell> new EmptyList<Integer>().map(new IntegerToString())
$.. ==> EmptyList

jshell> SourceList<String> l = new EmptyList<Integer>().map(new IntegerToString())

jshell> SourceList<Integer> l = new EmptyList<Integer>().map(new IntegerToString())
|  Error:
|  incompatible types: inference variable U has incompatible bounds
|      equality constraints: java.lang.Integer
|      lower bounds: java.lang.String
|  SourceList<Integer> l = new EmptyList<Integer>().map(new IntegerToString());
|                          ^-------------------------------------------------^
```

Make sure your `map` method is as flexible as you can make it.

```
jshell> class A implements Transformer<Object,Integer> {
   ...>   public Integer transform(Object o) {
   ...>     return 0;
   ...>   }
   ...> }
jshell> SourceList<Object> l = strList.map(new A());
jshell>
```

### Implement a `StringToLength` class

This class should implement the `Transformer` interface. The method of the class should take in a `String` and return an `Integer` which is the length of the `String`.

*Hint:* The` java.lang.String ``length()` method may come in handy.

Consider the following example:

```
jshell> SourceList<String> strList = new Pair<>("AA", new Pair<>("A", new Pair<>("", new EmptyList<>())));
jshell> strList.map(new StringToLength())
$.. ==> 2, 1, 0, EmptyList

jshell> SourceList<Integer> l = strList.map(new StringToLength())
jshell> SourceList<String> l = strList.map(new StringToLength())
|  Error:
|  incompatible types: inference variable U has incompatible bounds
|      equality constraints: java.lang.String
|      lower bounds: java.lang.Integer
|  SourceList<String> l = strList.map(new StringToLength());
|                         ^-------------------------------^

jshell> SourceList<Integer> intList = new Pair<>(1, new Pair<>(2, new Pair<>(3, new Pair<>(4, new EmptyList<>()))))
jshell> intList.map(new IntegerToString()).map(new StringToLength())
$.. ==> 3, 3, 3, 3, EmptyList
jshell> new EmptyList<Integer>().map(new IntegerToString()).map(new StringToLength())
$.. ==> EmptyList
jshell> strList.map(new StringToLength()).filter(new GreaterThanTwo())
$.. ==> EmptyList
jshell> intList.filter(new GreaterThanTwo()).map(new IntegerToString())
$.. ==> "3", "4", EmptyList
```

You can test your code by running the `Test3.java` provided.  Make sure your code follows the CS2030S Java style.

```
$ javac Test3.java
$ java Test3
$ java -jar ~cs2030s/bin/checkstyle.jar -c ~cs2030s/bin/cs2030_checks.xml *.java
```