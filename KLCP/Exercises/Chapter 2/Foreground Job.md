## Practice 1
### Objective:  
Understand how the foreground job works

### 1. Create a shell script

First, let's create a shell script that requires a longer processing time. The shell script (loop.sh file) below counts down numbers from 1,000,000.

Use vim to create a shell script file named loop.sh.


```bash
mkdir dir_ch8
cd dir_ch8
vim loop.sh
```

Press the i key to switch to the insert mode. Next, copy the code below and paste it into the file. After pasting the code, press the esc key followed by the : key and save the file by pressing the w + q keys. To learn how to use Vim, check [Chapter 3. Vim Editor](https://d-libro.com/topic/chapter-3-vim-editor).

```bash
#!/bin/bash
  
count=5000000
while [ $count -ge 0 ]
do
echo "countdown $count"

count=$((count - 1))
done
```

### 2. Run the shell script

To run the shell script, adjust access mode and run the shell script with the file path.

Command Line - INPUT

```custom
sudo chmod u+x loop.sh./loop.sh
```

When you run this as a foreground job, you'll see the countdown on your terminal. Until the countdown is completed, your command line is occupied and you cannot create another job.

Command Line - RESPONSE

```custom
:
countdown 920071
countdown 920070
countdown 920069
:
```

To suspend or stop the job, press Ctrl + Z or Ctrl + C.

Even when you redirect the standard output to a text file, the job is still running as a foreground job and you cannot make another job.

Command Line - INPUT

```custom
./loop.sh > countdown.txt
```