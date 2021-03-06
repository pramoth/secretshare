Architecture Documentation
==========================

The package dependencies in secretshare:
![Package Digraph](images/jdepend-webgraph.svg)

Some notes:
 1. com.tiemens.secretshare - looks odd as a dead-end.  This package only has BuildVersion.java, which seems like a natural place for this class.  It just makes the diagram look a bit strange.

Some To Do Items:

 1. Logger - since java.util.Logger exists now, add logging to this library.

2019/Sep - fixed\
com.tiemens.secretshare.math - has been split into com.tiemens.secretshare.math.type and com.tiemens.secretshare.math.equation.  Now, ".math.matrix" shows as a higher level than ".math.type"\
2018/Oct - fixed\
com.tiemens.secretshare.main.test - contains only "TicketTwoMain.java" (ticket two is a reference to the second GitHub issue).  The name ".main.test" is incorrect - it should be ".main.example"\
2018/Oct - fixed\
com.tiemens.secretshare.math.matrix - missing link to ".exceptions", reveals an implementation flaw in NumberMatrix.java.  There are "throw new ArithmeticException()" calls that should be "throw new SecretShareException()", as the Architecture Goals state.

Code
====
See [JdepsTest.java](../src/test/java/com/tiemens/secretshare/JdepsTest.java) for a very easy-to-configure test of package dependencies.


Command Line
====
```
$ ./gradlew clean build
$ jdeps build/classes/java/main | grep -v ' java.'
```

<details>
   <summary>Sample output from jdeps</summary>

   ```
com.tiemens.secretshare
 [file:///home/tim/workspace/secretshare/build/classes/java/main/]
   com.tiemens.secretshare.engine                     -> com.tiemens.secretshare.exceptions                 com.tiemens.secretshare
   com.tiemens.secretshare.engine                     -> com.tiemens.secretshare.math.combination           com.tiemens.secretshare
   com.tiemens.secretshare.engine                     -> com.tiemens.secretshare.math.equation              com.tiemens.secretshare
   com.tiemens.secretshare.engine                     -> com.tiemens.secretshare.math.matrix                com.tiemens.secretshare
   com.tiemens.secretshare.engine                     -> com.tiemens.secretshare.math.type                  com.tiemens.secretshare
   com.tiemens.secretshare.main.cli                   -> com.tiemens.secretshare                            com.tiemens.secretshare
   com.tiemens.secretshare.main.cli                   -> com.tiemens.secretshare.engine                     com.tiemens.secretshare
   com.tiemens.secretshare.main.cli                   -> com.tiemens.secretshare.exceptions                 com.tiemens.secretshare
   com.tiemens.secretshare.main.cli                   -> com.tiemens.secretshare.math.type                  com.tiemens.secretshare
   com.tiemens.secretshare.main.example               -> com.tiemens.secretshare                            com.tiemens.secretshare
   com.tiemens.secretshare.main.example               -> com.tiemens.secretshare.engine                     com.tiemens.secretshare
   com.tiemens.secretshare.math.combination           -> com.tiemens.secretshare.exceptions                 com.tiemens.secretshare
   com.tiemens.secretshare.math.equation              -> com.tiemens.secretshare.exceptions                 com.tiemens.secretshare
   com.tiemens.secretshare.math.matrix                -> com.tiemens.secretshare.exceptions                 com.tiemens.secretshare
   com.tiemens.secretshare.math.matrix                -> com.tiemens.secretshare.math.type                  com.tiemens.secretshare
   com.tiemens.secretshare.math.type                  -> com.tiemens.secretshare.exceptions                 com.tiemens.secretshare
   com.tiemens.secretshare.math.type                  -> com.tiemens.secretshare.md5sum                     com.tiemens.secretshare
   com.tiemens.secretshare.md5sum                     -> com.tiemens.secretshare.exceptions                 com.tiemens.secretshare
   ```
</details>



Architecture Goals
==================
 1. Single "library" exception SecretShareException\
   This allows callers to easily isolate SecretShare errors.
   Especially useful for [N-version programming](https://en.wikipedia.org/wiki/N-version_programming) where SecretShare is one of the implementation choices, and the goal is to ignore SecretShare-specific exceptions without having to use "catch (Exception e)".  This goal is important because there are N-1 other implementations, and 1 implementation should not halt processing, but neither should the system be over-capturing and ignoring potential (runtime) Exceptions.
 2.  No compile dependencies on 3rd-party libraries\
   Because 3rd-party libraries hinder use of a library, this library shall have no 3rd-party dependencies for normal use.
   Test dependencies are allowed (JUnit, jdeps)
 3.  Logging\
   Because of "No compile dependencies", there is very little logging implemented in this library.
   Because this library pre-dates java.util.logging.Logger, it does not use this class.
   However, as an architectural goal, it should be refactored to use java.util.logging.Logger

