bash, sh, posix, shell, builtins

https://www.gnu.org/software/bash/manual/html_node/index.html

# set

Use `-x` to echo commands as they are being executed:

    #!/bin/bash
    set -x
    
If `source` is used to invoke other scripts it will show commands of the child process as well.

# export

Problem, environment variables are not by default inherited by child processes:

    foo=bar
    bash
    echo $foo
    # (empty)

The value of myvar will be passed to the new shell:

    export myvar="This variable is defined."
    bash
    echo $myvar
    # This variable is defined.

To pass this function to any child processes, use export -f:

    myfunc () { echo "This function is defined."; }
    export -f myfunc
    bash
    myfunc
    # This function is defined.

A really long list of what gets exported from the current shell:

    export -p

# source

The source command can be used to load any functions file into the current shell script or a command prompt.

The syntax is as follows:

    source filename [arguments]
    source functions.sh
    source /path/to/functions.sh arg1 arg2
    source functions.sh WWWROOT=/apache.jail PHPROOT=/fastcgi.php_jail

Create a shell script called mylib.sh as follows:

    #!/bin/bash
    JAIL_ROOT=/www/httpd
    is_root(){
       [ $(id -u) -eq 0 ] && return $TRUE || return $FALSE
    }

You can now call and use function is_root() from mylib.sh using the following syntax in your script called test.sh:

    #!/bin/bash
    # Load the  mylib.sh using source comamnd
    source mylib.sh
    echo "JAIL_ROOT is set to $JAIL_ROOT"
    # Invoke the is_root() and show message to user
    is_root && echo "You are logged in as root." || echo "You are not logged in as root."

# alias

Aliases allow a string to be substituted for a word when it is used as the first word of a simple command.

    alias [-p] [name[=value] …]


If arguments are supplied, an alias is defined for each name whose value is given.

    alias lsla="ls -la"
    lsla
    # result

If no value is given, the name and value of the alias is printed.

    alias lsla
    # ls -la

Without arguments or with the -p option, alias prints the list of aliases on the standard output in a form that allows them to be reused as input.
