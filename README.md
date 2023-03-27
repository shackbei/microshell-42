# microshell-42

**If someone has a solution with less code let me know ;)**

</br></br>

## FAQ 
<details>
  <summary>Question 1</summary>
  
  Why is it necessary to first duplicate the STDIN of the parent process (line 55) and then dup2 it with the one of the child process (line 41), in the case where you we use « ; » to separate process (no pipe). I thought that parent and process were sharing the same file descriptors thus the child stdin and stdout would natively be the keyboard and the terminal.

</br>

 - __Answer__: Parent and child processes share the same file descriptors. However, in this case, the reason for duplicating the STDIN of the parent process and then using dup2 to assign it to the child process is to ensure that the child process receives its input from the correct source. When the program encounters a semicolon, it creates a child process to execute the command before the senicolon, and then waits for the child process to finish before continuing with the next command. In this case, the input to the child process should come from the STDIN of the parent process. However, since the parent process is also waiting for the child process to finish, it cannot be reading from STDIN at the same time. Therefore, by duplicating the STDIN of the parent process and assigning it to the child process using dup2, the child process is guaranteed to have access to the correct input. So, duplicating the STDIN of the parent process and using dup2 to assign it to the child process is necessary to ensure that the child process receives input from the correct source when executing a command before a semicolon
</details>

<details>
  <summary>Question 2</summary>

  Why the input of the child process should come from the STDIN of the parent process ? Since we are forking, they have the same, the child cannot use his own ?

  </br>
  
  - __Answer:__ When a child process is forked from its parent process, it inherits the same file descriptors as its parent. So technically, the child process can use its own STDIN to receive input. However, in the context of this program, when the parent process encounters a semicolon, it creates a child process to execute the command before the semicolon, and then waits for the child process to finish before continuing with the next commsnd. In this case, the input to the child process should come from the STDIN of the parent process, because the parent process needs to wait for the child process to finish before it can continue reading from its own STDIN. If the child process used its own STDIN to receive input, the parent process would not be able to wait for the child process to finish because it would still be waiting for input from its own STDIN. This would cause the program to hang and become unresponsive. Therefore, to ensure that the program exevutes correctly, the STDIN of the parent process is duplicated and assigned to the child process using dup2 so that the child process can receive input from the correct source.
  
</details>


<details>
  <summary>Question 3</summary>
  
Why the parent need to close the duplicate (line 79) and redup it straight after (line 82) ?
  
</br>

- __Answer:__ The reason the parent process closes the duplicated STDIN file descriptor (line 79) and reduplicates it (line 82) is to ensure that the child process reads input from the correct source, which is the STDIN of the parent process. When the parent process encounters a semicolon and creates a child process to execute the command before the semicolon, it duplicates its own STDIN file descriptor using the dup system call, and assigns the duplicate file descriptor to the variable tmp_fd. This is because the parent process needs to keep reading input from its own STDIN, so it can't directly assign its own STDIN file descriptor to the child process. Once the child process is created, it needs to read input from the STDIN of the parent process, which is the file descriptor that was duplicated and assigned to tmp_fd. Therefore, the child process uses the dup2 system call to assign the value of tmp_fd to its own STDIN file descriptor (line 41). After the child process finishes executing, the parent process needs to wait for it to finish (line 77). Once the child process has finished, the parent process closes the duplicated STDIN file descriptor that was assigned to tmp_fd (line 79) to clean up the resources used by the child process. Then, the parent process reduplicates its own STDIN file descriptor using the dup system call (line 82), so that it can continue reading input from its own STDIN for the next command. Bottomline, the purpose of closing and reduplicating the STDIN file descriptor in the parent process is to ensure that the child process reads input from the correct source and to properly manage the resources used by the child process.

  
  </details>
  
  
  <details>
  <summary>Question 4</summary>
  
Why do we use WUNTRACED flag?
  
</br>

- __Answer:__ The purpose of using WUNTRACED in this context is to ensure that the parent process waits for the child process to complete or enter a stopped state before continuing execution. This is necessary to prevent race conditions and ensure that the child process has completed its execution before the parent process tries to read or write from the pipe or modify any shared resources.

  
  </details>

