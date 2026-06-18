As you cannot make a new job when a foreground job is running, if a program requires a lengthy process, it is good to run it as a background job. You can run multiple background jobs at the same time as they are running in the background. To start a background job, run a command with `&` **at the end** of the command.

## Practice 2

### Objective:  
Understand how background job works

### 1. Run the shell script multiple times as a background job

Run the loop.sh file redirecting to a text file with `&` at the end. The command line gives you a number, which is the ID of the job.

Command Line - INPUT

```custom
./loop.sh > countdown_bg_1.txt &
```

Command Line - RESPONSE

```custom
[2] 83447
```

You can also execute the same command multiple times.

Command Line - INPUT

```custom
./loop.sh > countdown_bg_2.txt &
```

Command Line - RESPONSE

```
[3] 83450
```

Command Line - INPUT

```custom
./loop.sh > countdown_bg_3.txt &
```

Command Line - RESPONSE

```
[4] 83451
```

### 2. Check the job statuses

To see running jobs, run the `jobs` command, which will be explained in the next section. You can see that multiple jobs are running in parallel.

Command Line - INPUT

```custom
jobs
```

Command Line - RESPONSE

```custom
[1]   Running                 ./loop.sh > countdown_bg_1.txt &
[2]-  Running                 ./loop.sh > countdown_bg_2.txt &
[3]+  Running                 ./loop.sh > countdown_bg_3.txt &
```

The `+` and `-` are **not part of the job number**. They indicate which job is the shell's "current" and "previous" job.

Given:

```bash
[1]   Running  ./loop.sh > countdown_bg_1.txt &
[2]-  Running  ./loop.sh > countdown_bg_2.txt &
[3]+  Running  ./loop.sh > countdown_bg_3.txt &
```

### Meaning

|Symbol|Meaning|
|---|---|
|`+`|Current/default job|
|`-`|Previous job|
|(none)|Other jobs|

So here:

- Job **3** is the **current** job (`+`)
    
- Job **2** is the **previous** job (`-`)
    
- Job **1** is just another job
    

### Why does it matter?

Commands like `fg` and `bg` use the current job by default.

For example:

```bash
fg
```

is equivalent to:

```bash
fg %3
```

because job 3 has the `+`.

Similarly:

```bash
fg %-
```

means:

```bash
fg %2
```

because job 2 has the `-`.

### How are they chosen?

The shell usually assigns:

- `+` to the **most recently started/stopped** job
    
- `-` to the job before that
    

If you start another background job:

```bash
./loop.sh > countdown_bg_4.txt &
```

you might then see:

```bash
[1]   Running ...
[2]   Running ...
[3]-  Running ...
[4]+  Running ...
```

The newest job becomes `+`, and the old `+` becomes `-`.

Think of `+` as **"the job the shell will use if you don't specify one."**