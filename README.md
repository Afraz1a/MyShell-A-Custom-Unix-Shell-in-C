#  MyShell — A Custom Unix Shell in C

> A feature-rich, POSIX-inspired shell built from scratch in C, complete with job control, pipelines, I/O redirection, command history, aliases, a virtual filesystem, and CPU scheduling simulations.

---

## Overview

MyShell is a custom interactive Unix shell implemented entirely in C. It replicates the core behavior of standard shells like `bash` and `zsh` while adding unique educational features — an in-memory virtual filesystem and CPU scheduling algorithm simulators — making it suitable both as a daily driver for basic tasks and as a learning tool for operating systems concepts.

---

## Features

### Core Shell
- **Interactive prompt** displaying the current working directory
- **Command execution** via `execv` with full `PATH` resolution
- **Arrow-key history navigation** (up/down) with raw terminal mode
- **Command history** (up to 200 entries) with the `history` built-in
- **Aliases** — define and expand command shortcuts with `alias`
- **Environment variable expansion** (`$VAR` in arguments)
- **Background execution** with `&`

### I/O Redirection
| Syntax | Effect |
|--------|--------|
| `cmd > file` | Redirect stdout to file (overwrite) |
| `cmd >> file` | Redirect stdout to file (append) |
| `cmd < file` | Redirect stdin from file |
| `cmd 2> file` | Redirect stderr to file |
| `cmd 2>> file` | Redirect stderr to file (append) |
| `cmd 2>&1` | Redirect stderr to stdout |

### Pipelines
- Full multi-stage pipelines: `cmd1 | cmd2 | cmd3 | ...`
- Each stage runs in its own process group with proper terminal control

### Job Control
| Command | Description |
|---------|-------------|
| `jobs` | List all background/stopped jobs |
| `fg %N` | Bring job N to the foreground |
| `bg %N` | Resume stopped job N in the background |
| `Ctrl+Z` | Suspend the foreground job |
| `Ctrl+C` | Interrupt the foreground job |

### Built-in Commands
| Command | Description |
|---------|-------------|
| `cd [dir]` | Change directory |
| `pwd` | Print working directory |
| `exit` | Exit the shell |
| `history` | Show command history |
| `jobs` | Show active/stopped jobs |
| `alias name="cmd"` | Define an alias |
| `set VAR=value` | Set an environment variable |

### Script & Source File Execution
MyShell detects file extensions and runs them with the appropriate tool:

| Extension | Action |
|-----------|--------|
| `.py` | Executed with `python3` |
| `.sh` | Executed with `sh` |
| `.c` | Compiled with `gcc -O2`, then executed |
| `.cpp` | Compiled with `g++`, then executed |
| `.java` | Compiled with `javac`, then run with `java` |

### Virtual Filesystem (`vfs`)
An in-memory filesystem with up to 32 files, persistent for the shell session.

```
vfs create <filename>         # Create a new file
vfs write <filename> <data>   # Write data to a file
vfs cat <filename>            # Print file contents
vfs ls                        # List all files with metadata
vfs rm <filename>             # Delete a file
```

### CPU Scheduling Simulations (`schedule`)
Interactive simulators for classic OS scheduling algorithms:

```
schedule fcfs                 # First-Come, First-Served
schedule rr <quantum>         # Round Robin with time quantum
```

Both simulators display:
- An ASCII Gantt chart
- Per-process turnaround and waiting times
- Average turnaround and waiting time

---

## Building & Running

**Requirements:** GCC, a POSIX-compatible system (Linux/macOS)

```bash
# Compile
gcc -O2 -o myshell main.c

# Run
./myshell
```

---

## Usage Examples

```bash
# Basic commands
[my_shell:/home/user]$ ls -la
[my_shell:/home/user]$ echo Hello, World!

# Piping
[my_shell:/home/user]$ cat /etc/passwd | grep root | wc -l

# Redirection
[my_shell:/home/user]$ ls > files.txt
[my_shell:/home/user]$ cat nonexistent 2> errors.txt
[my_shell:/home/user]$ make 2>&1 | grep error

# Background jobs
[my_shell:/home/user]$ sleep 30 &
[1] 4821
[my_shell:/home/user]$ jobs
[1] Running   sleep 30
[my_shell:/home/user]$ fg %1

# Aliases
[my_shell:/home/user]$ alias ll="ls -la"
[my_shell:/home/user]$ ll

# Virtual filesystem
[my_shell:/home/user]$ vfs create notes.txt
[my_shell:/home/user]$ vfs write notes.txt Hello from VFS
[my_shell:/home/user]$ vfs cat notes.txt
[my_shell:/home/user]$ vfs ls

# Scheduling simulation
[my_shell:/home/user]$ schedule fcfs
[my_shell:/home/user]$ schedule rr 4

# Run a Python script directly
[my_shell:/home/user]$ ./myscript.py arg1 arg2
```

---

## Architecture

```
main()
├── Signal setup (SIGINT, SIGTSTP, SIGCHLD)
├── display_prompt()
├── read_input()          ← raw mode, arrow-key history
├── parse_input()
│   ├── tokenize_line()   ← handles quotes, redirections, pipes
│   └── alias expansion
└── execute_command()
    ├── handle_builtin()  ← cd, exit, jobs, alias, vfs, schedule ...
    └── execute_pipeline()
        └── fork + execv per stage, with pipe chaining
```


## License

This project was developed as a final project for an Operating Systems course. Free to use for educational purposes.
