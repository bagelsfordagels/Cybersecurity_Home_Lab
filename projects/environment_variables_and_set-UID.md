# Environment Variables

## Seeing Environment Variables

### To check the current environment variables one can use printenv or env. This outputs all the current environment variables for the system.
![printenv](../images/printenv.png)

### To change environment variables one can use export or unset. 
![export.png](../images/export.png)

- here you can see I changed the shell from bash to sh.

## Parent and Child EV inheritance 

### Next I compiled and ran myprintenv.c and saved the output to file1. Then I commented the child process's env print and uncommented the parent's env print. Then I compiled and ran the file again, saving the output to file2. The using the diff command we can see the difference between the outputs.
![parent_child.png](../images/parent_child.png)

- There is no difference between the outputs becuase parent and child processes have the same environment variables.

## Execve and Environment Variables

### In this section I compiled and ran the myenv c program. When first running it the program outputs nothing. After changing the execve line to include the environ argument it then ouputs the environment variables.
![execve.png](../images/execve.png)

- After chaning the NULL argument to environ in the execve line the program outputs the environment variables.

## System and Environment Variables

### The System function is a vulnerability in C programs due to the way it executes system calls. It uses a sh shell to completed commands,  creating a potential attack vector. 
![system.c](../images/system.png)

- Here I created a program that uses the system function to print the environment variables. 