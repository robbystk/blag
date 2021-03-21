---
layout: post
title: Misadventures in Java
date: 2021-03-19 21:24 -0600
---

Warning: high sodium content

I've been having trouble finding a job.
I thought expanding my skill set might make it easier,
so I decided to try learning Java.
The syntax is easy enough, but one thing I had trouble with was the compilation.
Every tutorial that I found has you use an IDE
and just click the compile button,
but I like understanding how things work on a deeper level
and wanted to be able to compile my programs outside the IDE.

## Manual Compilation

It's easy enough to compile a single file.
Just run `javac file.java`, which will give you `file.class`
in the working directory.
If the class has a `main` method, you can then run it with `java <ClassName>`.
I ran into problems when working with multiple files, though.
It wasn't obvious how to handle dependencies between classes.
I found that if there's an `import` statement,
then `javac` will also compile the file that gets imported,
but it seemed odd to import classes in the same directory.

The canonical way to organize a project is to group classes into packages
with names like `com.foo.project`.
The `.` separator in the package hierarchy corresponds to
the directory structure of the source code,
so classes in the package `com.foo.project` should be located in the directory
`com/foo/project/`.
Classes in the same package can interoperate without `import` statements,
but must be compiled separately, and in the correct order.
If you have class A, that depends on B, which depends on C,
then C must be compiled first, then B, then A.
At least, that's what I thought.
It turns out you can give `javac` multiple source files--you can just
specify A, B, and C all at once, and it'll work.
It took me until I was writing this post
more than two weeks after I first started working with java to realize this.
Before then, I labored under the assumption that
each file had to be compiled separately and in the right order,
which was frustrating.

## Enterprise-grade Directory Structure

A typical project directory created by an IDE might look like this:
```
|-- bin
|-- lib
`-- src
    |-- main
    |   `-- java
    |       `-- com
    |           `-- foo
    |               `-- project
    |                    |-- BarClass.java
    |                    |-- BazClass.java
    |                    `-- MainClass.java
    `-- test
        `-- java
            `-- com
                `-- foo
                    `-- project
                         |-- BarClassTest.java
                         |-- BazClassTest.java
                         `-- MainClassTest.java
```
Compiled classes are placed in `bin`,
and any external dependencies are put in `lib`.
I'll get to dependencies a bit later.
To compile a project organized like this,
you could just run `javac`
on all the source files.
This works but will put the compiled `.class` files alongside the source files,
and compiling the tests won't work because
because the compiler won't know where to find the classes under test.
The first step to solving this is to specify
that the `.class` files should be put in `bin` with the `-d` flag.
In order to run the code, we have to change into the `bin` directory
since java looks in the working directory by default, which is inconvenient.
The tests still don't work either.
Fortunately there is a single mechanism
that will solve both of these problems--the classpath.

## The Classpath

For understanding the classpath, I found the article
[Understanding the Java Classpath: Building a Project Manually][classpath]
by Martin Gaston _extremely_ helpful.
The class `com.foo.project.BarClass`, is expected to be located in
the file `/com/foo/project/BarClass.class`.
The classpath specifies the location of the top level (`/`) of that hierarchy,
besides the working directory---in this case, `bin`.
For both `java` and `javac`, the class path can be specified with `-cp`,
so to compile, use `javac -d bin -cp bin src/ . . . /Class.java`,
and to run, use `java -cp bin com.foo.project.Class`.

[classpath]: https://dev.to/martingaston/understanding-the-java-classpath-building-a-project-manually-3c3l

The classpath is similar to the shell `$PATH`
in that multiple locations can be specified.
Just like in `$PATH`, the different locations are separated by colons (`:`).
Unlike with `$PATH`, a `.jar` file can be specified as a location in the path.
This lets us include external dependencies.
For example, if you're using a testing framework such as [JUnit],
put its jar in the `lib` directory,
and use `-cp bin:lib/testing-framework-1.2.3.jar` when compiling.

[JUnit]: https://junit.org/junit5/

This knowledge is probably all that's necessary to build 95% of java projects,
but it was a long journey to get here.
Perhaps I'm spoiled by things like [cargo] and [bundler],
but I find it frustrating that the every time I want to run the tests,
three commands are necessary:
```shell
javac -d bin src/main/java/com/foo/project/*.java
javac -d bin -cp bin:lib/junit-platform-console-standalone-1.4.2.jar src/test/java/com/foo/project/*.java
java -jar lib/junit-platform-console-standalone-1.4.2.jar -cp bin --scan-class-path
```
That seems like a lot of complexity for such a common action,
but at least I can just put it in a script or a makefile
and forget about it.
At least, until I have to add a dependency to the classpath.

Speaking of makefiles, I made a naive [makefile for java][makefile]
from before I learned that it's not necessary to run `javac`
on each source file individually.

[cargo]: https://doc.rust-lang.org/cargo/
[bundler]: https://bundler.io/
[makefile]: https://gist.github.com/robbystk/d4dc87c48505cfd31bea921cc19f5f9a

## Dedicated Build Tools

Make or a script is an adequate initial solution,
but the lack of dependency resolution makes it less than ideal.
Surely someone has come up with a tool that makes things more convenient.
I looked into build tools for java, and found several,
including [Maven] and [Ant], so I looked at those next

[Maven]: https://maven.apache.org/
[Ant]: https://ant.apache.org/

### Maven

Maven's primary objective is "making the build process easy".
That sounds like exactly what I'm looking for.
I checked out some tutorials and that's where the trouble began.
Depending on whether you look at the [Maven in 5 Minutes] tutorial,
or the [Maven Getting Started Guide],
the (apparently) _simplest_ thing to do to get started is either:
```shell
mvn -B archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4
```
or
```shell
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

[Maven in 5 Minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[Maven Getting Started Guide]: https://maven.apache.org/guides/getting-started/index.html

o\_O

Excuse me, what?
Is that really the simplest way to start using Maven?
What's an archetype?
What's an artifact?
Why are there three different IDs?
What was all that stuff you downloaded and where did you put it?
Oh yeah, it downloads _stuff_ from _somewhere_ and puts it _somewhere_. Cool.

Well once you've typed in one of those novels
and let it finish doing its thing,
you have a nice shiny new java project,
and you can run `mvn compile` or `mvn test`.
By the way, the first time you compile, it downloads even more stuff.
It does provide a bunch of other useful ~~commands~~ phases
(they're called phases) as well.  `mvn package` makes a jar for distribution,
`mvn verify` checks everything but doesn't compile
`mvn install` installs into 'local repository' (whatever that is)
and `mvn clean` cleans up after itself.
There's also `mvn site` which generates HTML documentation for the project
(after it has finished downloading yet more stuff, obvs).

Out of curiousity I tried to locate where it put all the stuff it downloaded.
I thought maybe `/opt/maven/` (which exists),
or maybe somewhere in my home directory, like `~/.maven/` (doesn't exist)
or `~/.mvn/` (also no).
It turns out it's `~/.m2/`, obviously.

After this I was _quite_ fed up with maven.
I uninstalled it, deleted the project and `~/.m2/`
and resolved to never touch it again.

### Ant

Ant existed before maven, so I hoped maybe it might be simpler,
or at least a bit easier to get started with.
Happily, ant is basically make but with java-specific directives built in,
and it uses XML instead of a human-readable format.
It only took a few minutes with the documentation to produce
a [passable `build.xml`][build.xml]
that does everything necessary for a simple project.
The hardest part was specifying the classpaths for the various tasks,
but already having an understanding of the classpath made it easier.
Editing XML is annoying, but other than that,
everything about Ant seems straightforward and comprehensible.
I'm happy to continue using it for now.

[build.xml]: https://gist.github.com/robbystk/9727807ad3d1e765449504b1dcb62551

## Conclusion

So far, it seems like every aspect of the Java ecosystem
insists on a Rube Goldberg machine's worth of complexity
for even the simplest of tasks.
Compiling programs is just the most prominent example.
I don't know why things are this way or if anything can be done about it.
I clearly don't have any answers.
This isn't meant to be a critique--just a way for me to vent my frustration.
