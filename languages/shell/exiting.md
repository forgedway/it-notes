# TLDR
Robust signal handling in shells is a myth. I couldn't make a simple script
that reliably exits on `Ctrl-C`. For example, a script like `sleep 1000`
doesn't either.

# MANIFEST
- The note describes problems and some attempts to resolve these problems for exiting shell scripts.
- It shows problems at first and leaves the attempts to the end.
- It describes process groups to understand how signaling works.
- It describes my opinion about how to deal with them (if we can).


# PROBLEMS
## PROBLEM 1
Is it killable by `Ctrl-C` in most cases? Why?
```bash
while true; do
  ping google.com
done
```

<details>
<summary>ANSWER</summary>

It's killable in:
- `zsh`;
- `dash`;
- `yash`;
- `mksh`;
- `busybox sh`.

It's not killable in:
- `bash`;
- `ksh`;
- `ksh93`.

The script is also affected by the **PROBLEM 2**.
</details>

<details>
<summary>EXPLANATION</summary>

#### Killable shells:
When `zsh`, `dash`, `yash`, `mksh` or `busybox sh` get `SIGINT`, they exit
after the current process (`ping`) exits whether it exits as "killed by a
signal" or as "exited".

#### Unkillable shells:
When `bash`, `ksh` or `ksh93` receive `SIGINT`, they assume that the current
process (`ping`) receives `SIGINT` too. Then they wait to check how the
process will end, either as "killed" or as "exited" (`waitpid (2)`). In case
of "killed" by the signal, they will terminate themselves too. But in case of
"exited", they won't terminate themselves and continue to run.

In other words, if a subprocess has a `SIGINT` handler and decides to exit in
the handler or later, these shells won't stop execution of the script.

As far as I can tell, developers of these shells wanted "graceful" `SIGINT`
proxying, but the implementation causes trouble for people. In our case, the
user wants the script to exit and `ping` exits on `SIGINT`, but the shells
DON'T!

IMHO `SIGINT` has one purpose: to interrupt an application. And it shouldn't
be used for other purposes. Other signals like `SIGUSR1` and `SIGUSR2` can be
used freely for anything programmers decide. It would be better if every shell
exited on `SIGINT` and wouldn't continue execution.
</details>

## PROBLEM 2
Are these scripts killable by `SIGTERM` to the `PGID`? Why?

```sh
sleep infinity
```

```sh
sleep infinity &
wait
```

<details>
<summary>ANSWER</summary>

It's killable most of the time by sending `SIGTERM` to the process group ID.
But in any shell I tested (in `bash`, `dash`, `yash`, `ksh`, `busybox sh`, ...)
it has a period of time when it's not killable by `SIGTERM`. The signal kills
the script but not the sleep subprocess. On my computer, the time window is
about 2ms.

</details>

<details>
<summary>EXPLANATION</summary>

When the shell starts a subprocess it blocks all signals (`sigprocmask (2)`),
then creates a new process (`clone (2)`) and after that the subprocess unblocks
signals (`sigprocmask (2)`). This way it has a condition when a signal is sent
to the process group, but it is delivered only to the main script and not
delivered to the subprocess because the subprocess isn't created yet.

</details>

## PROBLEM 3
Does the behavior here differ from the behavior of problem 2?
```sh
trap 'exit 1' INT
sleep infinity
```

<details>
<summary>ANSWER</summary>

Yes. It has different behavior. If the signal is delivered during the race
condition, the main script gets stuck with the sleep subprocess.
</details>

<details>
<summary>EXPLANATION</summary>

Custom handlers in the shell can't be executed during the execution of a
subprocess. The shell executes them after the subprocess ends. That's
why if the subprocess receives the signal after creation the main script won't
exit as in **PROBLEM 2**.
</details>


## PROBLEM 4
You decided to limit the process time in a script by `timeout` (a `POSIX`
utility). Is it killable by `Ctrl-C`? Why?
```sh
timeout 5 sleep 10
```

<details>
<summary>ANSWER</summary>

It's not.
</details>

<details>
<summary>EXPLANATION</summary>

`timeout` always ensures that it's the leader of its own process group. But
`Ctrl-C` sends `SIGINT` only to the active process group. That's why `timeout`
and its subprocess (`sleep` in our case) won't get `SIGINT` at all.
</details>

## PROBLEM 5
Does `Ctrl-C` kill every process in the script after the `wait` command has
started? Why?
```sh
sleep 100 &
sleep 1000 &
wait
```

<details>
<summary>ANSWER</summary>

`Ctrl-C` kills the script process, but it won't kill the background processes.
</details>

<details>
<summary>EXPLANATION</summary>

Despite the shell sending `SIGINT` to the background processes, the processes
don't receive the signal. The shell blocks `SIGINT` and `SIGQUIT` signals for
all background processes when it creates them.

This is caused by `POSIX`'s idea of killing only the current script and not
background jobs.

FROM `POSIX`:
> If job control is disabled (see the description of set -m) **when the shell
executes an asynchronous AND-OR list, the commands in the list shall inherit
from the shell a signal action of ignored (SIG_IGN) for the SIGINT and SIGQUIT
signals**. In all other cases, commands executed by the shell shall inherit the
same signal actions as those inherited by the shell from its parent unless a
signal action is modified by the trap special built-in (see trap)

IMHO this behavior isn't intuitive. For example, if the script wants to
make several requests (or calculations) at the same time, it doesn't need the
results of these requests after the termination. At the same time, it might
cause some errors in the operation of the newly launched script.
</details>

## PROBLEM 6
As a solution for **PROBLEM 5**, some people use this snippet.  
Will it work in all cases?
```sh
cleanup() {
  trap '' INT TERM
  kill -s TERM -- -$$
  wait || exit $?
  exit 1
}
trap 'cleanup' INT TERM

task1 &
task2 &
...

wait
```

<details>
<summary>ANSWER</summary>

It will work only in cases where the shell is the leader of the current
process group.
</details>


<details>
<summary>EXPLANATION</summary>

If the script is launched in an interactive shell, the process of the script
will be a process group leader. In that case `Ctrl-C` will kill everything
including the subprocesses.

But there are many cases where the process of the script isn't the leader of a
process group, such as:
- The script is called from another script (or a program).
- The script is called through a utility like `strace` or `cpulimit`.

In these cases, `-$$` won't expand to a group ID, the `kill` utility will
exit with an error and the subprocesses won't receive any signals.
</details>

## PROBLEM 7 (No solutions)
While I was testing solutions, I came across a race condition with `bash`.
This script gets stuck if `TERM` signal is delivered right after exiting of
the `wait4 (2)` syscall of the `wait` command on the last line. As far as I
understand, the problem is that the kernel notifies the process that the child
process has finished by the result of the `wait4 (2)` syscall, but the `bash`
interpreter doesn't change the internal state (because of the `TERM` signal)
and considers the child to be running. Later, in the user handler, the `wait`
statement gets stuck in an endless loop.

```bash
#!/usr/bin/env bash

handler() {
  trap '' INT TERM
  exec >/dev/null 2>&1

  sleep 0.01
  kill -s TERM -- -$$ || true
  wait || exit $?

  exit 1
}
trap handler INT TERM

sleep 3600 &
kill -s TERM -- $$ &

wait
```

<details>
<summary>strace loop</summary>


```strace
19:07:52.450005 wait4(-1, 0x7ffc512046a4, 0, NULL) = -1 ECHILD (No child processes)
19:07:52.450041 rt_sigaction(SIGINT, {sa_handler=SIG_IGN, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7fc02d83e8f0}, {sa_handler=SIG_IGN, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7fc02d83e8f0}, 8) = 0
19:07:52.450083 rt_sigprocmask(SIG_SETMASK, [], NULL, 8) = 0
19:07:52.450144 write(2, "./script.sh: line 45: wait: pid 521723 is not a child of this shell\n", 68) = 68
19:07:52.450212 rt_sigprocmask(SIG_BLOCK, [CHLD], [], 8) = 0
19:07:52.450257 rt_sigprocmask(SIG_SETMASK, [], NULL, 8) = 0
19:07:52.450306 rt_sigprocmask(SIG_BLOCK, [CHLD], [], 8) = 0
19:07:52.450350 rt_sigprocmask(SIG_SETMASK, [], NULL, 8) = 0
19:07:52.450394 rt_sigprocmask(SIG_BLOCK, [CHLD], [], 8) = 0
19:07:52.450449 rt_sigaction(SIGINT, {sa_handler=0x55acaa8bd080, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7fc02d83e8f0}, {sa_handler=SIG_IGN, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7fc02d83e8f0}, 8) = 0
19:07:52.450488 rt_sigaction(SIGINT, {sa_handler=SIG_IGN, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7fc02d83e8f0}, {sa_handler=0x55acaa8bd080, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7fc02d83e8f0}, 8) = 0
19:07:52.450520 wait4(-1, 0x7ffc512046a4, 0, NULL) = -1 ECHILD (No child processes)
# Here we go again
```
</details>


# PROCESS GROUPS (PGIDs)

You can skip this section if you know what these are. Or you can glance over
it if you don't want to understand it in depth.

> TLDR: PGID is used to manage applications (e.g. to kill all processes at once).
All new child processes are in the same process group if they don't create and
enter a new one.

#### Why do they exist?
Process groups are used to manage several related processes together as a
single unit. When a signal is sent to a group, each process in the group
receives the signal. It's a convenient way to kill the entire group or to
signal that something has happened.

#### What are these?
`PGID` (process group ID) is a number that is assigned to every process just
like `PID`. Several processes can have the same `PGID`, if so, they are
considered a process group. If a process from a group creates another
process, the new process inherits the `PGID` and the group expands in size.
Processes of an application, script or pipeline (in an interactive shell)
often belong to a separate process group.

#### How to interact
Signals | Reason
| - | -
`SIGINT`, `SIGTERM`, `SIGKILL`, `SIGQUIT` | To terminate a group.
`SIGSTOP`, `SIGTSTP`, `SIGCONT` | To stop and then continue a group.
`SIGWINCH`, `SIGHUP` | To signal that something has happened.


### PROCESS GROUPS PRACTICE
If we start a pipeline like this in an interactive shell, every process will
be in the same process group:
```sh
sleep 50 | cat | sort
```

We can check it from another terminal by `ps -o pid,pgid,comm -t pts/{X}`,
where `{X}` is the terminal number (check it with the `tty` command).

```ps
  PID    PGID COMMAND
29653   29653 zsh
29845   29845 sleep
29846   29845 cat
29847   29845 sort
```
Note that `sleep` process has the same `PID` and `PGID`. It means that `sleep`
is considered as a leader of the process group.

We can send a signal to a group (to each of the processes in a group) by
killing the negative `PGID` of the group. In our case it's done by the
command:
```sh
kill -s INT -- -29845
```

or simply by pressing `Ctrl-C` in this terminal to send `SIGINT`.


### PROCESS GROUP CREATION
`setpgid(2)` and `setsid(2)` are syscalls that are used to manipulate process
groups (and sessions). Something similar is used to create a new group:
```C
clone(...);
...
setpgid(0, 0);
...
execve("/usr/bin/program", ...);
```

Who creates (and manages) new process groups:
Category | Examples | Why
| - | - | -
**Init processes** | `systemd`, `upstart` | To manage services.
**`WM`s and `DE`s** | `i3`, `gnome` | To launch new applications.
**Terminal emulators** | `kitty`, `st` | To create new sessions for a shell.
**Interactive shells** | `bash`, `zsh` | To run scripts and programs.
**Terminal multiplexers** | `screen`, `tmux` | To run multiple shells.
**Container platforms** | `docker`, `podman` | To run containers.
**Servers** | - | To manage worker groups.
**Other**<br>(`ssh`, daemonizing tools, scripts (with `nohup`, `setsid`, `setpgid`), custom software) | - | -


### PROCESS GROUPS IN INTERACTIVE SHELLS
An interactive shell has a session ID (another number `SID`) that represents
its session. There can be different process groups in the session. An
interactive shell creates a new process group for every program, script or
pipeline it launches. When a process from this group creates another process,
the new process inherits the `PGID`. This way the group (the program, script
or the pipeline) can be managed (killed or so) at once.

Let's create several process groups at the same time:
```sh
# This will launch a process within a background process group.
# It won't hold the terminal.
$: dash -c 'sleep 1000 && echo finished' &

# This will launch a process within a foreground process group.
# It will hold the terminal.
$: bash -c 'sleep 1000 && echo finished'
```

We can see it by `ps -o pid,sid,pgrp,stat,comm -t pts/{X}`, where `{X}` is the
terminal number (check it with `tty` command).
```ps
  PID     SID    PGRP STAT COMMAND
35705   35705   35705 Ss   zsh
39593   35705   39593 SN   dash
39595   35705   39593 SN   sleep
39649   35705   39649 S+   bash
39650   35705   39649 S+   sleep
```
As we can see:
- `zsh` (the interactive shell) has `s` in the status.
  - It means it's the session leader.
- `SID` (session ID) is the same for every process in that terminal.
  - It means every process is in the same session.
- "dash" process group doesn't have the `+` sign in the status.
  - It means these processes are in a background group and don't hold the
  terminal.
- "bash" process group has the `+` sign in the status.
  - It means these processes are in the foreground group and they hold the
  terminal.

Additional:
- `S` in status:
  - The process is asleep.
- `N` in status:
  - The process is "nice" (has a `nice` value > 0).


# CTRL-C IN SHELLS
The current script, program or pipeline, that works in the foreground in
a shell, can be terminated (usually) by the `Ctrl-C` hotkey. When the user
presses `Ctrl-C`, the shell sends `SIGINT` (signal interrupt) to the
foreground process group. When the processes get the signal, they usually
try to stop themselves. After the exit of THE GROUP LEADER the shell
releases the terminal and can be used again.

Schematically, it looks like this:
Who | What
| -: | -
**User** | Starts a script `$: ./script.sh`.
**Shell** | Launches the script in a new process group.
**Shell** | Blocks on the group leader (./script.sh process).
**Script** | Might expand to several processes (`clone`, `vfork`).
**User** | Waits for some time.
**User** | Hits `Ctrl-C`.
**Shell** | Sends `SIGINT` to the foreground process group.
**Every process in Script** | Receives `SIGINT`.
**Every process in Script** | Tries to terminate.
**Shell** | Releases the terminal when the process group leader exits.

# SOLUTIONS

## SOLUTION 1
Just always replace the default signal handler:
```sh
trap 'exit 1' INT

while true; do
  ping google.com
done
```

Though this snippet doesn't fix most of the other problems, it will change the
default behavior of `SIGINT` handling for `bash`, `ksh` and `ksh93`.

## SOLUTION FOR PROBLEMS 2 and 3
These scripts can't be fixed.
```sh
sleep infinity
```

```sh
trap 'exit 1' INT
sleep infinity
```

But we can change it to asynchronous execution and there won't be this
problem:
```sh
cleanup() {
  trap '' INT TERM
  test -z "${!:-}" || {
    kill -s TERM -- $! >/dev/null 2>&1 || true
    wait $! || true
  }
  exit 1
}

trap cleanup INT TERM

sleep infinity &
wait $!

trap - cleanup INT TERM
```

## SOLUTION 4
To deal with this, we should kill the `PGID` of the subprocess manually, but
to avoid getting stuck on problems 2 and 3 we need to launch it in an
asynchronous way. Note that there must be a `sleep 0.01` command in the signal
handler to wait for `setpgid (2)` in the `timeout` binary.

```sh
cleanup() {
  trap '' INT TERM
  test -z "${!:-}" || {
    sleep 0.01
    kill -s TERM -- -$! >/dev/null 2>&1 || true
    wait $! || true
  }
  exit 1
}

trap cleanup INT TERM

timeout 2 sleep 10 &
wait $! || { ... }

trap - INT TERM
```

## SOLUTIONS FOR PROBLEMS 5 and 6
- Sending the signal to the current process group (`-$$`) doesn't work for
cases when a script isn't a `PGID` leader.
- Killing all subprocesses of the current script is problematic because every
subprocess can create new subprocesses.

The only solution that I see is to relaunch the script in a new process group
in case it isn't a leader of the current process group.

I tried to create such a script header that works for any shell (from `dash` to
`ksh93`). And the last problem that I can't resolve is a race condition in
bash that is described in the **PROBLEM 7**. Only the `bash` interpreter
often gets stuck in an endless loop, though it works, as far as I can see, for
all other interpreters.

Another problem that I didn't manage to overcome is that it's not fully
cross-platform. It uses the `setpgid` utility that is a part of GNU utils, so
it won't work on other platforms like `BSD` or `Alpine Linux`.

<details>
<summary>NOT FULLY WORKING SOLUTION (HEADER)</summary>

```sh
is_the_group_leader() {
  ps -o pid,pgid | grep -q -E $$'[[:space:]]+'$$
}

# relaunch the script if
if ! is_the_group_leader; then
  handler() {
    trap '' INT TERM
    exec >/dev/null 2>&1

    test -z "${!:-}" || {
      sleep 0.1
      kill -s TERM -- "-$!" || {
        # busybox variant
        kill -s TERM "-$!"
      } || true
      wait $! || exit $?
    }
    exit 0
  }
  trap handler INT TERM

  exec 5<&0
  cat <&5 | setpgid ./script.sh "$@" &

  exec >/dev/null 2>&1 5>&-
  wait $! || exit $?

  exit 0
fi

exec 5>&-

handler() {
  trap '' INT TERM
  exec >/dev/null 2>&1

  sleep 0.1
  kill -s TERM -- -$$ || {
    # busybox variant
    kill -s TERM -$$
  }
  wait || exit $?
  exit 0
}
trap handler INT TERM

# REAL SCRIPT HERE
do_stuff &
wait

do_other_stuff &
wait
```
</details>

# Epilogue

My opinion is that no one should use shells for any reason, even for simple
scripts. Robust signal handling in shells is a myth. I'd love for someone to
find a solution, but for now I haven't managed to find a real one, and even if
I did, I wouldn't trust that solution at all. Shells have plenty of race
conditions and other inconspicuous pitfalls. That's why I'll try to avoid them
in the future.
