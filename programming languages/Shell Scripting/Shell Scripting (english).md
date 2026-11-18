# Shell Scripting — A Direct Translation

**Author: Kayky Moreira Praxedes**

# 0. Basic Concepts

> This summary uses **Bash on GNU/*Linux***. System commands may vary by distribution; Bash-specific features are not necessarily available in other shells or when running a file with `sh`.

> In syntax models, **elements in braces indicate content to be replaced** (such as commands, paths, values, flags, etc.), and the braces should be removed in actual use, except when explicitly presented as part of the syntax. `{options}` indicates optional modifiers; the main description presents the behavior without them, considering the indicated arguments. When an argument can also be omitted, this will be explained in the text. The concrete examples already present the form used in the terminal.

## Shell:

The shell is a program that works as a **command interpreter** and also as a **programming language**, allowing you to control and combine other system programs. The **terminal** provides the input and output medium used to interact with the shell, while the ***kernel*** is the part of the operating system responsible for managing resources such as processes, files, and devices.

When you open a terminal in a graphical interface, a **pseudoterminal** is normally created and a shell (such as `bash`) is started connected to it. The commands typed are then interpreted by the shell, which can execute them internally or start other programs.

### Programs:

Commands executed by the shell can be **internal** (builtins, implemented by the shell itself) or **external programs**, stored in the file system. For example, `cd` is a builtin, while `ls` normally corresponds to an external program.

When an external program is called only by name, without indicating its path, `bash` looks for a corresponding executable in the directories defined by the `$PATH` variable. That is why it is possible to simply run `ls` instead of writing its full path, such as `/usr/bin/ls`.

### Exit Status:

In addition to the information a command may print, its execution ends by producing an **exit status**, represented by a value from `0` to `255`. By convention, `0` represents a successful execution, while values other than `0` indicate some type of failure or condition other than success.

The status of the last command executed is available in `$?`. This value is important in scripts, as it allows decisions to be made based on the success or failure of previous commands.

## Manuals:

Since *Linux* receives recurring updates, printed manuals may become outdated. Most of the necessary information, from user commands to internal *kernel* and C library interfaces, can be consulted in the operating system (OS) itself.

### System Hierarchy:

For organization purposes, the manuals were divided into groups represented by numbers, following this hierarchy:

1. User commands;
2. *Kernel* system call interfaces;
3. C library interfaces;
4. Special files (devices, drivers, etc.);
5. File formats;
6. Games and amusements;
7. Miscellaneous subjects;
8. System administration commands;
9. *Kernel* internal commands (rarely used on *Linux*, which keeps this documentation on the official website);
10. *New* (additional section for new or in-progress documentation, when used).

> The last two entries vary by distribution and *Linux* environment. Section 9 is not standardized; *New* may appear identified by `n`, not being a universal section 10.

- `apropos {word}` - Shows a one-line summary about the searched word (what the command is for, what type of file it is, etc.). Returns partial results found in the title and summary, including when the search matches part of a word.

```
apropos grep
bzegrep (1)          - search possibly bzip2 compressed files for a regular expression
grep (1)             - print lines that match patterns
...

apropos line
proc_pid_cmdline (5) - command line
HEAD (1)             - Simple command line user agent
...
```

- `whatis {word}` - One-line summary about the searched word (direct result only).

```
whatis grep
grep (1)             - print lines that match patterns

whatis line
line: nothing appropriate.
```

> The number next to the name returned in `apropos` and `whatis` corresponds to the manual section on the system.

- `man {hierarchy} {word}` - Full manual for the word (direct result only). The hierarchy can be omitted, but it may show different results than expected if the word appears in multiple hierarchies (it chooses the first referenced one).

- `info {word}` - Manual organized into navigable topics; it can be thought of as an expanded `man` for programs that offer documentation in this format, though it is not necessarily more up to date.

- `{word} --help` - Intermediate manual about the searched word (direct result only).

- `help {command}` - Consults the help for Bash internal commands, such as `cd` and `read`.

## Basic Commands:

### System Information:

> For more in-depth information, use the command consultation tools.

- `who {options}` - Lists logged-in users.

- `pwd {options}` - Shows the current working directory.

- `history {options}` - Shows recently executed commands.

- `ls {options} {directory}` - Lists the contents of a directory.

- `tree {options} {directory}` - Shows the contents of a directory in a hierarchical tree format.

- `find {starting_directory} {options} {criteria}` - Searches for files and directories by name, permissions, and other criteria.

- `locate {options} {criteria}` - Fast path search in a previously built database, working as an alternative to `find` for searching names. It does not use a starting directory.

> The database may be outdated. Its update is done by `updatedb` (normally with `sudo`) or by a scheduled system task.

- `whereis {options} {command/file}` - Shows the location of commands, manual pages, and binaries.

- `which {options} {command}` - Locates executables in the `$PATH` directories, while `whereis` also looks for other related files, such as manuals.

- `type {command}` - Shows how Bash interprets a name: internal command, function, alias, or external program. `command -v {command}` provides a short identification, useful in scripts.

- `file {options} {file}` - Shows the type of the specified file.

- `stat {options} {file}` - Shows detailed information about files and directories.

- `date {options} {date}` - Shows or sets the system clock.

- `cal {options} {date}` - Shows a calendar on the command line.

### Terminal Actions:

- `clear` - Clears the current screen content.

- `exit {status}` - Terminates the shell with the informed status (optional). In an interactive session, this normally also closes the associated terminal.

- `logout` - Terminates a login shell; it does not close all of the user's sessions on the system.

## Process Automation:

The shell can be used directly in the terminal, one command at a time, or in an automated way through scripts, which allows greater speed, versatility, and more complex tasks. To create the script, it is necessary to:

1. Create a file and insert the desired sequence of commands. On the first line, indicate the interpreter with `#!/path/interpreter` (for example, `#!/bin/bash`).
2. Give execution permission with `chmod +x script.sh` and run it with `./script.sh`. The `./` informs the path, since the current directory normally does not belong to `$PATH`.

> The `#!` (shebang) resembles C's `#include` in the idea of indicating something external needed by the code. The difference is that it points to the program that will interpret the file, without including code or libraries.

It is also possible to run a script by choosing the interpreter directly in the command (such as `bash script.sh`). In this case, execution does not require execution permission nor depend on `#!` (the file still needs to be readable).

---

# 1. Text Processing

## Terminal Text Editors:

> The main differences are in the number of tools present in the editor and in the way they edit.

- `nano {options} {file}` - Simple editor, with the main shortcuts visible on screen.

- `vim {options} {file}` - More complete editor, with separate modes for inserting text and executing commands.

- `emacs {options} {file}` - Robust and extensible editor, with integrated tools for editing and programming.

> `nano` and `pico`, as well as `vim` and `vi`, are related, but not necessarily the same program. Some distributions use aliases or links so that one name executes the other's implementation.

## Text Viewing:

- `echo {arguments}` - Prints the arguments as text, normally followed by a line break.

- `printf {format} {arguments}` - Prints according to a format: in `printf '%s\n' "$text"`, `%s` receives the text and `\n` represents the line break (syntax almost identical to C's).

- `cat {options} {files}` - Concatenates files and prints their contents. If used with a single file, it simply prints it.

> The `tac` command reverses the line order of each file, keeping the order of the files received.

- `zcat {options} {file}` - Reads the contents of a file compressed with `gzip`. There are variations depending on the compression type.

- `less {options} {file}` - Shows the output of a command or text file and allows navigating backward and forward.

> The `more` command does basically the same thing, but offers more limited navigation, especially when receiving data from another command.

- `head {options} {file}` - Shows the first lines of a file (ten by default; `-n` changes the quantity).

- `tail {options} {file}` - Shows the last lines of a file (ten by default; `-n` changes the quantity).

## Extracting Data from Text:

- `strings {options} {file}` - Extracts readable characters from files (it can extract data from binaries such as `.jpg`, `.mp3`, etc.).

- `wc {options} {file}` - Counts the number of lines, words, characters, etc. (depending on the chosen option) of a file.

- `grep {options} {pattern} {file}` - Filters the lines that match a pattern (by default, a regular expression). It can read files or standard input. `-i` ignores differences between uppercase and lowercase, `-n` shows the line number, `-v` selects the ones that do not match, and `-F` treats the pattern as literal text.

```bash
grep -in "error" log.txt   # Lines with "error", "ERROR" etc., indicating the position.
grep -F "[OK]" log.txt    # Searches for the literal text [OK].
```

- `cut {options} {file}` - Extracts parts of each line, such as delimited fields (`cut -d ":" -f 1 /etc/passwd` extracts the usernames).

- `diff {options} {file_1} {file_2}` - Compares the contents of files.

## Text Operations:

- `sort {options} {file}` - Sorts the contents of an input data stream or file.

- `uniq {options} {file}` - Removes **adjacent** repeated lines. It is common to use it after `sort`; with `-c`, it also counts repetitions.

- `tr {options} {set_1} {set_2}` - Replaces or removes characters from standard input. For example, `tr '[:lower:]' '[:upper:]' < file.txt` converts lowercase letters to uppercase.

- `tee {options} {files}` - Shows the output of a command and writes it to one or more files.

> The following commands have greater complexity (this is only a basic usage):

- `sed {options} {expression} {file}` - Prints the contents of a file (like `cat`) with the option to replace words.

- `awk {options} {program} {file}` - Formats the result of a command.

# 2. File System

Before anything else, it is important to understand how the shell identifies the path to a file (and/or directory):

- `/absolute/path/file` - Starting the path with a slash (`/`) indicates that the absolute path is being used, from the system root.
- `~/user_path/file` - Starting the path with `~/` indicates that the path from the current user's home directory is being used (**it is also considered an absolute path**). To use another user's directory without informing the absolute path, you can write `~target_user/path/file`.
- `relative/path/file` - The system considers the current directory and interprets the path from it.

In addition, `.` and `..` work as shortcuts for the directory itself and for its parent directory. They can be imagined as aliases, although they are special path components, not aliases defined in the shell. Thus, being in directory `C/`, whose absolute path is `/A/B/C`, the commands `cd D` and `cd ./D` produce the same result for reaching `D/`. Similarly, to reach `B/`, both `cd /A/B` and `cd ..` produce the same result.

In common *Linux* file systems, names normally distinguish uppercase and lowercase letters (case-sensitive), so `File.txt` and `file.txt` may represent different files. In addition, files and directories whose name begins with `.` are conventionally treated as **hidden** and, by default, are not displayed by `ls` (they can be shown with `ls -a`).

## *Linux* Folder Structure:

> The structure and function of the folders may vary by distribution. The links shown below illustrate an organization with directories integrated into `/usr`.

```text
/                         # root: ancestor of all system files
├── bin -> usr/bin        # essential binary commands to initialize and run the system
├── boot                  # files needed to initialize the system
├── dev                   # device files (disks, terminals, printers, etc.)
├── etc                   # local system configurations (e.g., /etc/passwd = user list)
│   ├── opt               # configs for optional packages installed in /opt
│   ├── X11               # X window system configs
├── home                  # users' personal directories (e.g., /home/zach)
│   └── user              # personal directory of a specific user
├── lib -> usr/lib        # shared libraries
│   └── modules           # loadable kernel modules
├── mnt                   # mount points for temporary file systems
├── opt                   # optional software packages
├── proc                  # virtual file system with kernel and process info
├── root                  # administrator's personal directory (not the same as /)
├── run                   # volatile runtime data, recreated at each boot
├── sbin -> usr/sbin      # essential system administration binaries
├── sys                   # pseudo file system for devices
├── tmp                   # temporary files
├── usr                   # shareable programs and data, normally static
│   ├── bin               # most user commands
│   ├── games             # games and educational programs
│   ├── include           # headers used by C programs
│   ├── lib               # libraries
│   ├── local             # local hierarchy: bin, games, lib, share, src etc.
│   ├── sbin -> bin       # administration commands; may be integrated into bin
│   └── share             # architecture-independent data
│       ├── doc           # documentation
│       ├── info          # main GNU info system directory
│       └── man           # online manuals
└── var                   # variable data: change during system execution
    ├── log               # log files (lastlog, messages, wtmp etc.)
    └── spool             # spool application data (cron, at, lpd etc.)
```

## Users:

*Linux* is a multiuser system in which access rights are defined for:

- `u` (user): Owner of the file.
- `g` (group): Group associated with the file, bringing together users who share permissions. It allows giving access to several specific people to a file or directory **without having to release it to the rest of the system** (others).
- `o` (others): Other users, who are not the owner nor belong to the group associated with the file.

The classes are not added together: in traditional permission control, it is first checked whether the user is the owner; otherwise, whether they belong to the group; finally, the others permissions are applied.

When researching detailed information about a specific file (whether with `stat`, `ls -l`, etc.), it is possible to see the file owner and group.

## Information:

- `id {options} {user}` - Shows the user's identity information. If no user is informed, it considers the current user.

- `basename {options} {path/file_or_dir}` - Returns only `file_or_dir`.

- `dirname {options} {path/file_or_dir}` - Returns `path`.

- `lsof {options}` - Lists open files.

- `fuser {options} {file}` - Identifies the processes that use a file or file system.

## File and Directory Operations:

> Using these commands with an absolute path (`/` and `~/` expanded by the shell to the personal directory) and with relative paths may produce different results.

- `cd {options} {path/directory}` - Changes the current working directory.

- `mkdir {options} {path/name}` - Creates a directory at the specified path.

- `rmdir {options} {path/name}` - Deletes an empty directory; if it contains elements, it will not be deleted.

- `rm {options} {path/name}` - Deletes a file or directory. It is very commonly used with the `-r` flag (recursive), with which it deletes all files in a directory and the directory itself.

- `mv {options} {source/old_name} {destination/new_name}` - Moves or renames a file or directory. If the destination is an existing directory, it keeps the name and moves the item into it; otherwise, the destination indicates the new path and name.

- `cp {options} {source/old_name} {destination/new_name}` - Copies a file, interpreting the destination in the same way as `mv`. To copy directories, it is necessary to use it with the `-r` flag, which recursively copies the files and subdirectories of the target folder.

- `ln {options} {source/file_name} {destination/link_name}` - Creates links (alternative paths to access a given item).

    - `ln` (hard link): works as a new **independent pointer to the data of the original file** (it works even if the original file is deleted). **It is not used with directories and requires the same file system**.

      > In the file information (accessed by `ls -l`, for example), it is possible to see whether there are other hard links to that disk space and how many (by default, it is only 1).
 
    - `ln -s` (symbolic or soft link): works as a **pointer to the original file or directory** (it is linked to its name). Thus, the symbolic link stops working if the original file is deleted, but it works again if a file with the same name and path is created in its place. **It works with directories and can cross file systems**.

      > In its information, it indicates the path to which it points (`link_name -> /path/file`).

- `gzip {options} {file}` - Compresses files.

- `gunzip {options} {file}` - Decompresses files.

- `tar {options} {files}` - Groups multiple files/directories into a single file.

- `split {options} {file}` - Splits a file into smaller pieces.

- `shred {options} {file}` - Overwrites the file data to make recovery difficult; `-u` also removes the file. Effectiveness depends on the storage: SSDs, copies, and systems that write data to new locations may preserve earlier versions.

## Permissions:

Each file or directory can have the following permissions:

- `r` (read): Allows **the file to be opened and read**. For directories, it allows **the contents to be listed**.
- `w` (write): Allows **the file to be written or truncated**. For directories, it allows **creating, removing, and renaming entries**, normally together with `x`.
- `x` (execute): Allows **the file to be treated as an executable program**. For directories, it allows **accessing them directly and their subfolders** (such as when using `cd` or opening a file by path).

**Permission Format:** `-rwxrw-r--`

The first character (`-` in the example above) represents the file type. The main ones are:

- `-` - Regular files;
- `d` - Directories;
- `l` - Symbolic links;
- `c` (character device) - Character devices, generally accessed as data streams, such as terminals;
- `b` (block device) - Block devices, such as disks, which allow access to storage regions;
- `p` and `s` - Respectively, named pipe and socket.

> In a simplified way, the system represents devices by special files. This allows read and write operations to be used to communicate with them, although each device has its own behaviors.

In the remaining positions, the elements, grouped in threes, represent the permissions of the **owner**, the **group**, and the **other users** (`-` indicates the absence of that permission).

- `chown {options} {owner}:{group} {file/directory}` - Changes the owner and/or group associated with a file or directory.

- `chgrp {options} {group} {file/directory}` - Changes only the group associated with a file or directory.

- `chmod {options} {permissions} {file/directory}` - Changes access permissions.

> Only the file owner or the superuser can change the mode (permission).

It supports two forms for specifying changes:

- **Octal Representation:** Uses three octal digits to define the absolute permissions of the owner, group, and other users, respectively. Permissions are calculated by the sum of `r`=4, `w`=2, and `x`=1 (`chmod 751 file` results in `-rwxr-x--x` for a regular file).
- **Symbolic Representation:** Uses the letters `u` (owner), `g` (group), `o` (others), `a` (all) accompanied by the operations `+` (adds), `-` (removes), or `=` (specifically defines) (`chmod u+x file` adds execution to the owner, for example).

> When omitting the class (`u`, `g`, `o`, or `a`), the command considers all of them, but respects the `umask` restriction; that is why `chmod +x` and `chmod a+x` are not always equivalent.

### Special Permissions:

In octal representation, a fourth digit can be added to the left to define special permissions:

- **SUID** (Set User ID, octal `4nnn`): When executing the file, the process assumes the effective identifier of the owner (`-rwsr-xr-x`).
- **SGID** (Set Group ID, octal `2nnn`): When executing the file, the process assumes the effective group of the file (`-rwxr-sr-x`). In directories, new files receive the directory's group.
- ***Sticky bit*** (octal `1nnn`): In directories, it restricts removal and renaming to the file owner, directory owner, or superuser (`drwxrwxrwt`).

The letters `s` and `t` occupy the position of an execution permission. If this permission is not enabled, they appear in uppercase (`S` and `T`). On *Linux*, SUID and SGID do not produce this execution effect on interpreted scripts.

- `umask {options} {mask}` - Defines the bits removed from the requested permissions when creating files and directories. The mask is inherited by child processes, but does not change existing files.

With `umask 022`, a creation that requests `666` results in `644` (`rw-r--r--`); for a directory that requests `777`, it results in `755` (`rwxr-xr-x`). The mask removes permissions, without adding execution to files that did not request it.

---

# 3. Processes

Every program running on *Linux* is treated by the *kernel* as a **process**. Since modern operating systems are multitasking, the *kernel* rapidly switches which process has access to the CPU (Central Processing Unit), creating the impression of simultaneity even on a single core. With multiple cores, there can also be truly parallel execution.

The shell itself is also a **process**. In a common execution from an interactive shell, when it starts an external program, that program normally runs in another process, related to the shell as its child process. Internal commands (builtins) can be executed directly by the shell process itself.

## How Processes Work:

A program can launch (execute) other programs, forming a parent-child relationship between processes:

```text
parent_process
 └── child_process
```

- A **parent process** is one that gives origin to a **child process**.
- Each process receives a PID (process ID), which can be reused after the process ends, in addition to keeping information such as allocated memory, execution state, and associated owner/user (as well as files).
- Many system services run as ***daemons***: processes in the background, without a user interface.

> Conceptually, PID 0 can be associated with the *kernel* side in this tree: it corresponds to the initial task, linked to the idle task (swapper/idle), and not to the *kernel* as a whole as a common program. From it originates PID 1 (`init`, often implemented by `systemd`), responsible for initializing services.

Processes can have different states indicating how their execution is, with the main ones being:

- `R` (running/runnable) - Running or ready to run;
- `S` (sleeping) - Waiting for some event, in an interruptible wait;
- `D` - Non-interruptible wait, often associated with input and output operations;
- `T` (stopped) - The process was instructed to stop;
- `Z` (zombie) - Child process finished but not collected by the parent;

> `<` (high priority) is not directly a state, but an indicator of elevated priority. Its influence depends on the scheduling policy, not on a fixed amount of CPU time.

### Process Information:

*Linux* exposes information about running processes through the virtual file system `/proc`. Each running process has its own folder inside `/proc`, identified by its PID (`/proc/{PID}`). It is inside it that, among other things, the process's file descriptors (`/proc/{PID}/fd`) are located, consultable with `ls -l`.

## Listing:

- `ps {options}` - Shows a **snapshot** of the processes.

    - The `ps x` command displays all of the user's processes, adding the `STAT` column (process state).
    - The `ps aux` command displays the processes of **all users**, with extra columns for CPU and memory usage.

    > Options without a dash (`x`, `aux`) invoke `ps` in the BSD style, which *Linux* can emulate.

- `pstree {options}` - Shows the processes in a tree format, highlighting the parent/child relationship.

- `top {options}` - **Dynamic** and continuously updated view of the processes, sorted by activity. It also allows navigation and filtering of information at runtime.

> `btop` is an alternative to `top` with a more visual interface: it gathers CPU, memory, disk, and network graphs, in addition to offering mouse support, tree process visualization, and interactive filters.

- `jobs {options}` - Shows the active jobs that **that shell** launched.

> **A job can be composed of more than one process**. When running `sort file.txt | uniq &`, this is a job (it appears as `[1]` in `jobs`), but there are two processes (`sort` and `uniq`, each with its own PID).

- `vmstat {options} {delay}` - Shows instantaneous system usage (memory, swap, disk); with a time in seconds, it updates continuously.

- `xload {options}` - Graph of system load over time (graphical interface).

- `tload {options}` - Similar to `xload`, but drawn in the terminal.

## Controlling Processes:

In the interactive session, programs can run in two modes: **foreground** (controlling the terminal) or **background** (releasing the prompt). To start directly in the background, simply follow the command with `&`:

```console
$ xlogo &
[1] 3486
```

- `fg {%N}` - Brings a job to the foreground, identified by the job number, `%N` (optional if there is only one).

- `bg {%N}` - Resumes a job stopped in the background.

> This command only works in the terminal where the job originated, requiring it to be stopped first (the process is resumed, and goes to the background).

### Signals:

- `kill {options} {-signal} {PID or %job}` - Sends a signal to a process (identified by its PID or by the job number `%N`). Without a specified signal, it sends `TERM` by default, asking the program to terminate.

`kill` does not "kill" processes directly; it sends **signals**, notifications that the system and other processes can deliver to a program. The list can be consulted with `kill -l`, and their meanings in `man 7 signal`.

- `Ctrl+C` sends `SIGINT` to the foreground process group, normally interrupting its execution.
- `Ctrl+Z` sends `SIGTSTP`, normally suspending that group (marked as stopped in `jobs`).

A program can handle or ignore several signals, but not `SIGKILL` (`kill -KILL PID`), which forces termination, nor `SIGSTOP` (`kill -STOP PID`), which forces suspension. Termination and suspension are different actions.

> A program can also implement its own keyboard commands: `q`, for example, can simulate an exit action. In this case, it interprets an input; it does not create a new system signal.

- `killall {options} {-signal} {name}` - Same idea, but sends the signal to all processes that match a **program name**.

- `nohup {command}` - Executes a command ignoring `SIGHUP`, helping to keep it after the terminal is closed. It does not put it in the background by itself; for that, use `nohup command &`.

- `wait {PID or %job}` - Waits for the termination of a child process or job and returns its status. Without an argument, it waits for all of the shell's background jobs.

---

# 4. Inputs, Outputs, and Redirections

## Standard Input and Output:

Programs normally use three channels to handle data: **standard input** (stdin, descriptor **0**), **standard output** (stdout, descriptor **1**), and **standard error** (stderr, descriptor **2**). In an interactive session, they are usually connected to the terminal: the input receives what is typed, while the two outputs show text on the screen.

These destinations can be changed individually. For example, a program can read from a file, write the result to another, and continue showing errors in the terminal.

### TTY (Terminal):

**TTY** (teletype) is the name given to the device that represents a terminal in the system. Historically it referred to physical terminals; when opening a terminal in a graphical interface, a **pseudoterminal** is normally created, whose side connected to the shell appears in `/dev/pts/{N}` on *Linux*.

Descriptors can be imagined as **pointers** to the input and output destinations, but they are not file system links. Each process has its own descriptor table, which can contain others besides `0`, `1`, and `2` (open files, pipes, etc.). Redirecting one of them does not automatically redirect those of another process.

## Redirection to Files:

In the operators below, the descriptor number must be next to the operator. If omitted, `0` is used for input and `1` for output.

- `{command} {n}< {file}` - Makes descriptor `n` read from the file. For example, `sort < names.txt` receives the contents of `names.txt` through standard input.
- `{command} {n}> {file}` - Directs descriptor `n` to the file, creating it or truncating its previous content. For example, `command 2> errors.txt` saves only the errors.
- `{command} {n}>> {file}` - Appends the output to the end of the file, creating it if necessary.
- `{command} &> {file}` - Directs both standard output and standard error to the same file.

Redirection is prepared **before command execution**. Thus, `cat file > file` empties the file before `cat` can read it.

> `set -o noclobber` enables protection against overwriting with `>` in the current shell; `set +o noclobber` disables it. The `>|` operator allows forcing the overwrite even with this protection.

> `/dev/null` discards the data written to it. For example, `command > /dev/null` discards standard output, while `command 2> /dev/null` discards errors.

## Descriptor Duplication:

- `{command} {n}>&{target}` - Makes descriptor `n` (by default, `1`) use the same destination that `target` has at that moment. `2>&1`, for example, directs errors to the current destination of standard output.

The order of redirections matters, as they are processed from left to right. Assuming that file `y` exists and `x` does not:

```bash
cat x y > output.txt 2>&1  # 1 becomes output.txt; 2 receives the same destination as 1 (output.txt).
cat x y 2>&1 > output.txt  # 2 receives the current destination of 1 (terminal); then only 1 goes to output.txt.
```

## Communication and Block Input:

- `{command_1} | {command_2}` - ***Pipes***. Connects the standard output of the first command to the standard input of the second (avoiding the need to create intermediate files). `sort names.txt | uniq`, for example, sorts the lines and removes adjacent repetitions. The error output keeps its original destination, unless it is redirected.

> By default, the status of this sequence (pipeline) is that of the last command. With `set -o pipefail`, it becomes that of the rightmost command that failed, or `0` if all succeeded; `set +o pipefail` restores the default behavior.

- `{command} << {delimiter}` - Sends the following lines as input until it finds a line containing only the delimiter (normally `EOF` or `END`). This block is called a here-document and dispenses with interactive input ended with `Ctrl+D`.

```bash
cat << EOF
> Personal directory: $HOME
> Result: $((2 + 3))
> EOF
```

Without protection on the delimiter, the block allows parameter expansion, command substitution, and arithmetic expansion. With the delimiter protected, as in `<< 'EOF'`, the content is literal; the final line remains only `EOF`, without quotes. Filename expansion does not occur in this text.

> `<<-` performs the same function, but removes tabs from the beginning of the block lines and from the final delimiter (it does not remove regular spaces).

---

# 5. Expansions

Before executing a command, the shell recognizes the line structure and performs **expansions**, replacing certain expressions with values, results, or file names. Only then does it deliver the already "chewed" arguments to the program.

Expansions follow an order:

1. **Braces;**
2. **Tilde;**
3. **Parameters;**
4. **Arithmetic and Commands;**
5. **Word Splitting;**
6. **File Names;**

At the end, the quotes used to protect the syntax are removed.

> Not every context applies all these steps (quotes and assignments, for example, alter this behavior).

## Types of Expansion:

### File Expansion:

File names can be represented by **wildcard characters**. The shell looks for existing names that match the pattern and replaces it with the results before command execution.

- `?` - Represents a single mandatory character. `ls doc?.txt` finds names such as `doc1.txt` and `docA.txt`, but not `doc12.txt`.
- `*` - Represents any sequence of characters, including empty. `rm *.tmp` deletes files ending in `.tmp`; `cat memo*` reads names starting with `memo`; `echo *__*` lists all files that have the characters `__` somewhere in their name (beginning, middle, and/or end).
- `[characters]` - Represents **one character from the set**. `ls report_[123].txt` finds reports 1, 2, and 3; `[a-d]` indicates a range.
- `[!characters]` or `[^characters]` - Represents a character that does not belong to the set. `ls file_v[^3].txt` ignores `file_v3.txt`, but may find `file_v1.txt` and `file_vA.txt`.
- `[[:class:]]` - Represents a character from a predefined class. `ls doc_[[:digit:]].txt` finds `doc_1.txt`, but not `doc_A.txt`.

| Class | Corresponding Characters |
| --- | --- |
| `alnum` / `alpha` | Letters and digits / only letters. |
| `digit` / `xdigit` | Decimal digits / hexadecimal digits (`0`–`9`, `A`–`F`, `a`–`f`). |
| `lower` / `upper` | Lowercase letters / uppercase letters. |
| `blank` / `space` | Space and tabulation / spacing characters, including line break. |
| `cntrl` | Control characters. |
| `graph` / `print` | Visible characters, without space / printable characters, including space. |
| `punct` | Punctuation characters. |

By default, wildcards do not include the initial dot of hidden names: it must appear explicitly, as in `.*`. If no name matches, the pattern remains literal and is passed that way to the command. Classes and letter ranges may depend on the language and region configuration.

> These patterns (globs) are not regular expressions. In a file pattern, `*` represents any sequence; in a regular expression, such as those used by `grep`, it repeats the previous element.

### Tilde Expansion (`~`):

The tilde works as a shortcut to personal directories: `~` expands to the current user's directory, while `~user` indicates another existing user's. It must appear in a position recognized by the shell, normally at the beginning of the word: `~/Documents`. In quotes (`"~/Documents"`), it remains literal.

### Arithmetic Expansion:

Executes calculations with **integers** and inserts the result into the command, using `$((expression))`. Addition (`+`), subtraction (`-`), multiplication (`*`), division (`/`), remainder (`%`), and exponentiation (`**`) are supported, in addition to comparison and logical operators.

> Internal spaces are ignored, and parentheses can organize the operations: `$(((5 ** 2)   * 3))` results in `75`. Integer division discards the fractional part: `$((5 / 2))` results in `2`.

### Brace Expansion:

Generates multiple words from a pattern between braces. Unlike wildcards, **it does not require existing files**.

- **Specific values:** `mkdir folder_{txt,pdf,img}` creates three folders: `folder_txt`, `folder_pdf`, and `folder_img`.
- **Value range:** `touch test_v{1..5}` generates names from `test_v1` to `test_v5`. Two dots (`..`) are used, not a hyphen.

It is possible to nest braces to combine patterns. Spaces inside the elements need to be protected: `{"A A",B}` generates two words, while `{A, B}` does not form the intended expansion.

> Since braces are expanded before variables, `{1..$n}` does not generate a sequence using the value of `n`.

### Parameter Expansion:

Allows retrieving values stored in the shell. The form `${variable}` explicitly delimits the name; `$variable` is its simplified form, sufficient when the name can be identified without ambiguity. `${USER}`, for example, retrieves the username registered in that variable.

Braces allow separating the name from adjacent text and applying additional operations, presented in the variables chapter. They **do not replace quotes** to preserve spaces: `${variable}` and `"${variable}"` may produce different arguments.

> `printenv` lists environment variables. In Bash, `set` without arguments also shows the shell's own variables and function definitions; therefore, the two queries are not equivalent.

### Command Substitution:

The form `$(command)` captures the **standard output** of a command and uses it as text. Trailing line breaks are removed; messages sent to standard error are not captured, unless they are redirected.

For example, `printf 'Date: %s\n' "$(date +%F)"` executes `date`, captures a date such as `2026-10-13`, and delivers it as an argument to `printf`. The quotes preserve the result as a single argument.

## Word Splitting:

When parameter or command expansions appear without quotes, their results can be split into words (word splitting) by the characters of `IFS` (by default, space, tabulation, and line break). After that, file patterns can also be expanded.

```bash
text="two words"
printf '<%s>\n' $text    # Prints <two> and <words>, on separate lines.
printf '<%s>\n' "$text"  # Prints <two words>, on a single line.
```

In command arguments, `"$variable"` preserves the value as one argument, including when it is empty. Without quotes, an empty value may disappear from the argument list. In simple assignments (`copy=$text`), this splitting and file expansion do not occur.

## Quotes and Escapes:

Quotes and escapes control which characters retain their special meaning and which should be treated literally.

- **Double Quotes (`"..."`):** Preserve spaces and prevent word splitting and file expansion. They still allow expansions such as `$variable`, `$(command)`, and `$((expression))`. `echo "The user $USER accessed the file"` keeps the text together and substitutes the variable.
- **Single Quotes (`'...'`):** Preserve literally everything between them. `echo 'The user $USER'` prints the text `$USER` itself; a single quote cannot be inserted directly inside this same pair of quotes.
- **Backslash (`\`):** Protects the next character outside single quotes. Inside double quotes, it acts only in specific cases, such as `\$`, `\"`, `\\`, backtick, and line break. `echo "The balance is \$5.00"` prints the dollar sign literally.

> A backslash immediately before a line break allows continuing the command on the next line, outside single quotes. Sequences such as `\n` depend on who interprets them: `printf 'Line 1\nLine 2\n'` produces two lines. In Bash, `echo -e` also interprets these sequences, but the behavior of `echo` varies with options and implementation.

---

# 6. Variables and Parameters

Variables associate a name with a value, which can be retrieved with `${name}` or `$name`. Braces delimit where the name ends: `${name}_copy` uses the variable `name`, while `$name_copy` looks for a variable called `name_copy`.

```bash
first="a life"
second="${first}is beautiful"
printf '%s\n' "$second"  # "a lifeis beautiful"
```

## Names, Assignment, and Values:

The name begins with a letter or underscore (`_`) and can continue with letters, numbers, and underscores. Characters such as `/`, `-`, and `*` are not allowed. Conventionally, the scripts' own names use lowercase, avoiding conflicts with known environment variables.

- `{variable}={value}` - Assigns a value, **without spaces around `=`**. To write text with spaces directly, quotes or escapes are used: `name="Ana Silva"`.
- `{variable}=` - Defines an empty text; the variable remains defined.
- `unset {variable}` - Removes the variable, unless it is protected against this.

Common variables store text; arithmetic contexts and specific attributes allow interpreting it as numbers. Therefore, `x=1`, `x="1"`, and `x='1'` produce the same value for a common variable.

```bash
var_1='script.*'
printf '%s\n' $var_1    # May expand to script.c, script.cpp etc., if they exist.
printf '%s\n' "$var_1"  # Prints literally script.*.
var_2=ls
"$var_2"                # Executes ls, as the expansion occupies the command name position. It would also work without double quotes
```

> The content of a variable is not reinterpreted as a complete line of code: `|`, `>`, and stored quotes do not automatically become operators or delimiters. The previous example works because it contains only the command name.

## Environment, Export, and Inheritance:

A variable created in the shell is not automatically sent to the programs executed by it. **Exporting** means including its name and value in the environment of these programs, not sharing a single variable between processes.

- `export {variable}={value}` - Creates or updates a variable and marks it for export. `export variable` exports an existing variable.
- `{variable}={value} {command}` - Provides a variable to the environment of that command, without changing its value in the shell that called it: `VAR="test" ./my_script.sh`.

### Isolation and Execution Context:

| Form | Variable Behavior |
| --- | --- |
| `bash script.sh` or `./script.sh` | Starts another interpreter; it receives the exported variables through the environment. Changes do not return to the original shell. |
| `( commands )` | Creates a subshell, copying the context, including non-exported variables and attributes. Changes remain isolated. |
| `source file.sh` or `. file.sh` | Executes in the current context. The changes may remain after the file ends. |

When a variable is passed through the **environment** to a new program, its name and value are transferred, not control attributes such as read-only. This differs from copying the context in a subshell. In both isolated cases, changing the copy does not modify the value in the original process.

## Shell Variables:

The shell maintains several variables with specific functions, conventionally in uppercase. Some are part of the environment, but others, such as prompt configuration variables, may exist only in the shell itself.

| Variable | Description |
| --- | --- |
| `$HOME` | Path of the current user's personal directory, normally used in the expansion of `~`. |
| `$PATH` | Directories, separated by `:`, in which executable programs are searched. |
| `$PS1` / `$PS2` | Main / secondary prompt, used when the shell waits for the continuation of a command. |
| `$IFS` | Internal Field Separator, used in the splitting of expansion results and by the `read` command. |
| `$PWD` | Path of the current working directory, maintained by the shell. |

> Manually changing `$PWD` does not change the process's working directory (how the shell identifies the path has no relation to this variable).

## Attributes and Scope:

- `declare -{attribute} {variable}={value}` - Assigns value and properties to the variable.

- `declare +{attribute} {variable}` - Removes an attribute when this is allowed.

Some important attributes are:

| Attribute | Function |
| --- | --- |
| `-x` | Marks for export, like `export`. |
| `-r` | Makes it read-only, like `readonly`: prevents assignment and removal with `unset`. It cannot be undone with `+r`. |
| `-i` | Evaluates assignments as integer expressions: `declare -i total=2+3` stores `5`. |

> Attributes can be combined: a variable can be exported and read-only at the same time. In Bash, the `typeset` command is equivalent to `declare` (alias).

### Locality in Functions:

By default, functions use the variables of the shell in which they execute. A global variable in this context is visible to functions, but **does not need to be exported**; export and scope are different properties.

- `local {variable}={value}` - Inside a function, creates a local variable that hides another of the same name while the function executes. Upon returning, the external variable becomes visible again.

The scope is dynamic: other functions called by this function can also see the local variable. Inside functions, `declare` also creates local variables by default; outside them, it does not replace `local`. The syntax of functions is reserved for its own chapter.

## Arrays:

- `{array}=(value1 value2 value3)` - Creates an indexed vector. When indices are not specified, the elements receive consecutive positions starting from zero.
- `{array}[{index}]={value}` - Assigns or changes an element. Access uses `${array[index]}`.
- `${#array[@]}` - Returns the number of elements; `${!array[@]}` allows obtaining their indices.

```bash
files=("final report.txt" "data.csv")
files[1]="new data.csv"
printf '%s\n' "${files[0]}"  # final report.txt
printf '%s\n' "${files[@]}"  # One element per line, preserving their spaces.
printf '%s\n' "${#files[@]}" # 2
```

`"${array[@]}"` preserves **each element as an independent argument**; `"${array[*]}"` brings all together into a single argument, separated by the first character of `IFS` (space by default). Without quotes, word splitting and file expansion may occur, losing the original limits of the elements.

> Indices do not need to be continuous. Therefore, the number of elements is not necessarily the highest index plus one.

## Special Variables and Parameters:

They provide information about execution. Special parameters, such as `$?` and `$$`, are not common names to which a value is assigned with `=`.

| Form | Description |
| --- | --- |
| `$RANDOM` | Produces a pseudorandom integer from `0` to `32767` (15 bits). |
| `$$` | PID of the original shell; it keeps this value in subshell contexts, such as `( ... )`. `$BASHPID` identifies the Bash process executing in that context. |
| `$!` | PID associated with the most recent command or job started in the background. |
| `$?` | Exit status of the last command or pipeline, according to the redirection and execution rules. |

> `$?` is updated after each command. After `echo $?`, it indicates the result of this `echo` itself; to save a result, the assignment `result=$?` must come immediately after the command of interest.

## Reading Input:

- `read {options} {variables}` - Reads a line from standard input and distributes its fields among the variables. The last one receives the rest of the fields and their internal separators; with one variable, it receives the entire text after the `IFS` treatment.

By default, spaces, tabulations, and line breaks work as separators, and spaces at the edges are removed. The `-r` option prevents backslashes from being interpreted as escapes. To also preserve spaces, use `IFS= read -r line`.

```bash
printf 'Full name: '
IFS= read -r name
printf 'Name received: <%s>\n' "$name"
```

> If no variables are passed to `read`, it stores the line in the `REPLY` variable (implicitly), without splitting it into fields. Its status indicates success or a condition such as end of input, which allows using it in tests and, later, in repetitions.

## Positional Parameters:

They represent the arguments passed to a script or function, similarly to `argc` and `argv` in C. They are different from the data read from standard input by `read`.

| Form | Description |
| --- | --- |
| `$#` | Number of positional arguments; it does not include `$0`. |
| `$0` | Name used to call the shell or script. It does not become the name of a function when it is called. |
| `$1` to `${n}` | Arguments by position. From the tenth onward, braces are mandatory: `${10}`, `${11}` etc. |
| `"$@"` | All arguments, each preserved separately, as in `"${array[@]}"`. |
| `"$*"` | All brought together into one argument, separated by the first character of `IFS`. |

- `shift {n}` - Discards the first `n` arguments and shifts the remaining ones: with `shift` (equivalent to `shift 1`), `$2` becomes `$1`, `$3` becomes `$2`, etc. It does not change `$0`.
- `set -- {values}` - Redefines the positional parameters; `--` ends the `set` options. Without values after `--`, it removes all positional arguments.

```bash
set -- "Ana Silva" "final report.txt"
printf '%s\n' "$#"  # 2
printf '<%s>\n' "$@" # Two complete arguments, one per line.
shift
printf '%s\n' "$1"  # final report.txt
```

## Expansion and Value Manipulation:

The `${...}` forms allow applying default values, tests, and operations on the text during expansion. In general, they modify **the expanded result**, without changing the original variable; assignment with `:=` is an exception.

### Default Values and Conditionals:

An **undefined** variable has no assigned value; an **empty** variable is defined but contains zero characters. In the operators below, `:` makes the check include both situations. Without it, only whether the variable is undefined is tested.

- `${variable:-default}` - Returns `default` if the variable is undefined or empty; otherwise, returns its value.
- `${variable:=default}` - In addition to returning, assigns `default` if the variable is undefined or empty.
- `${variable:?message}` - Writes an error to standard error if the variable is undefined or empty. In this case, a non-interactive shell terminates execution with an error status. If the variable is defined and non-empty, the expansion returns its value.
- `${variable:+alternative}` - Returns `alternative` only if the variable is defined and non-empty; otherwise, it produces empty text.

```bash
name=
printf '<%s>\n' "${name-default}"  # <>: defined, although empty.
printf '<%s>\n' "${name:-default}" # <default>: the test also considers empty.
: "${name:=default}"               # Assigns the default without trying to execute it.
```

> **Null command (`:`):** Performs no action and returns success. In the last line, it receives the expansion result as an argument; if `${name:=default}` were written alone in the command position, the shell would try to execute the resulting value.

### Text Manipulation:

- `${#variable}` - Number of characters in the text. For vectors, `${#vector[@]}` counts elements.
- `${!variable}` - Indirect expansion: uses the value of one variable as the name of another and retrieves its content, reminiscent of pointer dereferencing.
- `${variable:start:length}` - Extracts a part of the text, starting from the index `start` (beginning at zero). Without `length`, it goes to the end.
- `${variable/pattern/new}` - Replaces the first match of a pattern; `${variable//pattern/new}` replaces all.
- `${variable#pattern}` / `${variable##pattern}` - Removes the shortest / longest match of the pattern at the beginning of the text.
- `${variable%pattern}` / `${variable%%pattern}` - Removes the shortest / longest match of the pattern at the end of the text.

Patterns follow the shell's matching rules, not regular expressions. The duplicated forms make a difference when a wildcard allows more than one match:

```bash
file="report.final.txt"
printf '%s\n' "${file%.*}"  # report.final
printf '%s\n' "${file%%.*}" # report
printf '%s\n' "${file#*.}"  # final.txt
printf '%s\n' "${file##*.}" # txt
```

---

# 7. Conditional Structures

In the shell, conditions evaluate the **exit status** of commands: `0` means success and allows executing the true branch; values other than zero indicate a false condition or error. This resembles the return of success of a C program, but differs from a logical expression in C, in which zero is false.

> The internal commands `true` and `false` directly produce the statuses `0` and `1`, respectively. It is not necessary to use a tester for every command: `if grep -q 'error' log.txt; then ...` can already decide based on the result of `grep`.

## Expression Tests:

- `test {expression}` or `[ {expression} ]` - Tests files, texts, and integers, producing a status.

> `[` is a Bash internal command (there are also external versions), and `]` is its final argument; therefore, **spaces are mandatory**.

- `[[ {expression} ]]` - Bash construct that adds pattern and regular expression tests. It does not perform word splitting nor file expansion on values; its syntax and some operators differ from `[ ... ]`.

- `(( expression ))` - Evaluates an integer expression. If the numeric result is different from zero, it returns status `0`; if it is zero, it returns status `1`. It allows operators such as `>`, `>=`, `&&`, `||`, and `!`, without requiring spaces next to the parentheses.

**Most Used Tests:**

| Purpose | Expression with `test` or `[` |
| --- | --- |
| Existing path | `[ -e "$path" ]` |
| Regular file / directory | `[ -f "$path" ]` / `[ -d "$path" ]` |
| Read / write / execute allowed | `[ -r "$path" ]` / `[ -w "$path" ]` / `[ -x "$path" ]` |
| Empty / non-empty text | `[ -z "$text" ]` / `[ -n "$text" ]` |
| Equal / different texts | `[ "$a" = "$b" ]` / `[ "$a" != "$b" ]` |
| Equal / different integers | `[ "$a" -eq "$b" ]` / `[ "$a" -ne "$b" ]` |
| Smaller / smaller or equal integer | `[ "$a" -lt "$b" ]` / `[ "$a" -le "$b" ]` |
| Greater / greater or equal integer | `[ "$a" -gt "$b" ]` / `[ "$a" -ge "$b" ]` |

These tests can also be used in `[[ ... ]]`. In `[ ... ]`, quotes are important to keep empty values or values with spaces as complete arguments. A false test normally returns `1`; an invalid expression may return another error status.

### Text, Patterns, and Arithmetic:

In `[[ ... ]]`, `<` and `>` compare **texts**, according to the language and region ordering; for integers, `-lt`, `-gt`, etc. or `(( ... ))` are used. There are no `<=` and `>=` operators for textual comparison in this construct.

With `==` and `!=`, the right side can be a shell pattern: `[[ $file == *.txt ]]` tests the `.txt` ending, while `[[ $file == "*.txt" ]]` compares with the literal text `*.txt`. The `=~` operator allows testing a regular expression, as in `[[ $number =~ ^[0-9]+$ ]]`.

> `$((expression))` **expands a value**, while `((expression))` **executes an evaluation and provides a status**. For example, `$((2 > 1))` produces `1`, but `((2 > 1))` ends with status `0`, indicating that the condition is true.

## Decision Structures:

> To close a decision structure (delimitation), the opposite structure name is used. `if` uses `fi`, and `case` uses `esac`.

### `if`, `elif`, and `else`:

Executes the first block whose condition ends with success. `elif` allows testing another condition; `else` covers the case in which none succeeded. Both are optional.

```text
if {tested_command}; then
    {commands_success_case}
elif {other_command}; then
    {alternative_success_case_commands}
else
    {failure_case_commands}
fi
```

```bash
#!/bin/bash

file="report.txt"

if [ ! -e "$file" ]; then                    # Tests whether the file does not exist.
    printf 'Nonexistent file.\n'
elif [[ -f "$file" && -r "$file" ]]; then # Tests whether it is a regular file and has read permission.
    if test ! -s "$file"; then               # Tests whether the file is empty
        printf 'The file is empty.\n'
    else
        cat "$file"
    fi
else
    printf 'The path is not a readable regular file.\n' >&2
fi
```

The `!` negates the test result, and `&&` requires success of both commands. The semicolon before `then` separates commands on the same line; it can be replaced by a line break.

### `case`:

Compares a text with patterns, from top to bottom, executing the first one that matches. Patterns follow the shell's rules, allowing `*`, `?`, and sets between brackets; `|` separates alternatives for the same block.

```bash
file="report.txt"
case "$file" in
    *.txt | *.md)
        printf 'Text file.\n'
        ;;
    *.csv)
        printf 'Data table.\n'
        ;;
    *)
        printf 'Unidentified type.\n'
        ;;
esac
```

`;;` ends the selection after the matching block. The `*` pattern accepts any text and works as a default alternative when placed last; at the beginning, it would capture all cases.

> Bash also offers `;&`, which continues to the next block without testing its pattern, and `;;&`, which continues testing the following patterns. Therefore, there are cascade forms, although `;;` is sufficient for basic use.

## Short-Circuit Operators:

Chain commands based on their results, avoiding unnecessary executions:

- `{command_1} && {command_2}` - *AND* logic: executes the second only if the first ends with success.
- `{command_1} || {command_2}` - *OR* logic: executes the second only if the first fails.
- `! {command}` - Inverts the logical result: success becomes failure and failure becomes success.

The status of a list with `&&` or `||` is that of the last command actually executed. These operators have the same precedence among themselves and are evaluated from left to right.

> `a && b || c` is not equivalent to a general `if/else`: `c` also executes when `a` succeeds but `b` fails. When the intention is to choose between two branches, `if/else` makes this decision explicit.

---

# 8. Repetition Structures

Execute a block of commands several times, traversing a list or evaluating a condition. In Bash, `for`, `while`, `until`, and `select` delimit the body with **`do`** and **`done`**.

> The semicolon before `do` separates commands on the same line. It can be replaced by a line break, as occurs before `then` in conditional structures.

## `for`:

Traverses a list like a foreach, assigning each element to the variable and executing the body once for it. An empty list produces no iterations. The list can be provided in different ways:

- **Explicit list:** `in apple orange banana` provides the elements directly; quotes preserve spaces within an item.
- **Brace expansion:** `in {1..10}` generates the numbers from 1 to 10 before loop execution. As seen in expansions, `{1..$n}` does not use the value of `n` as a limit.
- **Array:** `in "${files[@]}"` traverses the elements preserving their limits, including spaces within names.
- **File name expansion:** `in *.sh` traverses the corresponding names in the current directory.

```bash
files=("final report.txt" "notes.md")
for file in "${files[@]}"; do
    cat -- "$file"
done
```

A simple variable without quotes, as in `for file in $files`, can split names with spaces. The use of `"${files[@]}"` preserves the elements (as explained in chapter 6).

> In `for script in *.sh`, each found name is preserved; in the body, `"$script"` is used. Without matches, `*.sh` remains literal by default, possibly requiring a file test before processing it.

### Positional Arguments:

Omitting `in {list}` is equivalent to using `in "$@"`, traversing the positional arguments (passed as parameters during the function call in the terminal) separately:

```bash
for argument; do
    printf 'Processing the argument: %s\n' "$argument"
done
```

### Arithmetic Form:

Bash offers a form with **initialization**, **condition**, and **update** (similar to C's `for`):

```bash
for ((i=1; i<=5; i++)); do
    printf 'Number: %s\n' "$i"
done
```

`i=1` initializes the counter once; `i<=5` is evaluated before each iteration; `i++` occurs after the body. The condition allows continuing while its numeric result is different from zero. The limit can also be a variable, as in `i<=limit`.

## `while` and `until`:

Evaluate the status of a command before each iteration, and can execute **zero times**. As in `if`, they can use testers or other commands:

- **`while` (while):** Executes the body while the command returns success (`0`).
- **`until` (until):** Executes the body while the command returns a status different from zero; it ends when it returns success.

```bash
counter=1
while [ "$counter" -le 5 ]; do
    printf 'Number: %s\n' "$counter"
    ((counter++))
done
```

> Replacing the line `while [ "$counter" -le 5 ]; do` with `until [ "$counter" -gt 5 ]; do` produces the same count: it repeats until the counter exceeds 5.

### Reading Files by Line:

`read` returns success when reading a line, allowing the repetition to be controlled. `IFS= read -r` preserves spaces and backslashes:

```bash
while IFS= read -r line; do
    printf 'Line: %s\n' "$line"
done < file.txt
```

The redirection after `done` provides input for the entire loop, including empty lines. On the other hand, `for line in $(cat file.txt)` splits words, losing the line limits.

> To include a last piece without a line break, the condition `IFS= read -r line || [ -n "$line" ]` is used. The redirection also avoids the subshell normally created by `cat file.txt | while ...`, keeping variable changes in the current context.

## Repetition Control:

- `break` - Ends the current loop immediately and continues after `done`.
- `continue` - Ignores the rest of the current iteration and moves to the next one. In the list `for`, it advances to the next element; in the arithmetic one, it executes the update and tests again; in `while` and `until`, it returns to the test.

```bash
for ((i=1; i<=5; i++)); do
    if ((i == 3)); then
        continue
    fi
    printf '%s\n' "$i"
done
# Prints 1, 2, 4, and 5, one per line.
```

> In a `while`, a `continue` placed before the counter update can prevent the stop condition from being reached. In the arithmetic `for` above, `i++` is part of the structure itself and continues to be executed.

## `select`:

Displays a numbered list and waits for a choice. Its syntax is similar to `for`; if the list is omitted, it uses the positional arguments (`"$@"`).

A valid number assigns the corresponding item to the variable; an invalid entry leaves that variable empty. **`REPLY`** stores the typed text. After executing the body, `select` waits for another choice, until it finds a `break` or the end of input. An empty line redisplays the menu without executing the body.

```bash
PS3="Choose an option: "
select fruit in apple banana kiwi orange exit; do
    if [ "$fruit" = "exit" ]; then
        printf 'Closing the menu...\n'
        break
    elif [ -n "$fruit" ]; then
        printf 'You chose: %s\n' "$fruit"
    else
        printf 'Invalid option: %s\n' "$REPLY"
    fi
done
```

> **`PS3`** is the shell variable that defines the `select` prompt, whose default value is `#? `. It does not need to be exported. The menu and the prompt are written to standard error; the choice is read from standard input.

---

# 9. Regular Expressions

A **regular expression** (regex) describes a text pattern: expected letters, positions, alternatives, and repetitions. It is like defining a set of possible texts, where a sequence that meets the rules **matches** the expression. Tools such as `grep`, `sed`, and `awk` use these patterns to search for or transform text.

> Regex and shell expansions have different rules. In `*.txt`, the `*` represents any sequence in shell expansion; in regex, it repeats the previous element. Globs also appear in `case` and in operations on variables, not only in file name expansion.

## Basic and Extended Syntaxes:

The **BRE** (Basic Regular Expressions) and **ERE** (Extended Regular Expressions) syntaxes differ in the writing of some operations. `grep` and `sed` use BRE by default; `grep -E`, `sed -E`, `awk`, and Bash's `=~` operator use ERE.

| Construct | BRE | ERE |
| --- | --- | --- |
| Group | `\(...\)` | `(...)` |
| Delimited repetition | `\{n,m\}` | `{n,m}` |
| Literal parentheses | `(text)` | `\(text\)` |

In ERE, `+`, `?`, and `|` are also operators. GNU `grep` and GNU `sed` accept `\+`, `\?`, and `\|` in BRE as extensions, but these forms are not universal in POSIX BRE. For these features, the following examples use ERE.

The single quotes in `grep -E 'pattern' file.txt` protect the pattern from shell interpretation. The backslash that reaches the tool is then interpreted according to the regex syntax.

> By default, `grep` prints the **lines** that contain matches. The `-o` option shows only the found snippets, one per line; `-F`, already presented in chapter 1, searches for literal text.

## Literals, Metacharacters, and Anchors:

**Literal** characters represent themselves, such as `a` and `7`. **Metacharacters** organize the pattern; their meaning depends on the position and the syntax used.

- `.` - Matches one character. In line searches with `grep`, it does not cross the separation between lines.
- `[characters]` - Matches one character from the set. `[abc]` accepts `a`, `b`, or `c`; `[a-z]` indicates a range.
- `[[:class:]]` - Uses a POSIX class, such as `[[:digit:]]` for a digit and `[[:alpha:]]` for a letter. The classes and the influence of language and region were presented in chapter 5.
- `[^characters]` - Negates the set when `^` appears right after `[`. Thus, `[^[:alnum:]]` matches a character that is not alphanumeric.
- `^` and `$` - Anchor the pattern at the beginning and at the end of the considered text. In line searches, `^error$` requires exactly `error`; without anchors, it also finds this snippet in `an error occurred`.
- `\` - Changes the interpretation of the next character according to the syntax. `\.` searches for a literal dot; in BRE, `\(` starts a group. Therefore, the escape does not only serve to remove special meaning.

> Delimiters such as `/` in `sed 's/pattern/new/'` belong to the tool's syntax. They are not a mandatory part of the regex: `grep 'pattern' file.txt` does not use them.

## Repetitions, Groupings, and Alternation:

**Quantifiers** indicate how many times the previous element can occur. In ERE syntax:

| Quantifier | Number of Occurrences |
| --- | --- |
| `*` | Zero or more. |
| `+` | One or more. |
| `?` | Zero or one. |
| `{n}` | Exactly `n`. |
| `{n,m}` | From `n` to `m`. |
| `{n,}` | At least `n`. |

The braces of these quantifiers are part of the real expression; only the values `n` and `m` are replaced. The `*` operator also works without escape in BRE.

> For "up to `m` times," `{0,m}` is used. The abbreviated form `{,m}`, accepted by GNU `grep`, is an extension and should not be presumed in any tool.

In ERE, **`(...)`** groups elements and **`|`** separates alternatives. A quantifier after the group applies to the set, not only to the last character:

```bash
grep -E '\.(bz|gz|zip)$' file_list.txt
# List of names, one per line: accepts file.zip, but not file.zip.bak.
```

The escaped dot is literal, the group brings together the alternative extensions, and `$` requires that they be at the end.

```bash
grep -Eo '[[:upper:]][[:alpha:]]*' text.txt
# Extracts snippets with an uppercase letter followed by zero or more letters.
```

This pattern finds `This` in `This works`, but also `Bar` inside `fooBar`; it does not, by itself, require the beginning of a word.

The phone example combines alternatives and quantifiers:

```bash
grep -E '^(\([0-9]{2}\)|[0-9]{2}) [0-9]{4,5}-?[0-9]{4}$' contacts.txt
# Accepts (31) 9999-9999, 31 99999999, and 31 99999-9999.
```

The first part accepts two digits **with the pair of parentheses or without both**. Then it requires a space, four or five digits, an optional hyphen, and four more digits. The anchors require that the entire line follow this format; the test does not verify whether the phone exists.

### Greedy Behavior:

In the POSIX model, the match that starts furthest to the left and, at that position, the longest possible is sought. Thus, a repetition can span intermediate separators as long as the complete pattern continues to match.

Consider `this (rug) is not what it once was (a long time ago), is it?`:

- `th.*is` matches `this (rug) is not what it once was (a long time ago), is`, going to the last possible `is`.
- In **ERE**, `\(.*\)` matches `(rug) is not what it once was (a long time ago)`, from the first `(` to the last `)`. In BRE, the escaped parentheses would have a grouping function.

```bash
printf '%s\n' 'this (rug) is not what it once was (a long time ago), is it?' |
    grep -Eo '\(.*\)'
```

### References and Groups:

In the `sed 's/pattern/replacement/'` operation, the pattern identifies the snippet that will be replaced. To reuse the found text, the replacement field allows using `&`, which represents the complete match, and `\1`, `\2`, etc., which represent parts captured by groups.

These references retrieve the content found in each match, without needing to know its value in advance. This allows wrapping a snippet with other characters, repeating its content, or reorganizing its parts.

```bash
printf '%s\n' '/bin/bash' | sed 's/[^/]*sh$/__&__/'
# Output: /bin/__bash__
```

The pattern `[^/]*sh$` finds a sequence without `/`, ending in `sh` at the end of the line. In this example, the match is `bash`; therefore, `__&__` produces `__bash__`. The prefix `/bin/` remains because it is outside the replaced snippet: **the complete match is not necessarily the entire line**.

To retrieve only parts of a match, **capture groups** are used, delimited by `\(...\)` in the basic syntax. Each group stores the snippet found by the expression inside it and receives a number by the opening order, from left to right. In the replacement, `\1` retrieves the first group, `\2` the second, and so on.

```bash
printf '%s\n' '/bin/bash' | sed 's/\([^/]*sh\)$/__\1__/'
# Output: /bin/__bash__
```

Here, the group surrounds the entire found snippet; therefore, `\1` and `&` equally represent `bash`. The group is dispensable in this case, but it allows selecting parts separately when the match contains more information.

Consider `megadeth.txt` in the "Last name, First name" format:

```text
Mustaine, David S.
Menza, Nick
```

```bash
sed 's/^\([^,]*\), \(.*\)$/\2 \1/' megadeth.txt
```

- `^\([^,]*\)` - First group: captures the last name, from the beginning of the line to before the comma.
- `, ` - Literal comma and space: they are part of the match, but remain outside the groups.
- `\(.*\)$` - Second group: captures the first name, after the separator to the end of the line.
- `\2 \1` - Replaces the found snippet with the second group followed by the first, separated by a space.

```text
David S. Mustaine
Nick Menza
```

In the first line, `\1` represents `Mustaine`, `\2` represents `David S.`, and `&` represents `Mustaine, David S.`. Therefore, using only `&` in the replacement would keep the original text, without inverting the names. It is also possible to combine the references:

```bash
sed 's/^\([^,]*\), \(.*\)$/& -> \2 \1/' megadeth.txt
# Mustaine, David S. -> David S. Mustaine
# Menza, Nick -> Nick Menza
```

> With `sed -E`, groups use parentheses without escape: `sed -E 's/^([^,]*), (.*)$/\2 \1/' megadeth.txt`. The references in the replacement remain `\1`, `\2`, etc. To insert a literal `&` in this field, `\&` is used; its meaning of complete match is a rule of the `sed` replacement, not a universal regex operator.

## Regular Expressions in Bash Tests:

The **`=~`** operator uses an extended regular expression in `[[ ... ]]`, as introduced in chapter 7. The test returns success when it finds a match; anchors allow requiring that the entire value follow the pattern.

```bash
number="12345"
pattern='^[0-9]+$'
if [[ $number =~ $pattern ]]; then
    printf 'The text contains only digits.\n'
fi
```

The quotes in the assignment preserve the regex as text. In the test, **`$pattern` is left without quotes** so that its operators are interpreted as regex; `"$pattern"` would make a literal comparison with the expanded content. In this context, word splitting and file name expansion do not occur.

---

# 10. `sed`

`sed` (stream editor) applies editing instructions to the text of a file or standard input (via pipe, for example). By default, it reads one line at a time and sends the result to standard output.

## Syntax:

> The single quotes surrounding the commands are important because they make the shell pass the program literally to the command.

- `sed {options} '{commands}' {input_files}` - Performs the editing on the informed files and prints the changes to standard output (without any modification to the original file).

    - `sed -f {file.sed} {input_files}` - Reads the commands from a file (good for larger programs). The `.sed` extension is a convention (as is `.sh` in shell scripts).
    - `sed -n '{commands}' {input_files}` - Disables automatic printing; commands such as `p` allow choosing what to print.
    - `sed -E '{commands}' {input_files}` - Enables extended regular expressions.
    - `sed -i{suffix} '{commands}' {input_files}` - Edits the file on disk. With a suffix, such as `-i.backup`, it keeps a copy of the original. Without a suffix (only `-i`), it edits without creating a copy.

    > `-i` requires that the file to edit be informed as an argument (it does not work with data received through standard input).

The examples use the file `service.conf`.

```text
# Local service
host=localhost
port=8080
routes=/test/api,/test/status
```

To view the configuration with the port changed:

```bash
sed 's/^port=8080$/port=9000/' service.conf
```

The output contains the four lines, with `port=9000` on the third (being the only one changed). The file on disk remains with `port=8080`.

## Structure and Operation:

An instruction has the form **address + command + arguments**.

- The address is optional and selects where to execute.
- The command defines the operation and determines which arguments it receives (and whether it needs to receive any).

In `3s/8080/9000/`, for example, `3` selects the third line, `s` indicates substitution, and `/8080/9000/` informs what to search for and what to put in its place.

The space that contains the text being edited is called the ***pattern space***. In the basic cycle, `sed` reads a line into this space, **traverses the instructions in the written order**, and prints the resulting content. The cycle restarts with the next line.

An instruction can, for example, modify the text that the next one will find:

```bash
sed -n 's/^port=/PORT /; /^PORT /p' service.conf   # Prints only "PORT 8080"
```

The first instruction transforms `port=8080` into `PORT 8080`. The second finds the beginning `PORT ` in the already changed text and prints it. The `;` separates instructions that accept this form. In a `.sed` file, they can also occupy distinct lines.

> The opposite (`sed -n '/^PORT /p; s/^port=/PORT /' service.conf`) would not work the same way: It would not find any "PORT" and would not print anything, but it would continue performing the substitution.

### Selection by Address:

| Address | Where the instruction applies | Example |
| --- | --- | --- |
| Omitted | On all lines. | `p` prints each line. |
| Number | On the indicated line, counting from 1. | `3p` prints the third. |
| `$` | On the last line of the input. | `$p` prints the last one. |
| `/expression/` | On the lines whose content matches the regular expression. | `/^port=/p` prints the port configuration. |
| `start,end` | In the interval between the two addresses, including the extremes. | `/^host=/,/^port=/p` prints the snippet from `host` to `port`. |

```bash
sed -n '2,3p' service.conf
# host=localhost
# port=8080
```

The `p` command prints the current content immediately. Without `-n`, lines 2 and 3 would appear **twice**, once by the `p` instruction and once by automatic printing. The others would appear once.

## Editing:

### Snippet Substitution:

`s/pattern/replacement/` replaces the first match in each selected line. The `g` option extends the replacement to the remaining matches **of the same line**. Compare the outputs for the routes configuration:

```bash
sed -n '/^routes=/{s|/test|/prod|;p;}' service.conf
# routes=/prod/api,/test/status

sed -n '/^routes=/{s|/test|/prod|g;p;}' service.conf
# routes=/prod/api,/prod/status
```

The address `/^routes=/` selects the line. The braces group the two instructions applied to the selected line: substitute and print. The delimiter of `s` can be a character other than `/`. Here, `|` makes the bars that belong to the paths legible.

> In the replacement, `&` retrieves the entire found snippet and `\1`, `\2`, etc. retrieve captured groups (chapter 9).

### Removing, Inserting, and Changing Lines:

| Instruction | Effect |
| --- | --- |
| `d` | Discards the current content and starts the next cycle, skipping the remaining instructions and automatic printing. |
| `i` | Inserts text before the selected line. |
| `a` | Appends text after the selected line (append). |
| `c` | Changes the entire line to the indicated text. Applied to an interval, it replaces the block with a single occurrence of the text. |

To obtain only the active configurations of the example, `sed '/^#/d' service.conf` discards the comment line. To also insert a timeout after the port, the two operations can be placed in the `configure.sed` file:

```sed
/^#/d
/^port=/a\
timeout=30
```

```bash
sed -f configure.sed service.conf
```

```text
host=localhost
port=8080
timeout=30
routes=/test/api,/test/status
```

> The backslash after `a` indicates that the text starts on the next line of the program. With `i`, the `timeout=30` option would appear before the port. With `c`, it would take the place of the `port=8080` line. The text provided to these commands is also emitted when `-n` is used.

### Termination, Reading, and Writing Files:

| Instruction | Effect | Example |
| --- | --- | --- |
| `q` | Ends reading. With automatic printing active, it prints the current line before exiting. | `sed '2q' service.conf` shows the first two lines. |
| `r file` | Appends the contents of another file to the output after the selected line. | `sed '/^port=/r extras.conf' service.conf` inserts the options from `extras.conf` after the port. |
| `w file` | Writes the content being processed to the destination file. | `sed -n '/^routes=/w routes.conf' service.conf` writes only the routes configuration. |

With `-n`, `r` continues sending the read text to the output and `w` continues writing to the destination. The option controls the automatic printing of the pattern space.

## Processing More Than One Line:

Consider the file `measurements.txt`, in which each measurement occupies two consecutive lines. The first identifies the sensor and the second contains the reading.

```text
temperature
24 C
humidity
60 %
```

### Advancing the Reading:

- **`n`** - Prints the current content if automatic printing is active, replaces this content with the next line, and continues at the next instruction.
- **`N`** - Appends the next line to the current content, preserving a line break between the texts, and continues at the next instruction.

```bash
sed -n 'n;p' measurements.txt
# 24 C
# 60 %

sed 'N;s/\n/: /' measurements.txt
# temperature: 24 C
# humidity: 60 %
```

In the first command, the cycle begins with the sensor name. `n` replaces the sensor name with the measurement value and `p` prints this value. In the second, `N` keeps the name and the value together, allowing `s` to replace the internal line break with "`: `".

### Auxiliary Memory (Hold Space):

The ***hold space*** is a reserve that starts empty and keeps its content between cycles. It allows saving a line, continuing reading, and retrieving the text when necessary.

| Instruction | Origin - Destination | Effect on destination |
| --- | --- | --- |
| `h` | Pattern space - Hold space | Replaces the stored content with a copy of the current one. |
| `g` | Hold space - Pattern space | Retrieves a copy of the stored content. |
| `H` | Pattern space - Hold space | Appends a line break and the current content. |
| `G` | Hold space - Pattern space | Appends a line break and the stored content. |
| `x` | Pattern space - Hold space and Hold space - Pattern space | Swaps the contents of the two spaces. |

To display **value followed by the sensor name in parentheses**, it is necessary to keep the name while reading the measurement:

```bash
sed -n 'h;n;G;s/\n/ (/;s/$/)/;p' measurements.txt
# 24 C (temperature)
# 60 % (humidity)
```

| After the operation on the first pair | Pattern space | Hold space |
| --- | --- | --- |
| Reading `temperature` | `temperature` | Empty |
| `h` stores the name | `temperature` | `temperature` |
| `n` reads the value | `24 C` | `temperature` |
| `G` appends the name to the value | `24 C\ntemperature` | `temperature` |

In the table, `\n` represents the internal line break. The two substitutions shape the result and `p` prints it. In the next pair, `h` updates the stored name to `humidity`.

> `H` and `G` append the line break even if the destination is empty. To accumulate lines without starting with an empty line, use `h` to store the first and `H` to append the following ones. The isolated `g` command retrieves the reserve. The `g` at the end of `s/pattern/text/g` indicates global substitution.

---

# 11. AWK

> This chapter uses **GNU AWK (`gawk`)**. Other implementations (such as those used in BSD systems) may present feature differences. On systems where `awk` calls `gawk`, it works, in practice, as an alias of that program.

AWK is a text processing language. It reads files or standard input (via pipe, for example), divides the text into records, and separates each record into fields.

## Syntax:

> The single quotes surrounding the commands are important because they make the shell pass the program literally to the command.

- `awk {options} '{commands}' {input_files}` - Performs the commands on the informed files.

    - `awk -f {file.awk} {input_files}` - Reads the commands from a file (good for larger programs). The `.awk` extension is a convention (as is `.sh` in shell scripts).
    - `awk -F '{separator}' '{commands}' {input_files}` - Defines how to separate the input fields. By default, they are spaces and tabulations.
    - `awk -v {var_1=value_1} -v {var_2=value_2} ... '{commands}' {input_files}` - Provides an initial value to a program variable.

The examples use `products.txt`. Each line records a product and the value of a purchase. A product may appear in more than one purchase.

```text
Rice;25
Beans;8
Coffee;15
Rice;12
```

```bash
awk -F ';' '{ print $1 }' products.txt
# Rice
# Beans
# Coffee
# Rice
```

By default, each line is a record. With `-F ';'`, the semicolon separates its fields. In the first record, `$0` contains the entire line (`Rice;25`), `$1` contains `Rice`, and `$2` contains `25`. The action `{ print $1 }` prints the first field at each read.

## Structure and Operation:

A program is formed by rules with the structure **`pattern { action }`**. The pattern determines when to execute the action. The braces delimit the instructions and are part of the code. To obtain the product names in purchases of at least 15:

```bash
awk -F ';' '$2 >= 15 { print $1 }' products.txt
# Rice
# Coffee
```

For each record, **AWK evaluates the rules in the written order**. More than one rule can be executed for the same record. If an action modifies a field, the following rules already receive the new value. Within an action, line breaks or `;` separate the instructions. Outside texts and regular expressions, `#` starts a comment until the end of the line.

- **Without a pattern**, the action is executed for all records, as in `{ print $1 }`.
- **Without an action**, AWK prints the selected record. With the same separator, `$2 >= 15` alone prints `Rice;25` and `Coffee;15`.

### Records, Fields, and Separators:

| Element | Meaning |
| --- | --- |
| `$0` | Current complete record. |
| `$1`, `$2` etc. | Record fields, numbered starting from 1. |
| `NF` | Number of fields in the current record. `$NF` accesses the last field. |
| `NR` | Number of the current record, with continuous counting between files. |
| `FILENAME` | Name of the current input file. |
| `FS` | Input field separator. `-F ';'` is equivalent to starting with `FS = ";"`. |
| `RS` | Input record separator. The default is the line break. |
| `OFS` | Separator inserted between `print` arguments separated by commas. The default is a space. |
| `ORS` | Separator appended to the end of each `print`. The default is a line break. |

## Selection by Pattern:

| Pattern | Selection in `products.txt`, with `-F ';'` |
| --- | --- |
| `/Rice/` | Records that contain the snippet `Rice` in any position. |
| `$1 == "Rice"` | Records whose first field is exactly `Rice`. |
| `$1 ~ /^B/` | Products whose name starts with `B`. The `!~` operator inverts the test. |
| `$2 >= 10 && $2 < 20` | Purchases of value greater than or equal to 10 and less than 20. |
| `NR == 2, NR == 3` | From the second to the third record, including both. |

Comparisons accept `==`, `!=`, `<`, `<=`, `>`, and `>=`. To combine conditions, `&&` requires both to be true and `||` requires at least one to be true. An isolated regular expression tests `$0`. To search for a snippet, write `/Rice/`. The isolated string `"Rice"` is a true condition because it is a non-empty text.

Two patterns separated by a comma open and close an interval. Once opened, it includes the following records until it finds the final pattern or reaches the end of the input. After being closed, the search for the next start restarts on the next record.

### Before and After Reading:

`BEGIN` executes an action before the first record. `END` executes an action after the complete processing of the input. Between these moments, a variable can accumulate values from several records. The `select.awk` program sums only purchases of value greater than or equal to 15:

```awk
BEGIN {
    FS = ";"
    OFS = " | "
    print "Product", "Value"
}

$2 >= 15 {
    print $1, $2
    total += $2
}

END {
    print "Total", total + 0
}
```

```bash
awk -f select.awk products.txt
```

```text
Product | Value
Rice | 25
Coffee | 15
Total | 40
```

`BEGIN` prints the header once. The central rule selects two records and sums 25 and 15. Variables do not require declaration and are worth zero in a numeric context or empty text in a textual context until they receive a value. The expression `total + 0` also displays a numeric zero if no purchase passes the filter.

## Output and Formatting:

`print` prints the entire record when used alone. With arguments separated by commas, it inserts `OFS` between them and appends `ORS` at the end. To visualize the difference, compare the two instructions applied to the first record:

```bash
awk -F ';' -v OFS=' | ' 'NR == 1 { print $0; print $1, $2 }' products.txt
# Rice;25
# Rice | 25
```

In the first `print`, the line still contains the original separator. In the second, AWK receives two arguments and separates them with `OFS`. If an action assigns a new value to a field, AWK will rebuild `$0` using `OFS`:

```bash
awk -F ';' -v OFS=';' '$1 == "Coffee" { $2 += 2; print }' products.txt
# Coffee;17
```

`printf` allows defining the format of each value (similar to `printf` in C). The spaces and the line break must be included in the format string.

```bash
awk -F ';' 'NR == 1 { printf "|%-10s|%8.2f|\n", $1, $2 }' products.txt
# |Rice      |   25.00|
```

`%-10s` aligns the name to the left in at least ten positions. `%8.2f` reserves at least eight positions, aligns the number to the right, and presents two decimal places. The width is minimum, so a larger value can exceed it.

| Format | Presentation |
| --- | --- |
| `%s` | Text. The precision in `%.3s`, for example, limits the output to three characters. |
| `%d` | Decimal integer. |
| `%f` / `%e` | Decimal number / exponential notation. The precision defines the places after the decimal separator. |
| `%g` | Decimal or exponential form according to magnitude and precision, expressed in significant digits. |
| `%o` / `%x` | Integer in octal / hexadecimal. |

## Operators and Functions:

| Operators | Operation |
| --- | --- |
| `+`, `-`, `*`, `/`, `%` | Addition, subtraction, multiplication, division, and remainder. |
| `^` | Exponentiation. GNU AWK also accepts `**`. |
| `=` | Assignment. To compare equality, `==` is used. |
| `+=`, `-=`, `*=`, `/=`, `%=` | Apply the operation and store the result in the variable or field. |
| `++`, `--` | Increment or decrement by one. |

Built-in functions can be called both in the condition and in the action. For example, `tolower` allows comparing names in lowercase and still printing the original spelling:

```bash
awk -F ';' 'tolower($1) == "rice" { print $1, $2 }' products.txt
# Rice 25
# Rice 12
```

| Function | Result |
| --- | --- |
| `length(text)` | Number of characters. Without an argument, `length()` uses `$0`. |
| `int(n)` | Integer part, truncated toward zero. |
| `index(text, snippet)` | Position of the first occurrence, counting from 1. Returns 0 if not found. |
| `substr(text, start, length)` | Up to `length` characters from the indicated position. Without the length, it goes to the end. |
| `split(text, array, separator)` | Divides the text, stores the parts in indices starting from 1, and returns the number obtained. |
| `tolower(text)` / `toupper(text)` | Copy of the text in lowercase / uppercase. |

## Control Structures:

### Decisions with `if` and `else`:

The pattern selects **the records for which the action will be executed**. An `if` inside the action allows **choosing what to do with the current record**. To classify all purchases, both alternatives produce an output:

```bash
awk -F ';' '{
    if ($2 >= 15)
        range = "15 or more"
    else
        range = "below 15"

    print $1, "-", range
}' products.txt
```

```text
Rice - 15 or more
Beans - below 15
Coffee - 15 or more
Rice - below 15
```

The condition is between parentheses. Each branch accepts one instruction or a block of instructions between braces. The `print` is outside the branches and uses the classification chosen in either of them.

### Repetitions with `for` and `while`:

AWK already traverses the input records. A loop inside the action serves to repeat an operation **during the processing of the same record**. To number the fields of the first purchase:

```bash
awk -F ';' 'NR == 1 {
    for (i = 1; i <= NF; i++)
        printf "Field %d = %s\n", i, $i
}' products.txt
# Field 1 = Rice
# Field 2 = 25
```

In `for (initialization; condition; update)`, `i` starts at 1 and advances to `NF`. The expression `$i` accesses the field whose number is in `i`.

`while (condition)` repeats the body while the test is true. It is useful when the stop depends on the content. This command searches for the value `15` among the fields and prints the first corresponding record:

```bash
awk -F ';' '{
    i = 1
    while (i <= NF && $i != "15")
        i++

    if (i <= NF) {
        print "Record", NR, "-", $0
        exit
    }
}' products.txt
# Record 3 - Coffee;15
```

The counter restarts at 1 for each record. If it exceeds `NF`, no field matched. If the search finds the value, `exit` ends the reading. Inside a loop, `break` ends only that loop and `continue` moves to the next iteration.

### Associative Arrays:

An associative array stores values identified by **keys**. In the previous sum, `total` stored a single result. To calculate a separate total for each product, the name can be the key of `total[product]`. Thus, the two rice purchases update the same element:

```bash
awk -F ';' '
    { total[$1] += $2 }
    END {
        for (product in total)
            print product, total[product]
    }
' products.txt
```

A possible output is:

```text
Rice 37
Beans 8
Coffee 15
```

On the first occurrence of `Rice`, the accumulator starts at zero and receives 25. On the second, it adds 12 to the stored value. At the end of the reading, `for (product in total)` traverses the **existing keys**, and `total[product]` retrieves the value associated with each one. The traversal order is not guaranteed.

> Indices can be provided as text or number. A numeric index is converted to text, so that `data[1]` and `data["1"]` access the same element.

---

# Sources

- ALVARES, Andrei Rimsa. *Course: Tópicos Especiais em Fundamentos da Computação: Shell Scripting*. Undergraduate course in Computer Engineering - Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2026.
- FREE SOFTWARE FOUNDATION. *GNU Bash Reference Manual*. Edition 5.3, for Bash 5.3. Boston: Free Software Foundation, May 2025. Available at: [https://www.gnu.org/software/bash/manual/bash.html](https://www.gnu.org/software/bash/manual/bash.html). Accessed on: Aug. 18, 2026.
- FREE SOFTWARE FOUNDATION. *GNU Coreutils*. Version 9.12. Boston: Free Software Foundation, 2026. Available at: [https://www.gnu.org/software/coreutils/manual/coreutils.html](https://www.gnu.org/software/coreutils/manual/coreutils.html). Accessed on: Sep. 4, 2026.
- THE LINUX MAN-PAGES PROJECT. *Linux man-pages*. Version 6.19. \[S. l.\], 2026. Available at: [https://www.kernel.org/pub/linux/docs/man-pages/man-pages-6.19.tar.xz](https://www.kernel.org/pub/linux/docs/man-pages/man-pages-6.19.tar.xz). Accessed on: Sep. 4, 2026.
- THE KERNEL DEVELOPMENT COMMUNITY. *The Linux Kernel documentation*. \[S. l.\], 2026. Available at: [https://docs.kernel.org/](https://docs.kernel.org/). Accessed on: Sep. 4, 2026.
