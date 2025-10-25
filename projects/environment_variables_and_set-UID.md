# Environment Variables

## Seeing Environment Variables

### 1.) To check the current environment variables one can use printenv or env. This outputs all the current environment variables for the system.
![printenv](../images/printenv.png)

### 2.) To change environment variables one can use export or unset. 
![export.png](../images/export.png)

- here you can see I changed the shell from bash to sh.

## Parent and Child EV inheritance 

### 1.) Next I compiled and ran myprintenv.c and saved the output to file1. Then I commented the child process's env print and uncommented the parent's env print. Then I compiled and ran the file again, saving the output to file2. The using the diff command we can see the difference between the outputs.
![parent_child.png](../images/parent_child.png)

- There is no difference between the outputs becuase parent and child processes have the same environment variables.

## Execve and Environment Variables

### 1.) In this section I compiled and ran the myenv c program. When first running it the program outputs nothing. After changing the execve line to include the environ argument it then ouputs the environment variables.
![execve.png](../images/execve.png)
