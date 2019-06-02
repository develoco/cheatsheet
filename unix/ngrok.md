ngrok, raspberrypi

# ngrok on pi

You will need an account created on ngrok.com.

Install and authenticate ngrok:

    wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-arm.zip
    ./ngrok authtoken 6RZvCRx8SPJNXuK5KNFCt_5vkhbsitqGhssvaYT3PFw
    
Use tmux to keep the connection after logout:

    tmux new-session ngrok

Start forwarding port 22, ngrok will pick a url and port:

    ./ngrok tcp 22
    # Web Interface                 http://127.0.0.1:4040                                                                                                        
    # Forwarding                    tcp://0.tcp.ngrok.io:10235 -> localhost:22

You can detach
    <control-b> d

Go to following url to check:

https://dashboard.ngrok.com/status

    ssh -p 10235 satoshi@0.tcp.ngrok.io
