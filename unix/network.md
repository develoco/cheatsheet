internetprotocol, ip, tcp, udp, network

# network diagnostics

test a port

    telnet somehost 80
    
    nmap -p 443 192.168.2.254

Another nmap option

    nmap -A 192.168.0.5/32 -p 23

Using _netcat_ (`nc`)

    nc -vz www.cyberciti.biz 443

For example, look for open ports from 20 to 30 on host.example.com

    nc -z host.example.com 20-30

find ip

    ping host
    traceroute host
    # ???
    route -n

dns

    nslookup google.com
    dig google.com
    dig google.com @8.8.8.8

Sample outputs:

    found 0 associations
    found 1 connections:
         1:	flags=82
      outif utun1
      src 10.8.0.2 port 54997
      dst 104.20.187.5 port 443
      rank info not available
      TCP aux info available

    Connection to www.cyberciti.biz port 443 [tcp/https] succeeded!

# Hit a server behind firewall

persistent reverse tunnel

    pi_at_work$ tmux
    ssh -p 2022 -R 4022:localhost:22 -N coder@breakaleg.ddns.net
