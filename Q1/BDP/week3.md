# Week 3

## About UNIX
UNIX is a true multiuser operating system; users have their own private space on the machine’s harddisk and are identified by an id number.

Some properties regarding UNIX users:

- All users have a user name and a password
- The user with id 0 is the system’s superuser, traditionally named root.
- UNIX processes act on behalf of the initiating user.
- Multiple users can be running multiple processes at the same time.
- Users are organised in groups. A group is a set of users that share the same class of permissions.

### UNIX filesystem
Everything in UNIX is represented as a file. It can be divided into two groups: 

- *configuration files*: Both user profile and system/server configuration files are plain text files. This allows to easily backup/restore/compare configuration files and remote administration using low-bandwidth *text console*
- *Devices*: accessing a device can be done via regular file I/O operation on filesystem objects that represent the real devices. For example *cat file.wav >/dev/sndcard* plays an audio file directly to the soundcard or *cat file.txt|lpr* prints it to a printer.

The following UNIX filesystem is the same for ALL UNIX versions:

|--bin          Binaries required before mounting /usr
|--etc          System wide configuration files
|--home         Users’ home directories
|--lib          Libraries required by the system
|--tmp          Temporary files. Everyone has RW access.
|--usr          Programs
| |--bin        Programs’ executables
| |--lib        Programs’ libraries
| |--local      Programs that are install locally
| |   |-bin,lib,share
| |--share      Programs’ required files (e.g. docs, icons)
| |--sbin       System administration programs
| |--src        Source files for the kernel and programs
|--var          Temporary space for running programs

Files need to be protected against intentional or unintentional access. Filesystem permissions provide the system with information about who can access a file or directory and what can he do with it.

### UNIX file permission
Files need to be protected against intentional or unintentional access. The filesystem permissions provide the system with information regarding who can access a file or directory and what he can do with it. The following is the permission types for each user types:

![Image](../../images/file_permissions.PNG)

The following is the complete display of all regarding permission of a file:

![Image](../../images/permission_detail.png)

### UNIX file paths
In Linux/UNIX, the backslash / symbolizes the root directory.

a dot (.) means the current directory
a double-dot (..) means the parent directory.

Thus ../ means going up one ladder

Lets say the filepath to the system log file is '/var/log/messages'

If the current directory is /var, the relative path to the system log is 

./log/messages == log/messages

### UNIX commands
Several useful commands good to know:

- ls: list files in a directory
    l: list details
    a: list hidden files (files that start with .)

- find <dir>: walk through a file hierarchy starting from <dir>
    type [dfl]: Only display directories, files or links
    name str: Only display entries that start with str
    {max|min}depth d



- touch <file>: Create and empty file named <file> or update the modification time for the existing
file <file>

- cp <from> <to>: copy file or directory <from> to the location specified by <to>
        -R: copy directories recursively
        -p: preserve filesystem permissions and attributes

- mv <from_1> · · · <from_n> <to>: move files or directories <from*> to directory <to>
        -n: do not overwrite existing files

- mkdir : create an empty directory in the path specified by the . The path to the directory must be pre-existing.
        -p: also create intermediate directories as required




    cat file_1 · · · file_n: concatenate and print files to standard output

    less file: displays a file on the screen allowing to browse it on both directions.
        q exit
        /pattern search for pattern in text, pressing / repeatedly moves through all occurrences.

    echo <string> write arguments to standard output

    head, tail <file>: display first/last lines of <file>
        -n Number of lines to display
        -f Display newly appended lines


### UNIX process
*Process* is the current task that your machine is currently working on. UNIX can do many processes at once, dividing the processor’s time between the tasks so quickly that it looks as if everything is running at the same time. This is called *multitasking*.

The UNIX shell has process management capabilities. Some useful key commands:

- Ctrl + Z will suspend current process
- Ctrl + C will kill current process
- '&' at the end of the command will run the process in the background i.e. find / |sort &
- 'ps' will show which processes are running
- kill -<singalno> <pid>: Send a signal to a process Important signals:
        TERM: informs the process that it should terminate.
        KILL: directly kill a process
- '>' will send a program's output to overwite the file
- '>>' will send a program's output to append to the file
- '<' will make a program read from the file

```
    $ echo "-What a nice day" > file
    $ echo "-Indeed" >> file
    $ cat file
    -What a nice day
    -Indeed
```

A process in Unix can output to 2 data streams: 

- STDOUT or 1
- STDERR or 2. 

Unix supports 2 types of text flow control:

- Redirects: send a program’s output to a file (>) or make a program read from a file (<). > will 
- Pipes: Forward a program’s STDOUT line-by-line to the input of another program.

### UNIX pipelinees
The pipe operator ( | ) allows combining commmands to read and write easy to process text. Some examples:

```        
    cat file |wc -l                                 // Count lines of file
    find / -type d | sort                           // View all directories sorted

    cat /var/log/access_log |grep foo|tail -n 10    // See the last 10 accesses from the host “foo” to our system’s web server.

    cat /etc/passwd |cut -f1 -d':'|sort             // Get a sorted list of the system’s users.
```

### Documentation
UNIX is a self-documenting system. All commands/tools have a manual page that describes their arguments, input and output formats and sometimes, even programming interfaces. 'man' <cmd> invokes the manual for a command:


Unix systems are also traditionally documented by providing full access to the source code that comprises them.

### Running a command per input line
'xargs' <cmd> will run cmd on each line in <STDIN>

```
$ find . -type f -maxdepth 1 |xargs wc      // Get file size statistics for the current directory
```