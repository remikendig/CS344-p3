# CS344-p3

What this thing needs to do:
- Use the colon symbol (:) as a prompt
- Flush the output buffers *every* time you output
  - `printf("a thing)"; fflush(stdout);`
- Support the following command line format:
  - `command [arg1 arg2 ...] [< input_file] [> output_file] [&]` where
    - `command` is the command,
    - `[arg1 arg2 ...]` are arguments to the command, which are optional,
    - `[< input_file]` redirects from an input file rather than stdin,
    - `[> output_file]` redirects stdout to an output file, and
    - `[&]` denotes a command to run in the background. NOTE: This should only work if the & is the last character in the command.
- Support lines starting with `#` as comments.
  - You do not need to support in-line comments.
- The command line should support a maximum length of 2048 and a maximum argument count of 512.
- Use fork(), exec(), and waitpid() to execute commands.
- Indicate that a command could not be executed when exec() returns an error.
  - Set the value retrieved by the build-in status command to 1.
  - Make sure child processes that have an exec() fail terminate.
- Use PATH variable to look for non built-in commands.
- Allow shell scripts to be executed.
  - If a command fails because it could not find the command to run, print an error message and set status to 1.
- After fork() but before exec(), use dup2() to do input/output redirection.
  - Input file redirected via stdin should be opened for reading only.
    - If the file cannot be opened, it should print an error message and set the exit status to 1.
  - Output files should be opened for writing only; truncated if existant, created if not.
    - If the file cannot be opened, blah blah error status 1.
  - Input and output should be able to be redirected at the same time.
- Expand any instance of "$$" into the PID of the shell itself.
- Wait for foreground processes to terminate before re-prompting.
- Do NOT wait for background processes to terminate before re-prompting.
  - Periodically check for background cleanup and/or use a signal handler to immediately wait for children that terminate.
    - The first is recommended.
  - Background commands should have their standard input redirected from /dev/null if no other specification.
  - They should also send standart output to /dev/null if no other output is specified.
  - Print PID of background processes when they start, and print PID and exit status when they complete.
    - Do this right before you prompt for the next input *after* completion of the background process.
- A CTRL-C input will send SIGINT to the parent process and all child processes at once.
  - SIGINT should *__not__* terminate the shell, only the foreground process if one is running.
    - The parent process will not terminate the child if this happens, the child will terminate itself.
    - If a child foreground process is killed by a signal, the parent will immediately print out the PID of the child and the signal that killed it.
    - Background processes should not be terminated by SIGINT.
- A CTRL-Z will send a SIGSTP to the parent process and all child processes at once. If received, print a message (immediately if at the command prompt, or immediately following termination of the foreground child process) saying that commands can no longer run in the background.
  - This should actually be true, of course.
  - If received again, print a message saying background commands are enabled again, and enable them.
  - Child processes will ignore a SIGSTP.
- Support three build in commands: `exit`, `cd`, and `status`.
  - `exit` takes no arguments, and will immediately exit after killing all child processes.
  - `cd` changes the working directory of the shell.
    - With no arguments, it will change to the directory specified in the HOME environment variable.
    - It can also take one argument: the directory to change to. This should support absolute and relative paths.
  - `status` prints either the last exit status *or* the terminating signal of the last *foreground* process.
    - If it is run before any foreground processes have run, it should print an exit status of 0.
    - Built-in commands do not count towards `status`.
