In fact: duplicating  
When anything like `2>&1` is encountered, it means that anything that descriptor 2 contains is copied to descriptor 1, thus the order of "redirects" matter:

`ls / not_existing_file 2>&1 1>/dev/null` - the `stderr` is copied to whatever the descriptor 1 points to (`stdout` by default), the `stdout` is copied to `/dev/null`, the `stderr` is not updated

`ls / not_existing_file 1>/dev/null 2>&1` - the `stdout` is copied to `/dev/null` the `stderr` is copied to whatever the descriptor 1 points to (`dev/null` in this case)

# Closing file descriptors
To close descriptor, duplicate `-`, e.g.:  
Close stdin: `0<&-`  
Close stdout: `1<&-`

# References
1. https://wiki.bash-hackers.org/howto/redirection_tutorial