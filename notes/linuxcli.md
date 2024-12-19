## commands
- `touch`: create file
- `ls`: list of files
- `cd`: change directory
	home dir: `~`
	parent dir: `..`
	current dir: `.`
- `pwd`: display current directory
- `mkdir`: make directory
- `ls -a`: view list of files with hidden files
- `cat`: read files
- `grep (searchstring) (directory)`: to find text within a file 
- `find (path) -name (filename)`: to find files within directories
- `ln -s (original file loc) (new loc)`: for symlinking files
	a hardlink is a link which refers to two different names for a specific physical place
	a softlink is like creating a new fwding address for the previously located physical place
- `man`: to read manual page for a command
	to search in a manual page use `/`
	 `man man` to get man command's man page
	 `man -k (text)` to search for a command within a man page
- `(command) --help`: to know how to details about a command
	you can run help for builtins using `help (command)`
- `echo`: outputs str as passed to it

### file globbing
- `?` for one char
- `*` for multiple char, wildcard
- `[abc]` for limited chars within brackets
- `[^abc]` for all chars except those in brackets

### piping
- redirecting output from file desrciptor to file: `>`
- redirecting output in append mode `>>`
- redirect stdout `1>` same as `>`
- redirecting stderr `2>`
- redirecting input `<`

- to avoid storing a file, you can pipe the output using `|`
- you can redirect from file descriptor to file descriptor using `>&`
  
- you can use `| tee (1) (2)` to save the middleman output of what is happening in halfway stage of running your command by saving it to a file or saving an error/result midway

### shell variables
- `echo $VAR` to read variables
- `VAR=value` to set value
- multi word vars, use `""`
- `export VAR=value` to save value of variable so future shell programs inherit the value
- `env`: to print every exported var
- to save output of command into var: `VAR=$(program)`
- `read` to read files directly instead of using cat

### processes
- `ps` or `ps aux` to list all running processes
- `kill` to kill processes
- ctrl+c to interrupt process
- suspend into bg ctrl+z
- fg to foreground a process
- bg to resume in background
- to start processes backgrounded from the start use `sleep processid &`
- echo `$?` to read exit code of process

### misc
- `su (user)` then enter pass to login, use tool johntheripper
- `sudo` to exec as root
- semicolon to chain commands
- export `PATH=xyz:$PATH` to append to path

### perms
- `chown (user) (filedir)` to change ownership
- `chgrp` for changing groups
- `id` to identify which users groups have permissions to use the file
- `chmod (u or g or o) (+ or -) (r or w or x) (filedir)`
- `chmod u=rx,g=rx (filedir)` using commas and = can also be done
- `chmod u+s (programdir)` to add it to SUID bit