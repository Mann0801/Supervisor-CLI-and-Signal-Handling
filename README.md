<<<<<<< HEAD
# Multi-Container Runtime

A lightweight Linux container runtime in C with a long-running supervisor and a kernel-space memory monitor.

Read [`project-guide.md`](project-guide.md) for the full project specification.

---

## Getting Started

### 1. Fork the Repository

1. Go to [github.com/shivangjhalani/OS-Jackfruit](https://github.com/shivangjhalani/OS-Jackfruit)
2. Click **Fork** (top-right)
3. Clone your fork:

```bash
git clone https://github.com/<your-username>/OS-Jackfruit.git
cd OS-Jackfruit
```

### 2. Set Up Your VM

You need an **Ubuntu 22.04 or 24.04** VM with **Secure Boot OFF**. WSL will not work.

Install dependencies:

```bash
sudo apt update
sudo apt install -y build-essential linux-headers-$(uname -r)
```

### 3. Run the Environment Check

```bash
cd boilerplate
chmod +x environment-check.sh
sudo ./environment-check.sh
```

Fix any issues reported before moving on.

### 4. Prepare the Root Filesystem

```bash
mkdir rootfs-base
wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-minirootfs-3.20.3-x86_64.tar.gz
tar -xzf alpine-minirootfs-3.20.3-x86_64.tar.gz -C rootfs-base

# Make one writable copy per container you plan to run
cp -a ./rootfs-base ./rootfs-alpha
cp -a ./rootfs-base ./rootfs-beta
```

Do not commit `rootfs-base/` or `rootfs-*` directories to your repository.

### 5. Understand the Boilerplate

The `boilerplate/` folder contains starter files:

| File                   | Purpose                                             |
| ---------------------- | --------------------------------------------------- |
| `engine.c`             | User-space runtime and supervisor skeleton          |
| `monitor.c`            | Kernel module skeleton                              |
| `monitor_ioctl.h`      | Shared ioctl command definitions                    |
| `Makefile`             | Build targets for both user-space and kernel module |
| `cpu_hog.c`            | CPU-bound test workload                             |
| `io_pulse.c`           | I/O-bound test workload                             |
| `memory_hog.c`         | Memory-consuming test workload                      |
| `environment-check.sh` | VM environment preflight check                      |

Use these as your starting point. You are free to restructure the repository however you want — the submission requirements are listed in the project guide.

### 6. Build and Verify

```bash
cd boilerplate
make
```

If this compiles without errors, your environment is ready.

### 7. GitHub Actions Smoke Check

Your fork will inherit a minimal GitHub Actions workflow from this repository.

That workflow only performs CI-safe checks:

- `make -C boilerplate ci`
- user-space binary compilation (`engine`, `memory_hog`, `cpu_hog`, `io_pulse`)
- `./boilerplate/engine` with no arguments must print usage and exit with a non-zero status

The CI-safe build command is:

```bash
make -C boilerplate ci
```

This smoke check does not test kernel-module loading, supervisor runtime behavior, or container execution.

---

## What to Do Next

Read [`project-guide.md`](project-guide.md) end to end. It contains:

- The six implementation tasks (multi-container runtime, CLI, logging, kernel monitor, scheduling experiments, cleanup)
- The engineering analysis you must write
- The exact submission requirements, including what your `README.md` must contain (screenshots, analysis, design decisions)

Your fork's `README.md` should be replaced with your own project documentation as described in the submission package section of the project guide. (As in get rid of all the above content and replace with your README.md)
=======
# OS Mini Project: Supervisor CLI with FIFO IPC

## Overview

This project implements a simple container management system using a supervisor process and a command-line interface (CLI).

It simulates basic container operations such as starting, stopping, and listing containers. Communication between the CLI and the supervisor is implemented using Inter-Process Communication (IPC) through Named Pipes (FIFO).

---

## Features

* Create containers using `fork()`
* Stop containers using `kill()`
* List active containers with their name and PID
* Communication using FIFO (`/tmp/os_fifo`)
* Supervisor process runs continuously and manages all containers

---

## Project Structure

```
.
├── supervisor.c
├── cli.c
├── README.md
├── .gitignore
```

---

## Compilation

```bash
gcc -Wall -Wextra -std=c11 -o supervisor supervisor.c
gcc -Wall -Wextra -std=c11 -o cli cli.c
```

---

## How to Run

### 1. Start Supervisor (Terminal 1)

```bash
./supervisor
```

### 2. Run CLI Commands (Terminal 2)

Start a container:

```bash
./cli start alpha
```

Start another container:

```bash
./cli start beta
```

List running containers:

```bash
./cli list
```

Stop a container:

```bash
./cli stop alpha
```

---

## Sample Output

Supervisor terminal:

```
Supervisor started. Listening on /tmp/os_fifo
Supervisor: started container 'alpha' with PID 12345
Supervisor: started container 'beta' with PID 12346
Active containers:
Name: alpha, PID: 12345
Name: beta, PID: 12346
Supervisor: stop signal sent to 'alpha'
Supervisor: container 'alpha' exited
```

---

## Concepts Used

* Process creation using `fork()`
* Process termination using `kill()`
* Inter-Process Communication using FIFO
* Signal handling
* Basic process management

---

## Notes

* The supervisor must be running before using CLI commands
* Output is displayed in the supervisor terminal
* FIFO is created at `/tmp/os_fifo`

---


>>>>>>> 4e6d8bc0f983fee57d3b07e819dc5dcdbf798d9b
