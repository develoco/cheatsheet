mosh, udp, ssh, remote, terminal, raspbian, catalina

# mosh on raspbian

With ssh access enabled and locale configured to UTF-8

    sudo apt-get install mosh

# mosh on macOS (Mojave)

    brew install mosh

## Troubleshooting

If connection is closed immediatelly:

```
Bigmac:~ dl$ mosh admin@Bigmac.local
Password:
bash: mosh-server: command not found
Connection to bigmac.local closed.
/usr/local/bin/mosh: Did not find mosh server startup message. (Have you installed mosh on your server?)
```

Confirm `mosh-server` is indeed installed:

```
Bigmac:~ dl$ which mosh-server
/usr/local/bin/mosh-server
```

Confirm that `mosh-server` is missing from the path when accessing through `ssh`:

```
Bigmac:~ dl$ mosh admin@Bigmac.local  'mosh-server --help'
Password:
bash: mosh-server: command not found
```

To fix it, on `~/.bashrc` (or `~/.zshrc` in macOS Catalina and above) of each user (and not on `~/.bash_profile`) add the path to `mosh_server` like this.

    export PATH=/usr/local/bin:$PATH
