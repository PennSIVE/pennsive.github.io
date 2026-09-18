---
author: "Gina Yu"
date: 2026-07-30
---

# Using `screen` on the Cluster

`screen` is a terminal utility that allows you to keep terminal sessions running in the background, even after you disconnect from the cluster, close your terminal, or lose your SSH connection.

`screen` can be used to:

- Keep commands running in the background
- Run long-running jobs or scripts
- Maintain remote terminal sessions
- Monitor interactive analyses over time
- Return to the same terminal session at a later time
- Organize multiple terminal sessions simultaneously

This page assumes that you are already familiar with logging into the cluster through SSH. For a broader introduction to cluster computing, please see the Cluster Computing page.

## Screen terminology: 
A screen session can have one of two states: 

- **Attached** — You are currently connected to the session.

- **Detached** — The session is still running, but no terminal is currently connected to it.

Detached sessions continue running until they are explicitly terminated. A detached session is **still active** and any programs running inside it will continue executing.

## Quick Reference

The table below summarizes the most commonly used `screen` commands.

| Task | Command |
|------|---------|
| Start a new session | `screen` |
| Start a named session | `screen -S my_session` |
| List active sessions | `screen -ls` |
| Reattach to a session | `screen -r my_session` |
| Force reattach to a session | `screen -d -r my_session` |
| Kill a session | `screen -S my_session -X quit` |
| Detach from a session | **Ctrl + A**, then **D** |

## Starting a `screen` Session

### First, SSH into the Takim2 cluster

```bash
ssh yourPennKey@takim2
```

Replace `yourPennKey` with your actual PennKey.

After logging in, your terminal should look similar to:

```text
yourPennKey@takim2 ~$
```

To start a new `screen` session, simply type:

```bash
screen
```

Alternatively, you can create a named session, which is recommended if you plan to have multiple sessions:

```bash
screen -S my_session
```

### Working Inside a `screen` Session

Once inside a `screen` session, you can use the terminal just as you normally would after logging into the cluster.

For example, you can:

- Start an interactive R session by typing `R`
- Run scripts such as `Rscript analysis.R` or `python script.py`
- Navigate directories using commands such as `cd my_project`
- Submit jobs using `bsub`
- Monitor log files or other running processes

Think of a `screen` session as a terminal that remains active even after you disconnect.

> **Note:** `screen` does not provide additional computing resources. It simply keeps your terminal session alive. For computationally intensive jobs requiring specific CPU or memory resources, use the cluster scheduler with `bsub`.

To leave a `screen` session without stopping the commands running inside it, press:

**Ctrl + A**, then **D**

This detaches the session while allowing any running commands to continue executing in the background.

## Managing Existing `screen` Sessions

To view all active `screen` sessions, type:

```bash
screen -ls
```

The output will look similar to:

```
123456.pts-2.takim2 (Detached)
```

where:

- `123456` is the session ID.
- `(Detached)` indicates that the session is still running in the background.

To reconnect to a session, type:

```bash
screen -r 123456
```

or, if you named your session:

```bash
screen -r my_session
```

To permanently end a session, type:

```bash
screen -S 123456 -X quit
```

or simply type `exit` from within the session.

If multiple `screen` sessions are active, the output may look similar to:

Example output:

```text
There are screens on:
    123456.analysis    (Detached)
    987654.testing     (Detached)

2 Sockets in /run/screen/S-yourPennKey.
```

## Best Practices

To keep your workspace organized, it is recommended to remove `screen` sessions that are no longer needed.

Unused sessions can:

- Continue running programs that consume CPU, memory, or other cluster resources
- Create clutter by accumulating detached sessions
- Make it more difficult to identify active work when using `screen -ls`

Using descriptive session names (e.g., `screen -S simulation`) can also make it easier to manage multiple sessions.


## Example Workflow

```bash
ssh yourPennKey@takim2
screen -S analysis
R
```

Run your analysis as usual.

When you want to disconnect without stopping your work:

- Press **Ctrl + A**, then **D**

Later, reconnect by logging back into the cluster and running:

```bash
screen -r analysis
```

## Troubleshooting 
### Recovering After a Disconnect

If your internet connection drops or you accidentally close your terminal, your `screen` session will continue running on the cluster.

Simply reconnect to the cluster using SSH:

```bash
ssh yourPennKey@takim2
```

Then list your available sessions:

```bash
screen -ls
```

Finally, reconnect to your session:

```bash
screen -r my_session
```

### Session Already Attached
If `screen` reports that a session is already attached, you can force it to detach from the previous terminal and reconnect to your current one:

```bash
screen -d -r my_session
```

### Managing Existing Sessions
To terminate the current `screen` session from within the session itself, simply type: 

```bash
exit 
```

## In Summary
`screen` is especially useful for interactive work where you expect to reconnect later. For non-interactive analyses or jobs requiring substantial computational resources, submitting a job through the cluster scheduler (`bsub`) is generally the preferred approach.

`screen` is especially useful for interactive work where you expect to reconnect later. For non-interactive analyses or jobs requiring substantial computational resources, submitting a job through the cluster scheduler (`bsub`) is generally the preferred approach.
For more information on `bsub`, please refer to [Submitting Jobs](wiki-jobs.md).