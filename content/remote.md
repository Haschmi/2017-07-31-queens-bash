---
title: "Using remote systems"
menu: main
weight: 7
---

## Learning Objectives

*   Learn how to set up a shell script for job submission
*   Learn how to select a cluster to run software on
*   What is $PATH?
*   Submit a simple job to the scheduler
*   Learn how to transfer files to and from your computer with sftp

At this point, we have learned all of the basics of using the shell. These skills will get you far on a normal UNIX system like OSX and most flavors of Linux. However, the system we are currently connected to is very different from a typical Linux install and handles big jobs somewhat differently.

Our system has a special "login" node that users can pretty much do whatever on, and a whole bunch of "production" nodes meant to do the actual work. Each node is essentially a different Linux computer, each with its own set of processors and memory. Although it's entirely possible to write a shell script to do stuff and then run it on the login node (which we've been doing so far), you won't have access to the same resources as you would on the production nodes (and it's not considered very polite to other users!). The login nodes are primarily for submitting jobs, retrieving data, short tests, and compiling software.

We will need to make several modifications to the shell scripts we have been writing in order to get them to run on the production nodes. Specifically, we will need to format our scripts for use by Sun Grid Engine, which is the job scheduler used on the CAC clusters. Although the job schedulers differ between computing organizations, the general concepts remain the same. What we are going to do is add some special commands to tell Sun Grid Engine exactly what to do with our script and what kind of resources we will need. This allows the cluster to move your job to nodes where resources are available and have your scripts play nice with others'.

## About Schedulers

The scheduler discussed here is "Sun Grid Engine" (SGE) which until recently was the one used on CAC systems. The syntax used here is quite similar to some other common work load schedulers such as "PBS".
SGE uses a set of special comments in each shell script to pass instructions to the scheduler. Each instruction is prefaced with #$, and are generally placed near the top of the script.
Let's learn a couple of these `#$` instructions. Note that you don't need to memorize these or even use all of them - what they do is specified on our wiki at [http://wiki.hpcvl.org/index.php/FAQ:SGE](http://wiki.hpcvl.org/index.php/FAQ:SGE). There is another useful reference of SGE commands at [https://kb.iu.edu/d/avgl](https://kb.iu.edu/d/avgl).

+ `#$ -S /bin/bash` - Specify our shell to SGE.
+ `#$ -cwd` - Tell SGE to start the job in the working directory that our job was submitted from.
+ `#$ -V` - pass all of our environment variables (any shell variable we've set in our current session) through to the job.
+ `#$ -N` - Assign an english name to our job (instead of just the numeric ID).
+ `#$ -o <someFile>` - Write `stdout` to a file. I usually just use something like `#$ -o $JOB_ID.o` so that every run of the same script has a unique output file.
+ `#$ -e <someFile>` - Same as `-o`, but for `stderr`.
+ `#$ -M hpcxxxx@localhost` - Specify an email address where the job scheduler can email your (generally hpcxxxx@localhost). On the next line, add something like `#$ -m be` for the scheduler to email you at the **b**eginning or **e**nd**** respectively.
+ `#$ -q abaqus.q` and `#$ -l qname=abaqus.q` ensure that your job will be scheduled on the SW (Linux cluster).
+ `#$ -pe shm.pe X` - Ask for "X" number of processors for your job.
+ `#$ -l mf=XG` - Ask for "X" gigabytes of memory for your job. It's generally a good idea to do this if you need more than 5 gigabytes.

That's a lot to take in, isn't it? Let's just create a "template job", which we can simply copy and edit for later job submissions.

```{.bash}
#!/bin/bash

# stuff you'll probably never want to change
#$ -S /bin/bash
#$ -cwd
#$ -V
#$ -M yourUserName@localhost
#$ -m be
#$ -q abaqus.q
#$ -l qname=abaqus.q

echo "The actual commands for your job go here."
```

Let's save this as `template.sh`

We can submit this job and have it get run with `qsub template.sh`. It will print out the `$JOB_ID` and tell you the job is submitted. Try this now.
If we're super fast, we can see what jobs of ours are running with `qstat -u ourUserName`. Our job may actually complete before we have the chance to run this, since `echo` should finish more or less instantaneously (if this happens we won't see any output).
We can delete a job using `qdel <JOB_ID>`, where `JOB_ID` is simply the job number we saw with `qsub` or `qstat`.

## What is $PATH, and how do we modify it?

When running our scripts before, we always had to specify the absolute path to our scripts (remember all that `./demo.sh` stuff from earlier?). Why did we have to do that for our script, whereas normal UNIX commands like `ls` or `cd` did not? This is due to a special shell variable called `$PATH`.

What is `$PATH`, exactly?
```{.bash}
echo $PATH
```
```{.output}
/opt/n1ge6/bin/lx24-amd64:/usr/sbin:/sbin:/usr/bin:/usr/lib64/qt-3.3/bin:/usr/local/bin:/bin:/usr/local/sbin
```

`$PATH` is a colon-delimited list of directories. Whenever you run *any command whatsoever* without specifying its path, your computer searches the directories on `$PATH` and runs what it finds. If the computer does not find a command on `$PATH`, it gives you the `-bash: xxxxx: command not found` error.

We often want to use software that's not on our `$PATH`. There are several ways of doing this:

+ **Specify the relative/absolute path to your script or executable** - As an example, if you had a program called `dostuff` installed in your home directory somewhere that you wanted to use, you could do so in your script with `~/path/dostuff` or `./dostuff` (if your script started in that directory).

+ **Use preintalled software** - We've installed a lot of extra software that's available as `usepackage` entries. To see what's available, type `use -l`. You'll see a bunch of entries that look like `blast - Blast current version`. To use this entry, just add `use blast` to your script. You can then call `blast` as if it were on your `$PATH` (you can try typing `blastn` now).

+ **Modify `$PATH` directly** - `$PATH` is just a list of directories separated by a colon. You can easily add a directory to this with a command like `PATH=${PATH}:/directory/you/want/to/add`. Note that using the `#$ -V` option in your scripts will pass your current `$PATH` to your job.

## Connecting to external computers with `ssh`

We actually already did this at the beginning of the lesson to connect to the CAC network. I will give a better overview of what we did earlier. `ssh` connects you to an external computer and let you use it as if it were your own.

This is how you connect to another computer with `ssh`:
```{.bash}
ssh yourUserName@someInternetAddressOrIP
```

To connect to the Centre for Advanced Computing, the command changes to:
```{.bash}
ssh hpcXXXX@sfnode0.hpcvl.queensu.ca
# or using IP
ssh hpcXXXX@130.15.59.64
```

After ssh-ing to `sfnode0` and entering your password, you will be connected to the M9000 system (Solaris). You will usually want to use the SW system instead (Linux). To use the Linux cluster, `ssh` to the linux login node with `ssh swlogin1`. Note that all files are shared between both systems.

One helpful tip is to always use `ssh -X` instead of simply `ssh`. The `-X` option enables X-forwarding. In plain English, this means that any pop-up windows you create on the remote machine are passed to your computer screen as well.

To close an `ssh` connection, simply type `exit`.

## Transferring files to and from CAC using `sftp`

So, say at the end of the day we've done some work on the cluster. Now we want to take our files off of the cluster. We are going to do this using `sftp` (Secure File Transfer Protocol). `sftp` is a lot like `ssh` (what we are currently connected to the cluster with), only with a smaller list of commands we can use for transferring files.

Open up a new terminal window. Note that people on Windows should immediately type in `cd MyDocuments` to get into their `My Documents` folder on Windows.

Get an example file that you want to upload in a directory on your computer. It can be literally anything. In terminal/MobaXterm, `cd` to that directory.

Now let's connect to the cluster with `sftp` to transfer files.

```{.bash}
sftp hpcxxxx@sfnode0.hpcvl.queensu.ca
```
Once you login, you should see a prompt with something like:
```{.output}
Connected to sfnode0.hpcvl.queensu.ca.
sftp>
```

`sftp` is very similar to the `bash` shell we get with `ssh`. To see a list of possible commands, simply type `help`.

```{.bash}
help
```
```{.output}
Available commands:
bye                                Quit sftp
cd path                            Change remote directory to 'path'
chgrp grp path                     Change group of file 'path' to 'grp'
chmod mode path                    Change permissions of file 'path' to 'mode'
chown own path                     Change owner of file 'path' to 'own'
df [-hi] [path]                    Display statistics for current directory or
                                   filesystem containing 'path'
exit                               Quit sftp
get [-Ppr] remote [local]          Download file
reget remote [local]               Resume download file
reput [local] remote               Resume upload file
help                               Display this help text

[snip to save screen space]
```

You'll see a lot of old, familiar commands like `cd` and `ls`, along with a few new ones. Note that a lot of these commands make reference to `local` and `remote` computers. We are actually connected to two computers at the same time: our own (at the directory where we called `sftp`), and a remote (at our home directory on `sfnode0`). A lot of our old commands now have two versions: a remote one, and a local one (usally prefixed with `l`). Let's try a few of these out to get the hang of it.

Important `sftp` commands (used the same way as before!):

+ `pwd` / `lpwd`: Print the remote working directory / our local working directory.

+ `cd` / `lcd`: Change remote working directory / local working directory.

+ `ls` / `lls`: List remote/local files.

+ `mkdir` / `lmkdir`: Create a remote / local directory

+ `put <localFile>`: Moves a file from your local working directory to the remote working directory. Can put multiple files by using `*` (example: `put *.txt`).

+ `get <remoteFile>`: "Gets" a file from the remote working directory and downloads it to your local working directory.

+ `bye` / `exit`: Either of these commands will quit your `sftp` session.

