# Terminal
Execution environment for applications. Exposes text buffer. Only one application runs in foreground (this application "owns" keyboard/mouse).
# Shell
Application, uses terminal, exposes OS functionality for user (via commands). Allows configuration of execution environment (via variables)
# Environment variables
Shell utilizes two types of variables:
 - local: accessible only via current shell. Not passed to child processes. Created: `a=some_value`. Displayed with `set` command
 - exported: passed to every child process. Created: `export a=some_value`. Displayed with `env` command