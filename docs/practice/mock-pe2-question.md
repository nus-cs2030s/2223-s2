# CS2030S MOCK PRACTICAL ASSESSMENT II
# AY2022/23 Semester 2

# INSTRUCTIONS
--------------


1. Accept the practice question [here](https://classroom.github.com/a/ukhfX20W) 

2. Log into the PE nodes and run `~cs2030s/get-mockpe2` to get the practice question.

3. There is no submission script

4. You should see the following in your home directory.
      - The files `Test1.java` and `CS2030STest.java` for testing your solution.
      - The skeleton files for the question: `Main.java`
      - The following files to solve the question are provided `Circle.java`, and `Point.java`
      - The file `StreamAPI.md` contains information about the `Stream` class from the `java.util.stream` package.
     
5. Solve the programming tasks by editing `Main.java`. 

# Streams

## Marking Criteria

- Functionality and type correctness (12 marks)
- Style (3 marks)

## Your Task

There are four parts to this question that may or may not be dependent on each other. You will need to write four single line `Stream` pipelines to generate certain `Stream`s and solve certain computations. The last part of this question will get you to re-solve the Lab 0 (Pi Estimation) using only a `Stream` and some supporting classes.

The Stream API is included in the file `StreamAPI.md`.

You are provided with a `Point`, `Circle`, and `Main` class. These `Point`, and `Circle` classes are similar to those used in Lab 0.  Take a look at them to see what are the methods available.

All of your single line pipelines will be written in the `Main.java` skeleton file. Each method body must contain only a single return statement.

## Implement the `pointStream` method.

The method `pointStream` has two arguments: `point` of type `Point` and `f` of type `Function<Point,Point>`.  Recall that `Function` is the Java equivalent of our `Transformer` functional interface which has the single abstract method `apply` instead of `transform`.  The method should return a `Stream<Point>` which contains the point `p`, followed by `f(p)`, and then `f(f(p))`, and so on.  Implement this method body using a single stream pipeline.

Some examples of use are shown below:

```
jshell> pointStream(new Point(0, 0), p -> new Point(p.getX(), p.getY() + 1)).limit(3).forEach(System.out::println)
(0.0, 0.0)
(0.0, 1.0)
(0.0, 2.0)

jshell> pointStream(new Point(0, 0), p -> new Point(p.getX() + 1, p.getY())).limit(3).forEach(System.out::println)
(0.0, 0.0)
(1.0, 0.0)
(2.0, 0.0)

jshell> pointStream(new Point(0, 0), p -> new Point(p.getX() + 1, p.getY() + 1)).limit(3).forEach(System.out::println)
(0.0, 0.0)
(1.0, 1.0)
(2.0, 2.0)
```

You can test your code by running the `Test1.java` provided.  Make sure your code follows the CS2030S Java style.

```
$ javac Test1.java
$ java Test1
$ java -jar ~cs2030s/bin/checkstyle.jar -c ~cs2030s/bin/cs2030_checks.xml Main.java
```

## Implement the `generateGrid` method.

The method `generateGrid` has two arguments: `point` of type `Point` and `n` which is of type `int`. This method should return a finite stream of type `Stream<Point>` containing the `n * n` points that define a grid starting from the point `point` and then incrementing both `x` and `y` cordinates by one. For example: a grid of size `3` starting from a point `(0,0)` should look like the following:
```
(0,0) (0,1) (0,2)
(1,0) (1,1) (1,2)
(2,0) (2,1) (2,2)
```

When in the stream they should appear in the order of the row first i.e. `(0,0) (0,1) (0,2) (1,0) (1,1) (1,2) (2,0) (2,1) (2,2)`. 

Implement this method body using a single stream pipeline.

Some examples of use are shown below:
```
jshell> generateGrid(new Point(0, 0), 2).forEach(System.out::println)
(0.0, 0.0)
(0.0, 1.0)
(1.0, 0.0)
(1.0, 1.0)

jshell> generateGrid(new Point(0, 0), 3).forEach(System.out::println)
(0.0, 0.0)
(0.0, 1.0)
(0.0, 2.0)
(1.0, 0.0)
(1.0, 1.0)
(1.0, 2.0)
(2.0, 0.0)
(2.0, 1.0)
(2.0, 2.0)

jshell> generateGrid(new Point(-1, 0), 2).forEach(System.out::println)
(-1.0, 0.0)
(-1.0, 1.0)
(0.0, 0.0)
(0.0, 1.0)
```

You can test your code by running the `Test1.java` provided.  Make sure your code follows the CS2030S Java style.

```
$ javac Test1.java
$ java Test1
$ java -jar ~cs2030s/bin/checkstyle.jar -c ~cs2030s/bin/cs2030_checks.xml Main.java
```

## Implement the `concentricCircles` method.

The method `concentricCircles` has two arguments: `circle` of type `Circle` and `f` which is of type `Function<Double,Double>`.  The method should return a `Stream<Circle>` which contains the first circle `circle`, followed by the circle with a radius given by `f(circle.getRadius())`, and then `f(f(circle.getRadius())`, and so on. In this way, we will have a stream of concentric circles (circles with a common center but with different radii - much like a target in archery).

Implement this method body using a single stream pipeline.

Some examples of use are shown below:
```
jshell> concentricCircles(new Circle(new Point(1, 1), 1.0),x -> x + 1).limit(3).forEach(System.out::println)
{ center: (1.0, 1.0), radius: 1.0 }
{ center: (1.0, 1.0), radius: 2.0 }
{ center: (1.0, 1.0), radius: 3.0 }

jshell> concentricCircles(new Circle(new Point(0, 0), 1.0),x -> x + 0.5).limit(3).forEach(System.out::println)
{ center: (0.0, 0.0), radius: 1.0 }
{ center: (0.0, 0.0), radius: 1.5 }
{ center: (0.0, 0.0), radius: 2.0 }
```

You can test your code by running the `Test1.java` provided.  Make sure your code follows the CS2030S Java style.

```
$ javac Test1.java
$ java Test1
$ java -jar ~cs2030s/bin/checkstyle.jar -c ~cs2030s/bin/cs2030_checks.xml Main.java
```

## Implement the `pointStreamFromCircle` method.

The method `pointStreamFromCircle` has one argument: `circles` of type `Stream<Circle>`. The method should return a `Stream<Point>` which contains the centers of all the circles in the `circles` list. Implement this method body using a single stream pipeline.

An example of use is shown below:
```
jshell> pointStreamFromCircle(Stream.of(new Circle(new Point(0, 0), 1), new Circle(new Point(1, 1), 2), new Circle(new Point(-1, -1), 1))).forEach(System.out::println)
(0.0, 0.0)
(1.0, 1.0)
(-1.0, -1.0)
```

You can test your code by running the `Test1.java` provided.  Make sure your code follows the CS2030S Java style.

```
$ javac Test1.java
$ java Test1
$ java -jar ~cs2030s/bin/checkstyle.jar -c ~cs2030s/bin/cs2030_checks.xml Main.java
```