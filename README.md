Using `jlink` to deploy native(*) Java applications
===================================================
A demo of using Java 9 modularization, and the JDK's new `jlink` tool, for zero-dependency 
deployments.  That is, application bundles suitable for native(*) execution, with the Java runtime 
embedded.

Unlike [older](http://launch4j.sourceforge.net/) 
[approaches](https://docs.oracle.com/javase/8/docs/technotes/guides/deploy/packager.html), 
which required embedding basically an entire JRE (around 200+ megs), the `jlink` tool allows you to 
strip down the embedded runtime to include only those portions of the standard library that are 
absolutely necessary.

> (*) No, this is not "native" in the C/C++ sense of AOT compilation.  There is still a Java Runtime 
 Environment executing the code.  However, the code is packaged up into a deployable bundle, with a 
 JRE and launch script native to the specific target platform (i.e. UNIX shell or Windows batch).  No 
 other software needs to be installed, and the end user experience is no different from an application 
 shipping with Qt or GTK shared libs.

Results
-------
This repository contains a Gradle multi-project setup, with two sub-projects.  One is a basic "Hello 
World" command-line application, and the other a JavaFX app.  These are the resulting sizes of the 
native application bundles that are generated, both raw as well as after being compressed with 7-zip, 
on a 64-bit Windows system (with Electron for comparison):

App   | Compressed with 7-zip | Raw Size when deployed
----- | --------------------- | ----------------------
`cli` (command-line application) | 10.8 MB | 21.7 MB               
`gui` (JavaFX desktop application) | 29.1 MB | 45.8 MB
[electron-quick-start](https://github.com/electron/electron-quick-start) example | 32.8 MB | 131 MB

How to Build
------------
To run these Gradle commands, your system must have a `JAVA_HOME` environment variable pointing to a 
Java 9+ location (the JDK subdirectory, not the JRE one).

`gradlew linkAll` 

To build the command-line version or the JavaFX version separately, use:

`gradlew cli:link`

`gradlew gui:link`

You can find the output under the `cli/build/dist` and/or `gui/build/dist` subdirectories.  

Each bundle will have a `/bin` subdirectory, containing UNIX shell scripts or Windows batch files with 
the same name as the subproject (e.g. `cli`, `gui.bat`).

The contents of either `dist` subdirectory can be copied and run anywhere (assuming the same platform 
type as the machine on which the bundle was made).  You could also use any installer tool of your 
choice to make a one-click installer artifact.
