# Just Enough Unix for Bioinformatics #

## Terminal Basics ##

```
date
```
return: Tue Sep 27 22:00:17 PDT 2022

**arguments**: basically, modifications to a command

As an example, we can change the format of the date that is produced when we enter the date command:

```
date "+%Y-%m-%d %H:%M:%S"
```
return: 2022-09-27 22:04:01

## Anatomy of a Unix Command ##

basic format: 
COMMAND OPTIONS FILEPATH

OPTIONS and FILEPATHs may be used, or the command may stand alone.

`date` as a stand alone command has no OPTIONS or FILEPATH. Entered as `date "+%Y-%m-%d %H:%M:%S"` has OPTIONS. The date command never has a FILEPATH.

## Manual Pages ##

Manual pages contain info about Unix commands. Access them by entering `man command` and hitting return. 

man: command
command: replace this with intended command you want info on... this is the arguments

Use spacebar to page through the info and use q to quit.

## Injury Prevention (Typing is Bad) ##

* use the up and down arrows to move backward through your command history and forward
* use left and right arrows to move through a command line to edit it
* take breaks from typing to prevent Injury

### Exercise 1 ###

Use the info in manual pages to figure out how to print the date with an unabbreviated day of the week.

```
man date

date "+%A-%x"
```
return: Tuesday-09/27/22

### Continued...###

history command: displays entire history of commands in this session

**tab completion**: hit the tab key to complete the rest of a word for your

printenv command: examine contents of a variable or see all environment variables

## Files and Directories ##

**file system**: location where your files are stored (hard drive)

df command: to see free space in file system

df -h command: to see free space in a more human, readable manner

du -h command: shows how much space each of your files and directories uses

### Creating and Viewing Files ###

touch command: creates a file or change its modification time

```
touch foo
```
The file foo is empty. Let's create a file with content:

```
cat > bar
```
AFter entering this, anything typed will become part of the file bar. Control-D will finish the text file.

cat command: dumps entirety of contents of file into Terminal

head command: first 10 lines of file

tail command: last 10 lines of File

```
head -1 bar 
```
This only shows the first line of the file.

Commands less and more allow us to page through files similarly to man.

### Editing Files ###

You can use both atom and nano to edit files. To use nano, type the "nano" command with the name of the file you want to edit:

```
nano bar
```
This opens an editor window.

### Navigating Directories###

pwd command: tells us which directory we are in

ls command: reports files and directories in your current working directory

ls -F command: adds characters to end of file names to distinguish directories from other Files

* a forward slash indicates a directory
* this is a sub directory
* unix sees . as the current directory and .. as the parent directory
* ls has lots of OPTIONS

**absolute path**: use a forward slash in this instance (good for navigating files other than in your working directory)

**relative path**: relative, as in, based in the current working directory

`cd` (change directory) changes the directory you are working from

`cd /` changes the working directory to the root directory, and `cd` without arguments will return you to your home directory

Files in certain directories can be manipulated from other directories:

```
cd /
touch ~/file2
```
This code changes your directory from home directory to root directory, and then creates file 2 in the home directory.

`~` represents the home directory.

```
touch /home/exouser/file3
cd ~/Documents
pwd
ls
touch ../file4
```
**Unsure about the above code... need help understanding relative/absolute.**

### Moving and Renaming Files ###

`mkdir` creates a new directory.

```
cd
mkdir Stuff
```
The above code changes the current working directory (cd) to a new directory that you created and named Stuff. (mkdir Stuff)

`mv` moves files AND is able to rename them, too

If there is no directory in the second argument of a `mv` command, the file will be renamed.

```
mv foo Stuff
mv bar barf
mv barf Stuff
mv file1 file2 file3 Stuff
```
The first line moves foo into directory Stuff. The second line renames file bar to barf.
The third line moves file barf to directory Stuff. The fourth line moves three files to Stuff. 

### Copying and Aliasing ###

`cp` copies files

```
cp Stuff/barf
```
**Is the file barf already in the home directory? Is Stuff the home directory? If not, how does this command copy barf from Stuff and place it in home?**

When making a copy of a file, you don't have to keep the same name.

```
cp Stuff/barf ./bark
```
`* (wildcard)`: fills in missing letters of arguments:

```
mv ba* Stuff
```

This moved files barf and bark to directory Stuff (in one line of code). This can be very useful for larger amounts of files!

**alias**: another name for a file1

```
ln -s Stuff/foo ./foof
nano foof
# do some editing
cat foof
cat Stuff/foo
```
I need to revisit the alias concept.

### Deleting Files ###

`rm` is the remove command (permanently deletes files)

### Using File Permissions ###

three types of permissions: read, write, and execute (`rwx`)

```
touch foo.text
ls -1F foo.text

-rw-r--r-- 1 exouser exouser 0 Mar 31 17:54 foo.txt 
```
This shows the permissions, number of links, owner, group, file size (bytes), date, and file name.

I was not able to recreate this return in my terminal. It returned "foo.txt".

```
chmod 777 foo.txt

-rwxrwxrwx 1 exouser exouser 0 Mar 31 17:54 foo.txt*
```
A hyphen denotes a regular file, a d denotes a directory, and an i denotes an alias. 

This file now has all permissions on: read, write, and execute for user, group, and public.

| **Category**|**Code**|**Oct**|**Meaning**|
|----------|---------|--------|-----------|
|raw data|-r--r--r--|444|anyone can r, nobody can w|
|private|-rw-------|600|I can rw, others NA|
|shared|-rw-r--r--|644|I can rw, others can r|
|shared|-rw-rw-r--|664|We can rw, public r|

`chmod` changes permissions of a file

```
chmod u-x foo.txt 
```
This file removes the execute permission from the user for this file. + is used to add permissions.

Octal format?

### Working with Text Files ###

Two types of files: binary and text, for compupter and human consumption respectively (usually). 

Switching files between Linux, Mac, and Windows can cause problems. Avoid putting sequence data into Word/Excel and then into Unix.

Large text files are common in Bioinformatics. 

```
ls -1h ~/data/C.elegans
```
I got a different return than what this tutorial suggested. My return: `c_elegans.PRJNA13758.WS269.genomic.fa.gz
c_elegans.PRJNA13758.WS269.protein.fa.gz`

These files are binary and should not be edited (raw data). 

`gunzip` uncompresses files
`gunzip -c` streams the file to the terminal in a way similar to `cat`

^c is a good way to interrupt a runaway process, such as a long file of text.

```
gunzip -c genome.gz | head
```
This uncompresses the file, and shows us the head of the file in FASTA format.

```
gunzip -c genome.gz | less
```
This allows us to page through the file. Hitting / will allow you to search for something in the material so you don't have to page through all of it.

`grep` is a command for finding patterns in files (similar to control F)

*return to this section for further analysis

`tar` creates archives, single files that contain multiple Files

To create a tar-ball: `tar -czf`

To decompress tar-ball: `tar -xzf`

## Processes ##

`top` is a program that monitors health of our compupter

`htop` does the same thing, JM prefers this owner

I like the color coordination of htop better.

### Viewing Processes ###

`top` or `htop` can be used to monitor a command being processed. 

`time` shows us the time elapsed and CPU time.

`> /dev/null`: directing output o Unix's black hole (lol)

### Managing Processes ###

```
perl -e 'while(1){print $i++, chr(10)}'
```
This was interesting to see! 

We use ^c to break out of something that is endlessly running so that we can use the terminal again.

^z will pause a process instead of terminating it completely. The command `fg` will restart it.

`ps` allows us to view all of our processes.

```
ps -u exouser
kill (put PID here)
```
`kill` can be used to interrupt a process that is in the background (the terminal no longer controls it)

`&` at the end of a command puts it in the background or `bg` is used to put a paused process in the background

### Unix Reference at End! Come back for reference.###


















