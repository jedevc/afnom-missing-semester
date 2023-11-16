---
layout: lecture
title: "#2 Intermediate Shell + Git Basics"
date: 2023-10-10
ready: true
phase: 1
---
<div class="note">
<b> Update 08/10/23: </b> The contents below have been adjusted to reflect the Missing Semester content covered at UoB. Check our <a href="https://github.com/afnom/missing-semester/commits/master">git history</a> for a full list of changes.

</div>


In this lecture, we will present some more intermediate concepts around using the shell, including using pipes and redirection, pagers, permissions and groups, basic bash scripts, environment variables, monitoring your system and logging into other systems remotely. We will also mention the basics of Git, though a much more in depth explanation will be offered in session #5.

# Pipes and redirection

## Redirection

In the shell, we wish to move data around all the time. This includes moving data between tools on the command line, and moving data in and out of files. So first, let's consider the basics: moving data into files from the shell.

There are many reasons we may want to do this, but they ultimately all boil down to wanting to be able to store some data so it can be recalled later. This could be anything from a note to yourself to a configuration file for a tool you're using.

To write data to a file directly from the shell, we use _redirection_. Redirection simply means that the output of a command has been sent (directed) somewhere else, in this case to a file. Suppose printing 'Hello World':
```bash
echo 'Hello World!'
# Hello World!
```
and we wish to instead write this to the file `hello.txt`. To achieve this we can use the redirection operator `>`, which has syntax `[stdout] > [filename]`.

`echo` writes to the standard output (stdout) so:
```bash
echo 'Hello World!' > hello.txt
# <no output>
```
Let's check our file. Recall the `cat` command outputs the content of a file to the standard output
```bash
cat hello.txt
# Hello World!
```
Suppose we now wish to write another line to `hello.txt`. If we try
```bash
echo 'Hello from Line 2!' > hello.txt
# <no output>
cat hello.txt
# Hello from Line 2!
```
we find that our original file has been overwritten! The redirection operator `>` always overwrites whatever file is specified. Sometimes this is not desirable, and fortunately the designers of the shell foresaw this issue. Therefore we also have the `>>` operator, which instead _appends_ to the target file rather than overwrites it. Other than that, it functions identically.

```bash
echo 'Hello from Line 2, but actually this time!' >> hello.txt
# <no output>
cat hello.txt
# Hello from Line 2
# Hello from Line 2, but actually this time!
```

Let's now use input event debugging as an example for why we might want to do this. Libinput handles all user input events like keypresses and mouse movements, and if there is a problem with input devices we may wish to review the the Libinput debug logs. We can see these with `sudo libinput debug-events`. However, when moving the mouse or using the touchpad, a huge number of a events are generated and we could not possibly review them all in real time. So instead, we can redirect the output of this command to a file, and then review it afterwards to see if we can spot whatever issue we are pursuing. We will cover reviewing large files in the pagers section below.

## Pipes

Redirection allows changing what files programs write to and read from in a very flexible way (more so than the limited set of functionality described above). However, when we wish to move data between programs, we would like to send the output of one program directly to the input of the next one, rather than writing it to a file and then having the next program read from the same file. This is exactly what the pipe `|` achieves.

Suppose we have a program that outputs some text, and we would like to count how many lines of text there are in its output. First, let us consider just counting how many lines are in some text. We can do this with the `wc` (word count) tool, and we can find out exactly how to configure it to count lines using its manpage (accessible with `man wc`). Now we know that `wc -l` will achieve what we want, we can now consider passing the data from one program into `wc` using a pipe. To do this, we put a pipe between the two commands: `[command 1] | [command2]`.

So if we wish count lines from the output of `ls`, we can:

```bash
ls | wc -l
```
This demonstrates the power of having _composability_. That is, we have the ability to chain multiple programs together with multiple pipes, as the only requirement for this is for a program to read from standard input and to write to standard output.

We will cover the command `grep` (which can be used to filter text) in detail later but know for now that it filters its input against some pattern which we provide.

So if we did
```bash
ls | grep 'hello' | wc -l
```
we now have a program that counts how many lines in a entries in a directory contain the string 'hello'. Perhaps if we are feeling especially fancy we can then redirect the output of `wc` to a file:

```bash
ls | grep 'hello' | wc -l > hellocount.txt
```
incredible.

# Pagers

Reading large volumes of text in the terminal is tricky if it is all just printed onto the screen at once. Perhaps you wish to review a log file that is thousands of lines long, so long in fact that your terminal cannot even scroll back far enough to where the file started being printed! We could use some commands to slice the file into chunks for us, but what we would really like is to be able to review the contents of a file interactively at our own pace. This is exactly what a pager performs - from the name you may guess that it allows you to review text page by page, and you would be correct.

Currently, the most ubiquitous pager has to be `less`. As usual, when we encounter a new tool, we check out what it does by reading the man page, in this case at `man less`. Here, the manual page tells us it is the opposite of `more`, which it also tells us is another program. Not particularly useful. But we can review the manpage of more to find out what it does, aside from the obvious (that the creator of the less thought of an epic pun).

We can see that more displays text content one screen at a time, allowing the user to advance one line with the enter key and use page up / down as expected. Less enhanced this by allowing bidirectional movement using the arrow keys (along with a myriad of other key bindings).

Less is typically used to read from a file, but it can read from standard input as well. As an interactive tool, it also includes help menus and prompts you when a command is not understood. Give it a go with `less somefile`, and press the h key once inside to review the help menu.

As we alluded to earlier, less can do much more than page text. For example, we can search the text below the cursor position with `/[term to search for]`.

# Aside: editing files

We will cover editors (and IDEs) in detail in session #3. However for today it is useful to have a command line file editor, and so for this we will use `nano`. Nano is a small and simple text editor that focusses on usability rather than being extremely powerful like some other editors e.g vim or emacs. To begin editing, we pass the name of the file we wish to edit as the argument, for example `nano somefile`. Nano immediately loads into a TUI (terminal user interface), where instructions for basic commands are onscreen. Note that `^` here refers to the control key, and `M` refers to the meta key (which you probably know as Alt). So when you see `^X` to exit, to trigger this you press Ctrl-X. Other than that it works exactly as you'd expect a text editor to - text appears at your cursor when you type, and you use the arrow keys to move around the file.

# Users, groups and permissions

Linux has a strong permission model where access to files (including programs) and directories is controlled by permissions granted to users and groups. While the concept of a user account is probably quite intuitive, the idea of groups may be new.

Users can belong to many groups. Being in groups grants you all permissions that the group has, and when accessing a resource a user does so with the combined permissions of all of their groups. For example, a University system may have a group for students, which grants access to student resources, and a staff group which grants access to staff resources. While a student or staff member may be in just their respective group, an administrator may be in both the student and staff groups, as this would grant them access to both the student and staff resources.

You can see what groups you're in with the `groups` command. Try it on lab machine to see how CS IT control access.

On a single-user system like a home computer, it's quite common to not be in any groups other than a group named the same as your user (this is created by default, as a user must be in at least one group). This is as there is no need to share resources between users. There are various system administration related groups that your account may be in though, such as `wheel` (provides access to normally restricted commands) or `sudoers` (grants ability to run a command as the root user). Do note though that there are many user accounts and groups that exist for system services. These accounts cannot be logged into and are used for the purposes of isolating programs for security and safety reasons.

You may at this point be wondering exactly how all of these groups and users control access. This is achieved with the permission data present as metadata in the filesystem. Recall the `ls` program used to list files. If we specify the arguments `-l -a` (usually shortened to `-la`), we can see all files including hidden ones (`-a`) listed in a long format with extra detail (`-l`).

```bash
ls -la
##Access        User Group Size Modified     Name
# drwx------ 15 ms   ms    4096 Oct  9 17:48 .
# drwxr-xr-x  4 root root  4096 Oct  7 18:46 ..
# -rw-------  1 ms   ms    5430 Oct  9 09:38 .bash_history
# -rw-r--r--  1 ms   ms      21 May 21 12:56 .bash_logout
# -rw-r--r--  1 ms   ms      57 May 21 12:56 .bash_profile
# -rw-r--r--  1 ms   ms     288 Oct  8 12:09 .bashrc
# drwxr-xr-x 17 ms   ms    4096 Oct  9 09:38 .cache
# drwx------ 14 ms   ms    4096 Oct  9 09:38 .config
# drwxr-xr-x  2 ms   ms    4096 Oct  7 18:47 Desktop
# drwxr-xr-x  2 ms   ms    4096 Oct  8 11:45 Documents
# drwxr-xr-x  2 ms   ms    4096 Oct  8 11:29 Downloads
# -rw-r--r--  1 ms   ms     264 Oct  9 09:38 .gtkrc-2.0
# drwx------  4 ms   ms    4096 Oct  7 18:50 .firefox
# drwxr-xr-x  4 ms   ms    4096 Oct  7 18:47 .local
# drwxr-xr-x  2 ms   ms    4096 Oct  7 18:47 Music
# drwxr-xr-x  2 ms   ms    4096 Oct  8 11:29 Pictures
# drwxr-xr-x  2 ms   ms    4096 Oct  7 18:47 Public
# drwxr-xr-x  2 ms   ms    4096 Oct  8 18:14 .ssh
# drwxr-xr-x  2 ms   ms    4096 Oct  7 18:47 Templates
# drwxr-xr-x  2 ms   ms    4096 Oct  7 18:47 Videos
```
We can now see the access control metadata. The top header naming the columns has been inserted as part of writing this. You may notice the column after access is unlabelled; do not worry about this column as it is not relevant here.

As you can see, each file has one user and one group associated with it. The user is the account that owns the file, and the group is the group assigned to the file. The permissions granted to the user and group are presented in access at the start.

The first character in access is the type of file. For normal files it is blank, and for directories the type is d. There are also other types for special kinds of files, but these will not be covered today. The next nine characters is actually three sets of three. The first three are access for the file owner, the next three are for the group, and the final three are for anyone else. These are generally abbreviated to (u)ser, (g)roup, and (o)ther.

The three characters for each control read, write and execute permissions, hence the characters r, w and x. For files, these operate as you may infer: r allows reading of the file, w allows modifying file content and otherwise manipulating the file, and x allows executing the file. The execute permission is especially important, as it is what separates programs from regular files. Without it there would be no way to tell if a file is meant to be executed or not. For directories, it is a little more complicated, r allows listing directory content, w allows writing files in the directory (provided execute is also present, else it does nothing), and x allows modifying the directory as a structure and using the directory as your current working directory.

For example if a file has permissions `rwxr-x---` it means
<ul>
    <li>The owner can read, write and execute the file</li>
    <li>Any user in the group and read and execute the file, but not write (modify) to it</li>
    <li>Anyone else has no access at all</li>
</ul>

There are also some other values that certain characters can take, but these will not be covered here.

## Changing file permissions

The owner and group of a file can be changed with `chown` (short for change owner). The owner and group can only be changed by the current owner of the file (or the root/superuser).

The access of a file can be changed with `chmod`. Again, this can only be done by the owner of the file, or the root user. The chmod command has versatile syntax allowing permissions to be selectively added and removed rather than just setting a specific value to file.

Both of the above can also be applied recursively to change the permissions of an entire directory structure. Do this with caution! For details on how to use these commands, take a look at their manual pages.

# Writing a basic bash script

Throughout Missing Semester so far we have been working with the bash shell to execute various commands. However, bash is more than a shell: it is an entire scripting language. Bash is as powerful as other programming languages, but its syntax is relatively arcane and difficult to use. Coming from other languages such as Python or Java it will be very unfamiliar. It is for this reason we are going to leave the fundamental structures in bash such as if-statements, iteration and function definition / calls to later on.

Even without these bash scripts remain very useful as a way to run many commands together that you might want to run frequently. Here is an example script, which could be entered using nano:
```bash
#!/bin/bash

echo "Blinky time!!"
sudo bash -c 'echo 0 > /sys/class/leds/tpacpi::lid_logo_dot/brightness'
sleep 1
sudo bash -c 'echo 1 > /sys/class/leds/tpacpi::lid_logo_dot/brightness'
sleep 1
sudo bash -c 'echo 0 > /sys/class/leds/tpacpi::lid_logo_dot/brightness'
sleep 1
sudo bash -c 'echo 1 > /sys/class/leds/tpacpi::lid_logo_dot/brightness'
curl --silent https://missingsemester.afnom.net > index.html
echo "Homepage line count"
wc -l index.html
# we could also pipe the output of curl into wc to avoid having to save it to a file
# try modifying the script to do this
```
Obviously this is something you would want to run very frequently, so it's handy to have together in a script rather than having to type out all of these commands every time.

There are a few parts of this script which are new. The first line tells whatever shell that is executing the script to stop and re-execute it with the /bin/bash program, which is the path to the bash shell. This line is needed as it makes the script portable to systems where bash is not the default shell. This can also be used to run other languages. For example, `#!/usr/bin/env python3` can be used to make a Python program directly executable from the shell.

The curl command fetches data from a URL, and then writes it to standard output. It can also do a lot more than this, but we are using it in its simplest configuration here. You can see that the silent parameter is being used: this suppresses progress and status messages from curl. There is another tool, wget, that is also commonly used for fetching URLs. Broadly, they have similar functionality as command line tools.

Suppose the above script has been saved to `missing-semester.sh`. We can then run it with `bash missing-semester.sh`. However, we may wish to run this file as we do with any other program, which we do by specifying the path to it:

```bash
# recall . refers to the current directory
./missing-semester.sh
# bash: ./missing-semester.sh: Permission denied
```
Not exactly what we were hoping for. Let's review the permissions:
```bash
ls -l missing-semester.sh
# -rw-r--r-- 1 ms ms 430 Oct  9 19:00 missing-semester.sh
```
Aha! The file does not have any execute permissions. This is a common problem that occurs when trying to run executables, so if you ever get permission denied on a file you own make sure to double check the permissions. In this case we want execute permissions on the file, so let's add them:
```bash
chmod +x missing-semester.sh # adds execute permission to all users
ls -l missing-semester.sh
# -rwxr-xr-x 1 ms ms 430 Oct  9 19:00 missing-semester.sh
```
Looking better
```bash
./missing-semester.sh
# runs as expected :)
```



# Environment variables

Environment variables define how the shell behaves, and this includes programs that run inside your shell. The `env` command prints all environment variables when executed with no arguments:

```bash
env
# HOME=/home/ms
# LANG=en_GB.UTF-8
# SHELL=/bin/bash
# USER=ms
# PWD=/home/ms/Documents
# XDG_CURRENT_DESKTOP=KDE
# EDITOR=nano
# etc.
```

So for example, if a tool writes a file to the home directory, it needs to know where that is. To resolve its location, it may use the environment variable $HOME. 

One particularly important variable is called PATH. PATH stores where the shell should look to find executable binaries. If you run `cat` in your terminal, the shell has to find out where the actual executable `cat` lies on the system. It does this by searching through PATH, in order from the from the first directory specified, immediately returning when it finds its first match.

For example, PATH may be `/usr/local/sbin:/usr/local/bin:/usr/bin`. If `cat` is ran in the shell, the shell first looks inside `/usr/local/sbin` for the cat executable. If it's in there, then it's done. Else it moves onto the next directory which is `/usr/local/bin`. If it finishes searching all of these and cannot find the file requested, the shell will inform you that the command does not exist. You can use the `which` command to find out where an executable is stored; the which command looks up the location of the executable in the exact same way the shell does:

```bash
which cat
# /usr/bin/cat
```
Let's consider the script (missing-semester.sh) we wrote earlier. Currently, to execute it, we have to call it with its exact path. If we want to be able to call it by name, we have to ensure that is is reachable by the PATH. We could move or copy it to one of the directories already in PATH, but the folders in the PATH above are system-wide, and also managed by the package manager. It is therefore not recommended to store your own scripts in them. Instead, since this executable is just for the current user (us), we'll add a directory to our own PATH.

It is custom to put user executables in `~/.local/bin` (where ~ means the home directory), but for this demonstration we will make our own folder in the home directory called `executables`. Then we can add it to the PATH, and move our script there.

```bash
mv missing-semester.sh missing-semester # let's rename our script first as programs being run in the shell generally avoid file extensions in their names (this is just for neatness)
missing-semester # try it, expecting not found as it's not in PATH
# bash: missing-semester: command not found
mkdir executables
PATH="$PATH:/home/ms/executables" # note the use of double quotes here. this enables variable expansion, so $PATH gets expanded into the actual value of $PATH
mv missing-semester executables/ # move to the executables directory
missing-semester
# and we're running!
```

With this setup, every executable placed inside the executables folder will be available to us in this shell.

Importantly, do note that this change isn't permanent and only applies to the particular shell you have changed the environment variable in. If you wished to apply this change to every shell, you could modify the `.bashrc` file, as this file is executed as part of the start-up process for all shells.

In the computer labs at UoB, you may find that you are asked to use the `module` command to make some program available. This works exactly like our PATH change above. The software you're loading with `module` is actually present on the computer all the time, but it's not found by your shell as it's not in the PATH. Once you run the `module load` command, it gets inserted into your PATH so your shell can find it.

Broadly, there is always a reason for the behaviour you see from a Linux computer, and tools like `env` grant you the power to look under the hood and see how it all fits together. Try running `env` the next time you're on a lab machine!

# Monitoring your system

One key part of maintaining a system is being aware of what programs are running on it. This is useful to know just to understand what is happening on your system, but it's especially useful if there are programs hogging resources or otherwise behaving badly that you might want to act on.

For example, your system might be bogged down by an application thrashing the disk or an application using a lot of memory, and you might want to know what program is causing the slowdown. To do this you can use the the `top` program. top gives you a list of all of the processes running, and by default sorts them by CPU usage. It provides information about the processes, such as their priority, total CPU time and memory usage. It also provides an overview of the total load of the system, what CPU utilisation is like and how much memory is used/available in total. Additionally, you can take actions on processes inside of top such as terminating them or changing their priority. Pressing the h key while inside top will display a brief help message showing what functionality is available.

While top is an interactive tool, sometimes we also wish to simply list processes. We can do this with the `ps` tool. ps supports many flags to control what fields are output, and a typical invocation to list all processes with full information is `ps -aux`. As a typical system has a lot of processes, this outputs many lines. If we are looking for a specific process, we may wish to pipe the output of ps into a grep to filter it. Suppose we're looking running instances of the program konsole (the terminal being used to demonstrate this):

```bash
# for reference, the normal ps header
# USER PID    %CPU %MEM VSZ     RSS    TTY STAT START TIME COMMAND

ps -aux | grep konsole
# ms   511185 0.1  0.5  1085812 194112 ?   Sl   21:15 0:11 /usr/bin/konsole
# ms   512281 0.0  0.5  1085928 194152 ?   Sl   21:28 0:00 /usr/bin/konsole
# ms   516264 0.4  0.5  1086616 192632 ?   Sl   22:52 0:01 /usr/bin/konsole
```

and we can immediately see the three terminals open as this guide is written.

# SSH

Secure shell (SSH) is a protocol used to log in to another Linux computer over a network. The `ssh` tool is one of the most important utilities, as being able to use other computers without having to sit in front them is a key feature and something worth mastering. Here at UoB, all lab computers can be logged into remotely. This is useful if you wish to view or manage files stored in your CS account or run some computing task (such as machine learning) that is not suitable to be run on your local machine. Being logged into the CS network from home also allows you access services that are only available on the University intranet from anywhere. An example of one of these hosts only accessible from the University intranet is the servers used to host team projects for the 2nd year UG Team Project module.

To log in to a remote machine with SSH, the command follows the format `ssh <username>@<host>`. At UoB, the username you use is your CS account name (e.g abc123) and the publicly accessible host is called tinky-winky, available at tinky-winky.cs.bham.ac.uk. So an example login might look like

```bash
ssh abc123@tinky-winky.cs.bham.ac.uk
# abc123@tinky-winky.cs.bham.ac.uk's password:
# [abc123@tinky-winky ~]$
```
where you get prompted for your CS password and then get dropped to a shell on tinky-winky. Tinky-winky acts as a "bastion" server, meaning that all hosts are accessible from it, but it is not to be used as a server for any work. If we want to start doing any work, we should then connect to a lab machine, which we can do with the command `ssh-lab`. We then get asked for our password again to log on to the machine. Once logged in, we have the same shell as if we sat down at the lab machine physically.

Typing out a long password every time you wish to login can become cumbersome (especially so if you are using tools that connect on your behalf). To resolve this, we can use an SSH key. An SSH key consists of two parts: a private key and public key. In short, the public key is used by others to encrypt data to be sent to you, and the private is used by you to decrypt data sent to you. Since the private key serves as your identity, the key must be kept secret, ideally with a password used to encrypt it.

For the university systems, password based login is always available so a key is not strictly required. Most servers however are configured with password based login disabled and only allow key login. This is due to the low security of the average password, possibility of brute forcing them, and the possibility of an attacker stealing the password with a malicious server. For these security reasons key authenticated is widely preferred.

To generate a key, we use `ssh-keygen`. Current security practices recommend using the ed25519 key algorithm, so the full command to generate a new key is `ssh-keygen -t ed25519`. The key generation program is interactive and asks you for a name for the key and what password you would like to protect the key with.
Keys and other SSH information are stored within the `.ssh` folder. The prefixed dot means the folder is hidden by default, so it will not appear in an `ls`. We can use the `-a` flag to list all files, which includes hidden ones. Once the key is generated, you will notice two new files inside your `.ssh` folder: `keyname` and `keyname.pub`. The .pub file is the public key, and the file without an extension is the private key.

In order for the server you're connecting to to know about the key, you have to copy the public key to the server. To do this, you must edit the `.ssh/authorized_keys` file (note the American spelling), and paste in the contents of your .pub file into an empty line. Once you save the file, login with your key is now possible.

Important note: never copy the private key. If you do so by accident, make sure you de-authorise the key from all servers it allows access to. 

To login with a key, we can use the `-i` option in ssh, with the i standing for identity file. So for example, we may do `ssh -i .ssh/keyname abc123@tinky-winky.cs.bham.ac.uk`. You will not be asked for your CS account password as the key authenticates you and proves your identity.

## SSHFS

Another feature of SSH is the ability to mount a directory on the remote server on your local filesystem, allowing easy access to read and write to your files. To do this, we first create a mountpoint, and then use the `sshfs` command to mount the filesystem over SSH. Once we're done, we can then unmount the remote filesystem with `fusermount3 -u`. An example may look like the following:
```bash
mkdir mountpoint
sshfs abc123@tinky-winky.cs.bham.ac.uk:/home/students/abc123 mountpoint
ls mountpoint
# <all the files in your CS account here>

# some time later
fusermount3 -u mountpoint
# make sure the unmount succeeded before removing the mountpoint!
rm -d mountpoint # tidy up afterwards. the d flag allows removing empty directories
```
As we can see, the `sshfs` command has three key components: the user and host we're connecting to, the directory on the host we want to mount, and the location on our filesystem and we are mounting it to.


# Git

Finally, let's check out the basics of Git. Git is a version control system that records the history of files as the change. This is very useful for tracking changes to a project over time, and almost necessary for collaborative work on a project. If you've ever had some project file/folder named something like "Project v4.3 FINAL final SUBMISSION" then Git is what you've been looking for this whole time.

In this lecture we're going over just the basics of Git. Git is capable of a lot, but there are four key operations that you need to get started.

`git clone <url>` makes a copy of a remote repository on your machine. In Git, a repository is where all of the files tracked by Git are stored (i.e your project code, assets etc) along with the metadata that Git uses to provide history. The remote is usually a Git hosting service such as GitLab or GitHub.

Once you've got a copy of a repository, you can start editing files. If you create any new files and want to start tracking them in the repository, add them with `git add <file>`. When you would like to commit the changes you've made to your local repository, run `git commit -a -m <commit message>`. This creates a new snapshot of the state of the files in the repository. Use the commit message to record what changes you've made.

Then, you'll probably want to send your changes back to the remote. This is done with `git push`. After this, your commit will appear on the remote server where everyone with access to the repository can see it.

Finally, there's one more command that's worth knowing. When other people have made changes to the repository by introducing new commits, and then have pushed them to the remote, you may want to pull these changes down into your local copy of the repository. You guessed it, that's exactly what `git pull` does.

There are many more Git commands, but we'll cover them in lecture #5 with a deep dive on exactly what Git is, what problem it solves, how it works and how to use it. So stay tuned for more!
