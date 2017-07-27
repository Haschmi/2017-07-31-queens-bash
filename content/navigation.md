---
title: "Navigation and exploration"
menu: main
weight: 3
---


## Learning Objectives
*   Learn how to navigate around directories and look at their contents
*   Explain the similarities and differences between a file and a directory.
*   Translate an absolute path into a relative path and vice versa.
*   Construct absolute and relative paths that identify specific files and directories.
*   Explain the steps in the shell's read-run-print cycle.
*   Identify the actual command, flags, and filenames in a command-line call.
*   Demonstrate the use of tab completion, and explain its advantages.

At the point in this lesson, we've just logged into our shell. Nothing has happened yet, and we're not going to be able to do anything until we learn a few basic commands. By the end of this lesson, you will know how to "move around" the system and look at what's there.

Right now, all we see is something that looks like this:

~~~ {.bash}
$
~~~

The dollar sign is a **prompt**, which shows us that the shell is waiting for input;
your shell may use a different character as a prompt and may add information before
the prompt. When typing commands, either from these lessons or from other sources,
do not type the prompt, only the commands that follow it.

Type the command `whoami`, then press the Enter key (sometimes marked Return) to send the command to the shell. The command's output is the ID of the current user, i.e., it shows us who the shell thinks we are:

~~~ {.bash}
$ whoami
~~~
~~~ {.output}
hasch
~~~

More specifically, when we type `whoami` the shell:

1.  finds a program called `whoami`,
2.  runs that program,
3.  displays that program's output, then
4.  displays a new prompt to tell us that it's ready for more commands.

Next,
let's find out where we are by running a command called `pwd` (which stands for "print working directory"). At any moment, our **current working directory** (where we are) is the directory that the computer assumes we want to run commands in unless we explicitly specify something else.
Here, the computer's response is `/global/home/hasch`, which is Hartmut's **home directory**:

~~~ {.bash}
$ pwd
~~~
~~~ {.output}
/global/home/hasch
~~~

> ## Home directory
>
> The home directory path will look different on different operating systems.
> On Linux it will look like `/global/home/hasch`,
> and on Windows it will be similar to `C:\Documents and Settings\hasch`.
> Note that it may look slightly different for different versions of Windows.

So, we know where we are. How do we look and see what's in our current directory?
```{.bash}
ls
```

`ls` prints the names of the files and directories in the current directory in alphabetical order, arranged neatly into columns.

If nothing shows up, it means that nothing's there. Let's make a directory for us to play with.

`mkdir <new directory name>` makes a new directory with that name in your current location. Notice that this command required two pieces of input: the actual name of the command (`mkdir`) and an argument that specifies the name of the directory you wish to create.

```{.bash}
mkdir Documents
```

Let's us `ls` again. What do we see?

Our folder is there, awesome. What if we wanted to go inside it and do stuff there?

We will use the `cd` (change directory) command to move around. Let's `cd` into our new Documents folder.

```{.bash}
cd Documents
pwd
```
```{.output}
~/Documents
```

Now that we know how to use `cd`, we can go anywhere. That's a lot of responsibility. What happens if we get "lost" and want to get back to where we started?

To go back to your home directory, the following two commands will work:

```{.bash}
cd /global/home/hasch
cd ~
cd $HOME
```

What is the `~` character? When using the shell, `~` is a shortcut that represents `/global/home/hasch`. $HOME is the value of an environment variable called HOME which serves the same purpose.

A quick note on the structure of a UNIX (Linux/Mac/Android/Solaris/etc) filesystem. Directories and absolute paths (i.e. exact position in the system) are always prefixed with a `/`. `/` is the "root" or base directory.

Let's go there now, look around, and then return to our home directory.
```{.bash}
cd /
ls
cd ~
```

Our "home" directory is the one where we generally want to keep all of our files. Other folders on a UNIX OS contain system files, and get modified and changed as you install new software or upgrade your OS.

> ## Using the filesystem at a Computing Centre
> If you are working on a computing centre such as the Centre for Advanced Computing, you usually have a number of places where you can store your files. These differ in both the amount of space allocated and whether or not they are backed up. 
>
> For instance at the CAC, the default allocations are:  
> **/home** (your home directory) - 500 GB, backed up  
> **/u1/work** - 2 TB, backed up  
> **/scratch** - 5 TB, NOT backed up  

There are several other useful shortcuts you should be aware of.  

+ `.` represents your current directory   

+ `..` represents the "parent" directory of your current location

+ While typing nearly *anything*, you can have bash try to autocomplete what you are typing by pressing the `tab` key.  


Let's try these out now:
```{.bash}
cd ./Documents
pwd

cd ..
pwd
```

Many commands also have multiple behaviors that you can invoke with command line 'flags.' What is a flag? It's generally just your command followed by a '-' and the name of the flag (sometimes it's '--' followed by the name of the flag. You follow the flag(s) with any additional arguments you might need.

We're going to demonstrate a couple of these "flags" using `ls`.

Show hidden files with `-a`. Hidden files are files that begin with `.`, these files will not appear otherwise, but that doesn't mean they aren't there!
```{.bash}
ls -a
```

Show files, their size in bytes, date last modified, permissions, and other things with `-l`.
```{.bash}
ls -l
```
This is a lot of information to take in at once, but we will explain this later! `ls -l` is *extremely* useful, and tells you almost everything you need to know about your files without actually looking at them.

We can also use multiple flags at the same time!
```{.bash}
ls -l -a
```

Flags generally precede any arguments passed to a UNIX command. `ls` actually takes an extra argument that specifies a directory to look into.

When you use flags and arguments together, they syntax (how it's supposed to be typed) generally looks something like this:
```{.bash}
command <flags/options> <arguments>
```

So using `ls -l` on a different directory than the one we're in would look something like:
```{.bash}
ls -l ~/Documents
```

How did I know about the `-l` and `-a` options? Is there a manual we can look at for help when we need help?

There is, in fact, a very helpful manual for most UNIX commands: `man` (if you've ever heard of a "man page" for something, this is what it is).
```{.bash}
man ls
```
You can scroll through this manual using the arrow keys. To close the manual, press the `q` to exit.
