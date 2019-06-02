# Auto-complete on macOS 10.15+

Add this to your `.zshrc`:

```zsh
autoload -Uz compinit && compinit
```

Open a new terminal tab/window and try `git ` and press _tab_.

## Troubleshooting
If you get the following warning message when opening the terminal:

```
zsh compinit: insecure directories and files, run compaudit for list.
Ignore insecure directories and files and continue [y] or abort compinit [n]?
```

Fix your permissions for the affected files:

```zsh
% compaudit
There are insecure directories and files:
/usr/local/share/zsh/site-functions
/usr/local/share/zsh
/usr/local/share/zsh/site-functions/_brew
```

```
cd /usr/local/share/zsh 
sudo chown -R root:staff .
sudo chmod -R 755 .
cd /usr/local/Homebrew/completions/zsh
sudo chown -R root:staff .
sudo chmod -R 755 .
```

Running `compaudit` should now return an empty result set.
