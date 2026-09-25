# Supervisor CLI and Signal Handling

A small Linux process supervisor written in C. It runs in the background and starts, stops, and tracks "containers". A separate command-line tool controls it by sending commands through a named pipe.

I built it for my Operating Systems course at PES University. It's a simplified version of how a container runtime manages its processes: each container here is just a child process, not a fully isolated container.

## What it does

```
./cli start web       →  supervisor forks a new child process called "web"
./cli list            →  supervisor prints every running container and its PID
./cli stop web        →  supervisor sends SIGTERM to "web"
```

## How it works

The project has two programs:

- **`supervisor`** runs continuously. On startup it creates a named pipe (FIFO) at `/tmp/os_fifo` and waits for commands on it.
  - `START <name>` forks a new child process for the container and records its name and PID.
  - `STOP <name>` sends that process `SIGTERM`.
  - `LIST` prints every active container and its PID.

  Commands that can't be carried out get an error message instead of a crash: a duplicate name, an unknown name, or an unknown command.

- **`cli`** is the tool you type commands into. It turns `start`, `stop`, or `list` into a text command, writes it into the pipe, and exits.

Key OS concepts it uses:

- **Named pipes (FIFOs)** let two unrelated processes talk to each other. The supervisor also keeps its own write end of the pipe open. Without that, the pipe would reach end-of-file every time a `cli` call finished.
- **`fork()`** creates each container as a child process of the supervisor.
- **Signals.** `STOP` sends `SIGTERM`. The supervisor installs a `SIGCHLD` handler and calls `waitpid(..., WNOHANG)` to reap exited children. That way stopped containers don't linger as zombie processes, and they're removed from the list.

## Build and run

You need Linux and `gcc`.

```bash
gcc -Wall -o supervisor supervisor.c
gcc -Wall -o cli cli.c
```

Start the supervisor in one terminal:

```bash
./supervisor
```

Send it commands from a second terminal:

```bash
./cli start web
./cli start db
./cli list
./cli stop web
./cli list
```

## Example output

This is what the supervisor prints for the commands above. After them, the run did `start web` twice more and then `stop ghost`:

```
Supervisor started. Listening on /tmp/os_fifo
Supervisor: started container 'web' with PID 156574
Container 'web' started with PID 156574
Supervisor: started container 'db' with PID 156576
Container 'db' started with PID 156576
Active containers:
Name: web, PID: 156574
Name: db, PID: 156576
Supervisor: stop signal sent to 'web' (PID 156574)
Supervisor: container 'web' with PID 156574 exited
Active containers:
Name: db, PID: 156576
Supervisor: started container 'web' with PID 156585
Container 'web' started with PID 156585
Supervisor: container 'web' already exists
Supervisor: container 'ghost' not found
```

## Files

| File | What it is |
|---|---|
| `supervisor.c` | The supervisor: the pipe listener, fork/stop logic, and child reaping |
| `cli.c` | The command-line client |
| `boilerplate/`, `project-guide.md` | Starter code and the assignment spec from the course |
