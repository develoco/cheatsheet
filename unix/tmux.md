tmux

# Links

  https://gist.github.com/MohamedAlaa/2961058

# Basics (cheatsheet)

Start new unnamed session

    tmux

## Escape secuence

The default prefix is  `Ctrl-b`

For instance to detach (save) use  `Ctrl-b, d`

Re-attach:

    tmux -a -t 0

Where `0` is the number assigned.

To list sessions:

    tmux ls

Start new with session name:

    tmux new -s myname

attach:

    tmux a  #  (or at, or attach)

attach to named:

    tmux a -t myname

kill session:

    tmux kill-session -t myname

To dettach `Ctrl-b, d`

To kill the current session  `Ctrl-b, :`  and enter `kill-session` (auto-complete enabled)

## Scrolling

`Ctrl-b, [` then you can use your normal navigation keys to scroll around (eg.` Up Arrow` or `PgDn`).

Press `q` or `ESC` to quit scroll mode.

# Sessions

    :new<CR>  new session
    s  list sessions
    $  name session

# Windows (tabs)

    c  create window
    w  list windows
    n  next window
    p  previous window
    f  find window
    ,  name window
    &  kill window

# daemon

https://coderwall.com/p/purqma/use-tmux-for-a-poor-man-s-daemon

		echo "sudo tmux new-session -d -s myServiceName 'sudo -u someuser myServiceExecutable'" >> /etc/rc.local

