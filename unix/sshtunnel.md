ssh, tunnel, mosh, tmux


# Basic tunnel

https://hackertarget.com/ssh-examples-tunnels/
https://www.everythingcli.org/ssh-tunnelling-for-fun-and-profit-autossh/

http://blog.mattgauger.com/2012/04/21/mosh-ssh-tunnels-tmux/
https://blog.rodneyrehm.de/archives/38-You-may-not-need-localtunnel-or-ngrok.html
https://www.howtoforge.com/reverse-ssh-tunneling

    sshd -T | grep -E 'gatewayports|allowtcpforwarding'
    # gatewayports no
    # allowtcpforwarding yes

    sudo nano /etc/ssh/sshd_config
    > GatewayPorts yes

No need to reload service
    # service ssh reload

    sudo sshd -T | grep -E 'gatewayports|allowtcpforwarding'
    # gatewayports yes
    # allowtcpforwarding yes

SSH from the destination to the source (with public IP) using the command below:

    tmux new -s ssh-tunnel
    Satoshi:~ dl$ ssh -R 2022:localhost:22 -N satoshi@lightningstrikes2x.local
    ctrl-b d (detach)

Now you can SSH from source to destination through SSH tunneling

    satoshi@lightningstrikes2x:~ $ ssh -p 2022 dl@localhost
    Satoshi:~ dl$


    mosh --ssh "ssh -p 2022" -p 60100 --server=/usr/local/bin/mosh-server coder@breakaleg.ddns.net
    mosh --server=/usr/local/bin/mosh-server coder@Bigmac.local

## Check open tunnels

Outgoing

    sudo lsof -i -n -P | egrep '\<ssh\>'

Incoming (reverse)

    sudo lsof -i -n -P | egrep '\<sshd\>'

# Advanced tunneling

## Mosh reverse tunnel (UDP hole punching)

http://blog.fraggod.net/2017/06/02/upgrading-ssh-to-mosh-with-udp-hole-punching-to-connect-to-a-host-behind-nat.html

## fifo and socat for UDP

http://zarb.org/~gc/html/udp-in-ssh-tunneling.html

With fifo:

On your local machine (local), connect to the distant machine (server) by SSH, with the additional -L option so that SSH will TCP port-forward:

    local# ssh -L 6667:localhost:6667 server
  
This will allow TCP connections on the port number 6667 of your local machine to be forwarded to the port number 6667 on server through the secure channel.

Setup the TCP to UDP forward on the server. On the server, we open a listener on the TCP port 6667 which will forward data to UDP port 53 of a specified IP. If you want to do DNS forwarding like me, you can take the first nameserver's IP you will find in /etc/resolv.conf - in this example, this is 192.168.1.1. But first, we need to create a fifo. The fifo is necessary to have two-way communication between the two channels. A simple shell pipe would only communicate left process' standard output to right process' standard input.

    server# mkfifo /tmp/fifo
    server# nc -l -p 6667 < /tmp/fifo | nc -u 192.168.1.1 53 > /tmp/fifo
  
This will allow TCP traffic on server's port 6667 to be forwarded to UDP traffic on 192.168.1.1's port 53, and responses to come back.

Setup the UDP to TCP forward on your machine. Now, we need to do the opposite of what was done upper on the local machine. You need priviledged access to bind the UDP port 53.

    local# mkfifo /tmp/fifo
    local# sudo nc -l -u -p 53 < /tmp/fifo | nc localhost 6667 > /tmp/fifo
  
This will allow UDP traffic on local machine's port 53 to be forwarded to TCP traffic on local machine's port 6667.

As you've probably guessed it now, when a DNS query will be performed on the local machine, e.g. on local UDP port 53, it will be forwarded to local TCP port 6667, then to server's TCP port 6667, then to server's DNS server, UDP port 53 of 192.168.1.1. To test DNS service on your local machine, use host:

    # host m6.fr 127.0.0.1
  
If the address is resolved, you can put the following line in your /etc/resolv.conf so that your first nameserver is actually you own machine:

    nameserver 127.0.0.1

With socat:

http://www.dest-unreach.org/socat/

Server side:

    socat tcp4-listen:5353,reuseaddr,fork UDP:nameserver:53

Client side:

    socat -T15 udp4-recvfrom:53,reuseaddr,fork tcp:localhost:5353


## pwnat

http://samy.pl/pwnat/
    usage: ./pwnat <-s | -c> <args>

      -c    client mode
        <args>: [local ip] <local port> <proxy host> [proxy port (def:2222)] <remote host> <remote port>

      -s    server mode
        <args>: [local ip] [proxy port (def:2222)] [[allowed host]:[allowed port] ...]


Server side allowing anyone to proxy:
      ./pwnat -s

Client wanting to connect to google.com:80:
      ./pwnat -c 8000 <pwnat.server.com> google.com 80
Then, browse to http://localhost:8000 to visit the google!

## Layer 3 VPN through an SSH tunnel

First we need these options set in the sshd_config, we will need root privileges on both sides.

    PermitRootLogin yes
    PermitTunnel yes

SSH Client Side:

    localhost:~# ssh -v -w any root@remoteserver

Now you should have a tun device when you show interfaces (# ip a). Next step is to add IP addresses to the tunnel interfaces.

    localhost:~# ip addr add 10.10.10.2/32 peer 10.10.10.10 dev tun0
    localhost:~# ip tun0 up

SSH Server Side:

    remoteserver:~# ip addr add 10.10.10.10/32 peer 10.10.10.2 dev tun0
    remoteserver:~# ip tun0 up

    # verify
    route -n 10.10.10.10
    ping 10.10.10.10

It is now possible to route any subnet through the other side host.

    localhost:~# route add -net 10.10.10.0 netmask 255.255.255.0 dev tun0

On the remote side we need to enable ip_forward and iptables.

    remoteserver:~# echo 1 > /proc/sys/net/ipv4/ip_forward
    remoteserver:~# iptables -t nat -A POSTROUTING -s 10.10.10.2 -o enp7s0 -j MASQUERADE
