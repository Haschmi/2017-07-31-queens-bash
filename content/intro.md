---
title: "Introduction"
menu: main
weight: 2
---

## Learning Objectives

*   Explain how the shell relates to the keyboard, the screen, the operating system, and users' programs.
*   Explain when and why command-line interfaces should be used instead of graphical interfaces.

At a high level, computers do four things:

-   run programs (applications)
-   store data
-   communicate with each other
-   interact with us

Until very recently, the only way to interact with a computer was by typing input and receiving printed text output. This kind of interface is called a
**command-line interface**, or CLI, to distinguish it from a
**graphical user interface**, or GUI, which most people now use.
The heart of a CLI is a **read-evaluate-print loop**, or REPL:
when the user types a command and then presses the enter (or return) key,
the computer reads it, executes it,
and prints its output.
The user then types another command,
and so on until the user logs off.

This description makes it sound as though the user sends commands directly to the computer,
and the computer sends output directly to the user.
In fact,
there is usually a program in between called a
**command shell**.
What the user types goes into the shell,
which then figures out what commands to run and orders the computer to execute them. Note, the shell is called *the shell* because it encloses the operating system in order to hide some of its complexity and make it simpler to interact with.

A shell is a program like any other. What's special about it is that its job is to run other programs rather than to do calculations itself. The most popular Unix shell is **bash**,
the **B**ourne **a**gain **sh**ell (so-called because it's derived from a shell written by Stephen Bourne --- this is what passes for wit among programmers). Bash is the default shell on most modern implementations of Unix and in most packages that provide Unix-like tools for Windows.

Using Bash or any other shell sometimes feels more like programming than like using a mouse. Commands are terse (often only a couple of characters long), their names are frequently cryptic,
and their output is lines of text rather than something visual like a graph.
On the other hand, the shell allows us to combine existing tools in powerful ways with only a few keystrokes and to set up pipelines to handle large volumes of data automatically.
In addition, the command line is often the easiest way to interact with remote machines and supercomputers.
Familiarity with the shell is near essential to run a variety of specialized tools and resources including high-performance computing systems. As clusters and cloud computing systems become more popular for scientific data crunching, being able to interact with them is becoming a necessary skill. We can build on the command-line skills covered here to tackle a wide range of scientific questions and computational challenges.
