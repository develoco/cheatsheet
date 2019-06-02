ssh

# Config

Alias

    cat ~/.ssh/config
    Host ls2x
         HostName 127.0.0.1
         User satoshi
         Port 4022

Generate key (client)

    ssh-keygen

Copy to server and add to authorized

    ssh-copy-id ls2x

The use of IdentityFile allows me to specify exactly which private key I wish to use for authentification with the given host. You can, of course, simply specify this as a command line option for "normal" connections:

    $ ssh -i ~/.ssh/blah.key username@host.com

On the config:

    Host github.com
        IdentityFile ~/.ssh/github.key

# Basic tunnel

This will forward all local port 9906 traffic to port 3306 on the remote database.example.com server, letting me point my desktop GUI to localhost (127.0.0.1:9906) and have it behave exactly as if I had exposed port 3306 on the remote server and connected directly to it.

    $ ssh -f -N -L 9906:127.0.0.1:3306 coolio@database.example.com
    # -f puts ssh in background
    # -N makes it not execute a remote command

Now I don't know about you, but remembering that sequence of flags and options for SSH can be a complete pain. Luckily, our config file can help alleviate that:

    Host tunnel
        HostName database.example.com
        IdentityFile ~/.ssh/coolio.example.key
        LocalForward 9906 127.0.0.1:3306
        User coolio

Which means I can simply do:

    $ ssh -f -N tunnel
