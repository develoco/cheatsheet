# macOS

    brew install tor
    brew services start tor

## With Onion Browser

Install Onion browser from DMG

### Add a hidden service

Install nginx or Apache

    % cat ~/Library/Application\ Support/TorBrowser-Data/Tor/torrc

Add this to the configuration file

```
...
HiddenServiceDir /Users/you/Tor/hidden_service/
HiddenServicePort 80 127.0.0.1:80
```

Create the directory

    mkdir /Users/dl/Tor/

Launch (re-launch) Onion browser

Check hostname:

Try it on Onion browser

    % cat ~/Tor/hidden_service/hostname 
    6i2ek6tqztrvxeywvfn5ac5wtlvtwmw4kvpwczf36pjdjk7unmjhbrad.onion

### Use with SSH

Add this block to your `~/.ssh/config`. You have to replace mydomain with the host domain name or IP address and myaccount with your user name.

    Host tor_mymachine
    HostName holusvopnwtax4ac4h5d7i3mfb6z22nzvhhezrxa3jqdnq6hd3upgmyd.onion
    # Mac
    ProxyCommand nc -x 127.0.0.1:9150 -X 5 %h %p

On Linux with port `9050`

    # Linux
    ProxyCommand ncat --proxy 127.0.0.1:9050 --proxy-type socks5 %h %p
