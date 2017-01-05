#Command substitution
`` `some command` `` plugs the `some command` output into some other context.
Currently backticks are deprecated in favour of `$(some command)` form which:

1. permits nesting `$(some command $(some nested))`
2. treats `\\` differently

#Process substitution
`<(command_list)` Feeds the output of processes into the _stdin_ of another process (piping allows only one
command output to be redirected into _stdin_ of another process).

The process list is run with its input or output connected to a FIFO or some file in /dev/fd.
The name of this file is passed as an argument to the current command as the result of the expansion.

#AWK
print the quote sign
```
awk 'BEGIN { print "Here is a single quote <'"'"'>" }' #this is concat of three strings '...' "'" '...'
awk 'BEGIN { print "Here is a single quote <'\''>" }'
```
simple examples:
```
#longest line in data file
awk '{ if (length($0) > max) max = length($0) }
     END { print max }' data
# Print every line that has at least one field
awk 'NF > 0' data
```
you can have multiple rules in awk (first goes first rule, then second, if line contains both patterns then will be processed two times)
```
awk '/12/ { print $0 }
     /21/ { print $0 }' data
```
awk env variables:
 - `AWKPATH` - contains awk programs
 - `AWKLIBPATH` - contains extensions (can be written in C/C++)

#References
 1. https://google.github.io/styleguide/shell.xml
 2. https://github.com/robbyrussell/oh-my-zsh/wiki/Coding-style-guide
 3. http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html
 4. https://www.kernel.org/doc/Documentation/process/coding-style.rst
 5. https://github.com/zsh-users/zsh-completions/blob/master/zsh-completions-howto.org