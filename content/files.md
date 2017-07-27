---
title: "Handling files"
menu: main
weight: 4
---


Now that we know how to move around and look at things, let's learn how to read, write, and handle files!

```{.bash}
cd ~
```

What if we want to make a file? There are a few ways of doing this, the easiest of which is simply using a text editor. For this lesson, we are going to us `nano`, since it's super easy to use.

To use `nano` on a file, simply type `nano <filename>`. If the file does not exist, it will be created. `^O` (ctrl + O) saves the file, and `^X` quits. If you have not saved your file upon trying to quit, it will ask you if you want to save.

> ## Using `vi` as a text editor
> Although `vi` isn't the easiest or most user-friendly of text editors, you'll be able to find it on any system and it will take you far.
>
> A few things about using `vi` before we start. There are couple modes, a command mode (for doing big operations) and an "insert" mode. You can switch to insert mode with the `i` key, and command mode with `Esc`.
>
> In insert mode, you can type more or less normally. In command mode there are a few commands you should be aware of:
> `:q!` - quit, without saving
> `:wq` - save and quit
> `dd` - cut/delete a line
> `y` - paste a line

Let's make a new file now, type whatever you want in it, and save it.
```{.bash}
nano test.txt
```

Do a quick check to confirm our file was created.
```{.bash}
ls
```
```{.output}
test.txt
```

Let's read our file now. There are a few different ways of doing this, the simplest of which is simply reading the entire file with `cat`.

```{.bash}
cat test.txt
```
```{.output}
This is the contents of our test file.
```
Although `cat` may not seem like an intuitive command with which to open files, it stands for "concatenate"- giving it multiple arguments will print out one file followed by the contents of the next, and so on.

```{.bash}
cat test.txt test.txt
```
```{.output}
This is the contents of our test file.
This is the contents of our test file.
```

We've successfully created a file. What about a directory? We've actually done this before, using `mkdir`.
```{.bash}
mkdir files
ls
```

## Moving and copying files

To practice moving files, we will move `test.txt` to that directory with `mv` (move). `mv`'s syntax is relatively simple, and works for both files and directories `mv <file/directory> <path to new location>`

```{.bash}
mv test.txt files
cd files
ls
```
```{.output}
test.txt
```

`test.txt` isn't a very descriptive name. How do we go about changing it?

It turns out that the way to rename files and folders is with `mv` again. Although this may not seem intuitive at first, think of it as moving a file to be stored under a different name. The syntax is quite similar to moving files: `mv <oldName> <newName>`.

```{.bash}
mv test.txt newname.testfile
ls
```
```{.output}
newname.testfile
```

> ## File extensions are arbitrary
>
> In the last example, we changed both a file's name and extension at the same time. On UNIX systems, file extensions (like .txt) are arbitrary. A file is a .txt file only because we say it is. Changing the name or extension of the file will *never* change a file's contents, so you are free to rename things as you wish.

What if we want to copy a file, instead of simply renaming or moving it? Use `cp`. This command has to different uses that work in the same way as `mv`:

+ Copy to same directory (new name): `cp <file> <newFilename>`

+ Copy to other directory (same name): `cp <file> <directory>`

Let's try this out.
```{.bash}
cp newname.testfile copy.testfile
ls
cp newname.testfile ..
cd ..
ls
```
```{.output}
newname.testfile copy.testfile
files Documents newname.testfile
```

## Removing files

We've begun to clutter up our workspace with all of the directories and stuff we've been making. Let's learn how to get rid of them. One important note before we start... when you delete a file on UNIX systems, they are gone **forever**. There is no "recycle bin" or "trash". Once a file is deleted, it is gone, never to return. So be *very* careful when deleting files.

Files are deleted with `rm <file> [moreFiles]`. To delete the `newname.testfile` in our current directory:
```{.bash}
ls
rm newname.testfile
ls
```
```{.output}
files Documents newname.testfile
files Documents
```

That was simple enough. Directories are deleted in a similar manner using `rmdir`.

```{.bash}
ls
rmdir Documents
rmdir files
ls
```
```{.output}
files Documents
rmdir: failed to remove `files/': Directory not empty
files
```

What happened? As it turns out, `rmdir` is unable to remove directories that have stuff in them. To delete a directory and everything inside it, we will use a special variant of `rm`, `rm -rf <directory>`. This is probably the scariest command on UNIX- it will force delete a directory and all of its contents without prompting. **ALWAYS** double check your typing before using it... if you leave out the arguments, it will attempt to delete everything on your file system that you have permission to delete. So when deleting directories be very, very careful.

> ## What happens when you use `rm -rf` accidentally
>
> Steam is a major online sales platform for PC videogames with over 125 million users. In 2015, it generated over 3.5 billion dollars in sales. Despite this, it hasn't always had the most stable or error-free code.
>
> In January 2015, user kevyin on GitHub reported that Steam's Linux client had deleted every file on his computer. It turned out that one of the Steam programmers had added the following line: `rm -rf "$STEAMROOT/"*`. Due to the way that Steam was set up, the variable `$STEAMROOT` was never initialized, meaning the statement evaluated to `rm -rf /*`. This coding error in the Linux client meant that Steam recursively deleted every single file when run in certain scenarios (including connected external hard drives). Moral of the story: **be very careful** when using rm -rf!

## Practice problem - looking at files

Sometimes it's not practical to read an entire file with `cat`- the file might be way too large, take a long time to open, or maybe we want to only look at a certain part of the file. As an example, we are going to look at a large and complex file type used in bioinformatics- a .gtf file. The GTF2 format is commonly used to describe the location of genetic features in a genome.

Let's grab and unpack an example file for us to use with `wget` (`wget <link>` downloads a file from a link):
```{.bash}
wget ftp://ftp.ensembl.org/pub/release-77/gtf/drosophila_melanogaster/Drosophila_melanogaster.BDGP5.77.gtf.gz
gunzip Drosophila_melanogaster.BDGP5.77.gtf.gz
```

> ## Unzipping files
>
> We just unzipped a .gz file for this example. What if we run into other file formats that we need to unzip? Just use the handy reference below:
>
> * `gunzip` unzips .gz files
> * `unzip` unzips .zip files
> * `unrar` unzips .rar files
> * `tar -xzf` unzips .tar.gz files
> * `tar -xjf` unzips .tar.bz2 files

We just downloaded every annotated feature in the *Drosophila melanogaster* genome. It's a huge file- what happens if we run `cat` on it? (Press `Ctrl + C` to stop it).

So, `cat` really sucks when reading big files. What are the alternatives? Try all of these out and see which ones you like best!

+ `head <file>` - Print the top 10 lines in a file to the console. You can control the number of lines you see with `-n [numberOfLines]`.

+ `tail <file>` - Same as `head`, but prints the last five lines in a file to the console.

+ `more <file>` - Opens a file and display as much as possible on-screen. You can scroll with `Enter` or the arrow keys on your keyboard. Press `q` to close the viewer. Everything that you've looked at remains on screen.

+ `less <file>` - Identical to `more`, except what you've looked at get's hidden once you close `less` with `q`. Remember, `less` is `more`.
