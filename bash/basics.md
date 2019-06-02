bash, sh, posix, sed

# basics

Start scripts with this if they are POSIX-compliant

    #!/bin/sh
    
To echo every commad:

    set -x

Make file executable (`.sh` extension optional)

    chmod +x script.sh

Use double-quotes `"` for variable expansion (as opposed to single-quotes):

    $ echo "hello `whoami`."
    hello dl.
    $ echo 'hello `whoami`.'
    hello `whoami`.

# sed (macos)

    sed -i .bak 's/few/asd/g' hello.txt

    g: Global
    s: substitute
    -i: realtime works with file inplace (mac needs a backup filename suffix)
    -e: expression (can be optional)

With special characeters, change `from '/support` for `from '/custom-elem/support`:

    sed -i "" "s/fram \'\/support/from \'\/custom\-elem\/support/g" test.js

Search replace `'./hyperscript.js'` with `'react'` (special characters within single-quotes)

    sed -i .bak 's/'\''\.\/hyperscript\.js'\''/'\''react'\''/g' *.js
    
In the example the expressions are `'\''\.\/hyperscript\.js'\''/'` and  `\''react'\''`. Once you make sure the change was correct you can delete the backup files:

    rm *.bak

# Tricks

## Generate passwords

To generate random passwords, unique id's (UIDs)

On Mac

    date | md5

On Linux

    date | md5sum

Produces something like `baa0f35aee45318136c208e91f5ffade`

## Ports and processes

Check what proceses are listening on a port

    $ lsof -i :6379
    # COMMAND    PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
    # redis-ser 7151   dl    6u  IPv4 0x307690cad617cc3f      0t0  TCP localhost:6379 (LISTEN)
    # redis-ser 7151   dl    7u  IPv6 0x307690cac4aa08bf      0t0  TCP localhost:6379 (LISTEN)
    $ kill -9 7151
    $ lsof -i :6379
    $

Iterate over subfolders and declare variables with count:

    themes=0
    for path in themes/*; do
      [ -d "${path}" ] || continue # if not a directory, skip
      dirname="$(basename "${path}")"
      let themes=themes+1 
      eval "theme_$themes=$dirname"
      # `theme_${themes}=${dirname}` will say command not found
      export theme_$themes=$dirname # also works
    done

Check if variable equals a string (argument variable `$1` can be empty so double quotes are required for the comparisson)

    if [ "$1" = "country" ]; then
       source="country"
       samples="US Canada Mexico..."
    else
      echo "try again"
    fi

Check for variable defined (not just emptyness):

    foo=""
    if [ ${foo+x} ]; then echo "Foo is Defined"; fi
    if [ ! ${bar+x} ]; then echo "Bar is not defined"; fi

For empty Use

    if [ -z ${baz} ]; then echo "Baz is undefined or empty"; fi
    # Baz is undefined or empty
    baz=""
    if [ -z ${baz} ]; then echo "Baz is undefined or empty"; fi
    # Baz is undefined or empty
    baz="not empty"

Indirection, use `!`

    $ export xyzzy=plugh ; export plugh=cave
    $ echo ${xyzzy}  # normal, xyzzy to plugh
    plugh
    $ echo ${!xyzzy} # indirection, xyzzy to plugh to cave
    cave

While sample

    COUNTER=0
    while [  $COUNTER -lt 10 ]; do
       echo The counter is $COUNTER
       let COUNTER=COUNTER+1 
    done

Change to script directory (POSIX, does not follow symlink)

    cd -- "$(dirname "$0")"

Check if command exists

    if !(hash docker 2>/dev/null); then
      echo "docker command missing"
    fi


Check if dir exists (or does not):

    if [ ! -d "${dist}" ]; then
      echo "
    Missing dist directory '${dist}'. Use \`bin/deploy.sh\` to deploy.
    "
      exit 1
    fi

Make short command:

    lsla="ls -la"
    lsla /
    
Process output line by line:

    `(ansible-vault decrypt some.vault | \
    while read LINE; do echo export ${LINE}; done)`


# Hard links

You can find inode number for your file with

    ls -i

shows references count (number of hardlinks to a particular inode)

    ls -l

after you found inode number, you can search for all files with same inode will show filenames for inode NUM in current dir (.)


    find . -inum NUM

# Substrings

To remove an extension from a file name:

    v="some string.rtf"
    v2=${v::-4}
    echo "$v --> $v2"

It is bash 4+, but in earlier version you can use the slightly longer:

    ${v::${#v}-4}

# More

Date to filename:

    backup="${prefix}$(`date +%Y-%m-%d.%H:%M:%S`).xml"


Use ``"$@"` instead of plain `$@` to propagate all args:

    $ cat bar.sh
    #!/bin/bash
    baz.sh "$@"

    $ cat baz.sh
    #!/bin/bash
    echo Received: $1
    echo Received: $2
    echo Received: $3
    echo Received: $4

    $ ./bar.sh "one quoted arg"
    Received: one quoted arg

Get current subdirectory without path:

    current_subdir=${PWD##*/}

Get current user into a variable:

    current_user_=`whoami`

Find out numeric user/group id's (uid, gid)

    ls -n
    # drwxr-xr-x 8 33 33 4096 Dec 19 23:23 twentynineteen
    id 
    # uid=0(root) gid=0(root) groups=0(root)
    id www-data
    # uid=33(www-data) gid=33(www-data) groups=33(www-data)
    id -u www-data
    # 33
    id -g www-data
    # 33

Create a file from the console, using `banana` as an exit word

    cat << banana > run-on-container.sh
    #!/bin/bash
    ln -s ~/eight-octo-recipes/assets
    ln -s ~/eight-octo-recipes/uploads wp-content/
    ln -s ~/eight-octo-recipes/themes/deepbluesea wp-content/themes/
    ln -s ~/eight-octo-recipes/plugins/octofacts.php wp-content/plugins/
    banana
