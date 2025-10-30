# Environment Variables

## Seeing Environment Variables

### To check the current environment variables one can use printenv or env. This outputs all the current environment variables for the system.
![printenv](../images/printenv.png)

### To change environment variables one can use export or unset. 
![export.png](../images/export.png)

- Here you can see I changed the shell from bash to sh.

## Parent and Child EV inheritance 

### Next I compiled and ran myprintenv.c and saved the output to file1. Then I commented the child process's env print and uncommented the parent's env print. Then I compiled and ran the file again, saving the output to file2. The using the diff command we can see the difference between the outputs.
![parent_child.png](../images/parent_child.png)

- There is no difference between the outputs becuase parent and child processes have the same environment variables.

## Execve

### In this section I compiled and ran the myenv c program. When first running it the program outputs nothing. After changing the execve line to include the environ argument it then ouputs the environment variables.
![execve_environ.png](../images/execve_environ.png)

- After chaning the NULL argument to environ in the execve line the program outputs the environment variables.

## System

### The System function is a vulnerability in C programs due to the way it executes system calls. It uses a sh shell to complete commands,  creating a potential attack vector. 
![system.c](../images/system.png)

- Here I created a program that uses the system function to print the environment variables. 

## Set-UID

### Set-UID programs allows users to execute privaleged programs as a normal user. In this section I created a c file called uid_ev. This file simply prints the environment variables.
![uid.png](../images/uid.png)

### I then changed the owner of the program to root and made it into a set-UID program.
![chown_root.png](../images/chown_root.png)

### Next, I used the export command to change the PATH, LD_LIBRARY_PATH, and ANY_NAME environment variables.
![export_names.png](../images/export_names.png)

### Then, I ran the set-UID program to see if the child process has the same environment variables.
![checkEV.png](../images/checkEV.png)

- Using grep I can see that the PATH and ANY_NAME EV's were maintained but the LD_LIBRARY_PATH EV was not present.

## PATH EV and Set-UID

### Using a system() call in a set-UID program is quite risky as a bad actor could change the PATH EV and have the program perform unintended actions.
![evil_path.png](../images/evil_path.png)

- Here I set the first path in the PATH EV as my own evil path.

### I then created a c program using system("ls") to list out the contents of a directory. In the evil directory I created another program called ls and in this one I had my "malicious code". 
![evil_ls.png](../images/evil_ls.png)

- Becuse the system call did not specify an absolute path for the command it could be manipulated to use the ls program in my evil directory. The malicious program did not open a root shell due to countermeasures in Ubuntu.

## LD_PRELOAD

### By adding user defined libraries to the LD_PRELOAD one can force a program to look at these libraries before the standard libraries in linux. One can have a program call a user-defined function that performs unwanted actions. 
![badSleep.png](../images/badSleep.png)
- I created a c file that has its own sleep function with an undesired output.

![mylib.png](../images/mylib.png)
- Then, I compiled the program and added it to my shared libraries. 

![LDadd.png](../images/LDadd.png)
- Next, I added my custom library to the LD_PRELOAD EV

![goodSleep.png](../images/goodSleep.png)
- I created a c program that runs the expected sleep function.

![normal_myprog.png](../images/normal_myprog.png)
- Running the program as a normal user outputs my user-defined sleep function.

![setUID_myprog](../images/setUID_myprog.png)
- After making the program a set-UID program the expected sleep function was called due the dynamic linker countermeasures. The EUID != the RUID so the LD_PRELOAD library is ignored. 

![root_myprog](../images/root_myprog.png)
- Changing the owner of the program to root and adding my custom library in the root shell printed my user-defined ouput. This is because the EUID == RUID so the LD_PRELOAD library is used. 

![bob_myprog.png](../images/bob_myprog.png)
- After changing the owner to bob and exporting the LD_PRELOAD again in my user account, the expected sleep function was called. Bob and mchughb id's differ so the countermeasure was applied.

## System() vs Execve()
### it is risky to use system() in a privaledged pogram becuase it invokes a shell to execute commands. On the other hand, execve() does not invoke a shell and is therefore a safer alternative.

![catall.png](../images/catall.png)
- This program provides privaledged cat access to files that a normal user cannot read. It either uses system() or execve() to execute the cat command. 

![system_rm.png](../images/system_rm.png)
- I then made catall.c a setUID program and tried to read a file mchughb did not have read access to. The program was not able to read the file due to the countermeasures in place that check the EUID vs the RUID. In Ubuntu, set_UID programs de-escalate privaledge when the EUID does not match the RUID. Therefore another user like bob would not be able to remove a file that is not writable to them.

![exceve.png](../images/execve.png)
- I then commented the system() call and uncommented the execve command. 

![catall_execve_setUID.png](../images/catall_execve_setUID.png)
- Then, I made the catall program root owned and a set-UID program again. This execve call is much more secure than the system() call so a bad actor would not be able to execute unwarranted commands. Execve() does not invoke a shell so there is no way a user could have their commands executed. 

## Capability Leaking
### After a set-UID program's privaledge is revoked, bad actors may still be able to execute unwanted commands via capability leaking. If a file descriptor is not closed in a program, then a user can inject commands into the file descriptor to exectute unwanted actions.
![zzz.png](../images/zzz.png)
- First I created a file called zzz in the /etc/ directory. This file is owned by root and has 644 permission.  

![cap_leak_setUID.png](../images/cap_leak_setUID.png)
- The cap_leak program uses a file descriptor to read /etc/zzz and then downgrades the privaledge to the RUID. It then executes a sh shell with execve().

![cap_leak_usingFd.png](../images/cap_leak_usingFd.png)
- I ran cap_leak as a normal user and the program was able to cat zzz but it was not able to write to it directly. I then tried to to write to it using the file desctiptor but this was also unsucessful. This is likely due to the countermeasures in place.