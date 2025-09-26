---
title: "Using resources effectively"
teaching: 15
exercises: 10
questions:
- "How do we monitor our jobs?"
- "How can I get my jobs scheduled more easily?" 
objectives:
- "Understand how to look up job statistics and profile code."
- "Understand job size implications."
keypoints:
- "The smaller your job, the faster it will schedule."
---

We now know virtually everything we need to know about getting stuff on a cluster. We can log on,
submit different types of jobs, use preinstalled software, and install and use software of our own.
What we need to do now is use the systems effectively.

## Measuring the statistics of currently running tasks

> ## Connecting to Nodes
> Typically, clusters allow users to connect directly to compute nodes from the login 
> node. This is useful to check on a running job and see how it's doing, but is not
> a recommended practice in general, because it bypasses the resource manager.
> If you need to do this, check where a job is running with `{{ site.sched_status }}`, then
> run `ssh nodename`. (Note, this may not work on all clusters.)
{: .callout}
  
We can also check on stuff running on the login node right now the same way (so it's 
not necessary to `ssh` to a node for this example).

### top

The best way to check current system stats is with `top` (`htop` is a prettier version of `top` but
may not be available on your system).

Some sample output might look like the following (`Ctrl + c` to exit):

```
{{ site.host_prompt }} top
```
{: .bash}
```
{% include /snippets/17/top_output.snip %}
```
{: .output}

Overview of the most important fields:

* `PID` - What is the numerical id of each process?
* `USER` - Who started the process?
* `RES` - What is the amount of memory currently being used by a process (in bytes)?
* `%CPU` - How much of a CPU is each process using? Values higher than 100 percent indicate that a
  process is running in parallel.
* `%MEM` - What percent of system memory is a process using?
* `TIME+` - How much CPU time has a process used so far? Processes using 2 CPUs accumulate time at
  twice the normal rate.
* `COMMAND` - What command was used to launch a process?

### free

Another useful tool is the `free -h` command. This will show the currently used/free amount of
memory.

```
{{ site.host_prompt }} free -h
```
{: .bash}
```
{% include /snippets/17/free_output.snip %}
```
{: .output}

The key fields here are total, used, and available - which represent the amount of memory that the
machine has in total, how much is currently being used, and how much is still available. When a
computer runs out of memory it will attempt to use "swap" space on your hard drive instead. Swap
space is very slow to access - a computer may appear to "freeze" if it runs out of memory and 
begins using swap. However, compute nodes on HPC systems usually have swap space disabled so when
they run out of memory you usually get an "Out Of Memory (OOM)" error instead.

### ps 

To show all processes from your current session, type `ps`.

```
{{ site.host_prompt }} ps
```
{: .bash}
```
    PID TTY          TIME CMD
 789235 pts/1    00:00:00 bash
 789399 pts/1    00:00:00 ps
```
{: .output}

Note that this will only show processes from our current session. To show all processes you own
(regardless of whether they are part of your current session or not), you can use `ps ux`.

```
{{ site.host_prompt }} ps ux
```
{: .bash}
```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
cot13     789225  0.0  0.0  89684  9684 ?        Ss   10:03   0:00 /usr/lib/systemd/systemd --user
cot13     789227  0.0  0.0 331448  5760 ?        S    10:03   0:00 (sd-pam)
cot13     789234  0.0  0.0 161724  5740 ?        S    10:03   0:00 sshd: cot13@pts/1
cot13     789235  0.0  0.0  26592  4276 pts/1    Ss   10:03   0:00 -bash
cot13     789404  0.0  0.0  58848  3940 pts/1    R+   10:11   0:00 ps ux
```
{: .output}

This is useful for identifying which processes are doing what.

## Killing processes

To kill all of a certain type of process, you can run `killall commandName`. `killall rsession`
would kill all `rsession` processes created by RStudio, for instance. Note that you can only kill
your own processes.

You can also kill processes by their PIDs using `kill 1234` where `1234` is a `PID`. Sometimes
however, killing a process does not work instantly. To kill the process in the most hardcore manner
possible, use the `-9` flag. It's recommended to kill using without `-9` first. This gives a 
process the chance to clean up child processes, and exit cleanly. However, if a process just isn't
responding, use `-9` to kill it instantly.

{% include links.md %}
