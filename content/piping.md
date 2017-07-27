---
title: "Redirection and piping"
menu: main
weight: 5
---


> ## Learning Objectives
>
> *   Redirect a command's output to a file.
> *   Process a file instead of keyboard input using redirection.
> *   Construct command pipelines with two or more stages.
> *   Explain what usually happens if a program or pipeline isn't given any input to process.

Now that we know most of the basic UNIX commands, we are going to explore some more advanced features. The first of these features is the wildcard `*`. In our examples before, we've done things to files one at a time and otherwise had to specify things explicitly. The `*` character lets us speed things up and do things across multiple files.

Ever wanted to move, delete, or just do "something" to all files of a certain type in a directory? `*` lets you do that, by taking the place of one or more characters in a piece of text. So `*.txt` would be equivalent to all .txt files in a directory for instance. `*` by itself means all files. Let's use some example data to see what I mean.

```{.bash}
wget <LINK>.wildcard_example.tar.gz
tar -xzf wildcard_example.tar.gz
ls
```
```{.output}
 dmel_unique_protein_isoforms_fb_2016_01.tsv  SRR307026_2.fastq
 Drosophila_melanogaster.BDGP5.77.gtf         SRR307027_1.fastq
 fb_synonym_fb_2016_01.tsv                    SRR307027_2.fastq
 gene_association.fb                          SRR307028_1.fastq
 SRR307023_1.fastq                            SRR307028_2.fastq
 SRR307023_2.fastq                            SRR307029_1.fastq
 SRR307024_1.fastq                            SRR307029_2.fastq
 SRR307024_2.fastq                            SRR307030_1.fastq
 SRR307025_1.fastq                            SRR307030_2.fastq
 SRR307025_2.fastq                            wildcard_examples.tar.gz
 SRR307026_1.fastq
```

Now we have a whole bunch of example files in our directory. For this example we are going to learn a new command that tells us how long a file is: `wc`. `wc -l <file>` tells us the length of a file in lines.

```{.bash}
wc -l Drosophila_melanogaster.BDGP5.77.gtf
```
```{.output}
471308
```

 Interesting, there are 471308 lines in our `Drosophila_melanogaster.BDGP5.77.gtf` file. What if we wanted to run `wc -l` on every .fastq file? This is where `*` comes in really handy.

```{.bash}
wc -l *.fastq
```
```{.output}
 20000 SRR307023_1.fastq
 20000 SRR307023_2.fastq
 20000 SRR307024_1.fastq
 20000 SRR307024_2.fastq
 20000 SRR307025_1.fastq
 20000 SRR307025_2.fastq
 20000 SRR307026_1.fastq
 20000 SRR307026_2.fastq
 20000 SRR307027_1.fastq
 20000 SRR307027_2.fastq
 20000 SRR307028_1.fastq
 20000 SRR307028_2.fastq
 20000 SRR307029_1.fastq
 20000 SRR307029_2.fastq
 20000 SRR307030_1.fastq
 20000 SRR307030_2.fastq
320000 total
```

 That was easy. What if we wanted to do the same command, except on every file in the directory? A nice trick to keep in mind is that `*` by itself matches *every* file.

```{.bash}
wc -l *
```
```{.output}
 22129 dmel_unique_protein_isoforms_fb_2016_01.tsv
471308 Drosophila_melanogaster.BDGP5.77.gtf
1304914 fb_synonym_fb_2016_01.tsv
106290 gene_association.fb
 20000 SRR307023_1.fastq
 20000 SRR307023_2.fastq
 20000 SRR307024_1.fastq
 20000 SRR307024_2.fastq
 20000 SRR307025_1.fastq
 20000 SRR307025_2.fastq
 20000 SRR307026_1.fastq
 20000 SRR307026_2.fastq
 20000 SRR307027_1.fastq
 20000 SRR307027_2.fastq
 20000 SRR307028_1.fastq
 20000 SRR307028_2.fastq
 20000 SRR307029_1.fastq
 20000 SRR307029_2.fastq
 20000 SRR307030_1.fastq
 20000 SRR307030_2.fastq
2317119 total
```

> ## Multiple wildcards

> You can even use multiple `*`s at a time. How would you run `wc -l` on every file with "fb" in it?

> ## Using other commands
> Now let's try cleaning up our working directory a bit. Create a folder called "fastq" and move all of our .fastq files there.

---------------------------------

## Redirecting output

Each of the commands we've used so far does only a very small amount of stuff. However, it's extremely easy to chain these simple UNIX commands together to do complicated stuff!

For our first foray into "piping", or redirecting, these commands we are going to use the `>` operator to write output to a file. When using `>`, whatever is on the left of the `>` is written to the filename you specify on the right of the arrow. The actual syntax looks like `command > filename`.

Let's try several basic usages of `>`. `echo` simply prints back, or echoes whatever you type after it.

```{.bash}
echo "this is a test"
echo "this is a test" > test.txt
ls
cat test.txt
```
```{.output}
this is a test

dmel_unique_protein_isoforms_fb_2016_01.tsv  fb_synonym_fb_2016_01.tsv
Drosophila_melanogaster.BDGP5.77.gtf         gene_association.fb
fastq                                        test.txt

this is a test
```

Awesome, let's try that with a more complicated command, like `wc -l`.

```{.bash}
wc -l * > word_counts.txt
cat word_counts.txt
```
```{.callout}
wc: fastq: Is a directory

22129 dmel_unique_protein_isoforms_fb_2016_01.tsv
471308 Drosophila_melanogaster.BDGP5.77.gtf
    0 fastq
1304914 fb_synonym_fb_2016_01.tsv
106290 gene_association.fb
    1 test.txt
1904642 total
```

Notice how we still got some output to the console even though we "piped" the output to a file. Our expected output still went to the file, but how did the error message get skipped and not go to the file?

This phenomena is an artifact of how UNIX systems are built. There are 3 input/output streams for every UNIX program you will run: `stdin`, `stdout`, and `stderr`.

Let's dissect these three streams of input/output in the command we just ran: `wc -l * > word_counts.txt`

* `stdin` is the input to a program. In the command we just ran, `stdin` is represented by `*`, which is simply every filename in our current directory.

* `stdout` contains the actual, expected output. In this case, `>` redirected `stdout` to the file `word_counts.txt`.

* `stderr` typically contains error messages and other information that doesn't quite fit into the category of "output". If we insist on redirecting both `stdout` and `stderr` to the same file we would use `&>` instead of `>`.

Knowing what we know now, let's try re-running the command, and send all of the output (including the error message) to the same `word_counts.txt` files as before.

```{.bash}
wc -l * &> word_counts.txt
```

Notice how there was no output to the console that time. Let's check that the error message went to the file like we specified.

```{.bash}
cat word_counts.txt
```
```{.output}
22129 dmel_unique_protein_isoforms_fb_2016_01.tsv
471308 Drosophila_melanogaster.BDGP5.77.gtf
wc: fastq: Is a directory
    0 fastq
1304914 fb_synonym_fb_2016_01.tsv
106290 gene_association.fb
    1 test.txt
    7 word_counts.txt
1904649 total
```

Success! The `wc: fastq: Is a directory` error message was written to the file. Also, note how the file was silently overwritten by directing output to the same place as before. Sometimes this is not the behavior we want. How do we append (add) to a file instead of overwriting it?

Appending to a file is done the same was as redirecting output. However, instead of `>`, we will use `>>`.

```{.bash}
echo "We want to add this sentence to the end of our file" >> word_counts.txt
cat word_counts.txt
```
```{.output}
22129 dmel_unique_protein_isoforms_fb_2016_01.tsv
471308 Drosophila_melanogaster.BDGP5.77.gtf
    0 fastq
1304914 fb_synonym_fb_2016_01.tsv
106290 gene_association.fb
    1 test.txt
1904642 total
We want to add this sentence to the end of our file
```

------------------------------------

## Chaining commands together

We now know how to redirect `stdout` and `stderr` to files. We can actually take this a step further and redirect output (`stdout`) from one command to serve as the input (`stdin`) for the next. To do this, we use the `|` (pipe) operator.

`grep` is an extremely useful command. It finds things for us within files. Basic usage (there are a lot of options for more clever things, see the `man` page) uses the syntax `grep <whatToFind> <fileToSearch>`. Let's use `grep` to find all of the entries pertaining to the `Act5C` gene in *Drosophila melanogaster*.

```{.bash}
grep Act5C Drosophila_melanogaster.BDGP5.77.gtf
```

The output is nearly unintelligible since there is so much of it. Let's send the output of that `grep` command to `head` so we can just take a peek at the first line.

```{.bash}
grep Act5C Drosophila_melanogaster.BDGP5.77.gtf | head -n 1
```
```{.output}
X       FlyBase gene    5794894 5799261 .       +       .       gene_id "FBgn0000042"; gene_version "5"; gene_name "Act5C"; gene_source "FlyBase"; gene_biotype "protein_coding";
```

Nice work, we've successfully send the output from one program to another. Let's try counting the number of entries for Act5C with `wc -l`.

```{.bash}
grep Act5C Drosophila_melanogaster.BDGP5.77.gtf | wc -l
```
```{.output}
37
```

Note that this is just the same as redirecting output to a file, then reading the number of lines from that file.

> ## Writing commands using pipes
> How many files are there in the "fastq" directory we made earlier? (Use the shell to do this.)

> ## Reading from compressed files
> Let's compress one of our files using gzip.
> ```{.bash}
> gzip gene_association.fb
> ```
>
> `gunzip -c` prints decompressed output to the console instead of decompressing the file. Using `gunzip -c`, can you write a command to take a look at the top few lines of the gene_association.fb.gz file (without decompressing the file itself)?
