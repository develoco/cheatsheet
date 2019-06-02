kill, ps, top, sigterm, term, hup

# kill

The default signal for kill is TERM. To list available signals, enter:

    kill -l

FInd the process number


And send the signal

    kill -KILL 1234
    #kill -SIGKILL 1234
    # kill -9 1234

The pkill command is another command with additional options to kill process by its name, user name, group name, terminal, UID, EUID, and GID. 

    pkill -KILL php-cgi
