# Terminal/Console
Execution environment for applications. Provided by the kernel. Usually provides the hardware (keyboard).

Exposes text buffer. Only one application runs in foreground (this application "owns" keyboard/mouse).

# Shell
Application. The command interpreter.

Uses terminal, exposes OS functionality for user (via commands). Allows configuration of execution environment (via variables)
# Environment variables
Shell utilizes two types of variables:
 - _local_: accessible only via current shell. Not passed to child processes. Created: `a=some_value`.  
 Displayed with `set` command
 - _exported_: passed to every child process. Created: `export a=some_value`.  
Displayed with `env` command

# References
1. https://unix.stackexchange.com/questions/4126/what-is-the-exact-difference-between-a-terminal-a-shell-a-tty-and-a-con