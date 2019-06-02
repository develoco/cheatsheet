lnd, ln, lightning, lightningnetwork, btcd, go, golang, golanguage, btcctl, lncli

# Install GO and BTCD

Install the go language and verify:

    brew install go
    go version

Add `~/go/bin` to path and test:

    cat ~/bash_profile
    export PATH=$PATH:"$(go env GOPATH)/bin"

Install btcd and verify

    go get -d github.com/btcsuite/btcd
    cd ~/go/src/github.com/btcsuite/btcd/
    GO111MODULE=on go install -v . ./cmd/...
    # To create default working directory
    btcd

Create `~/Library/Application Support/Btcd/btcd.conf`

```
[Application Options]
simnet=1
txindex=1
rpcuser=[DEFAULT BTCD]
rpcpass=[DEFAULT BTCD]
```

And start again `btcd`

Create `/Users/coder/Library/Application Support/Btcctl/btcctl.conf`

```
[Application Options]
simnet=1
rpcuser=[BTCD]
rpcpass=[BTCD]
```

And try issuing an RPC command:

    btcctl getinfo

# Install lnd

    go get -d github.com/lightningnetwork/lnd
    cd ~/go/src/github.com/lightningnetwork/lnd
    make
    make install
    # Test launch to create default working directory
    lnd

Create `~/Library/Application\ Support/Lnd/lnd.conf`

    [Application Options]
    debuglevel=info
    debughtlc=1
    [Bitcoin]
    bitcoin.simnet=1
    bitcoin.active=1
    bitcoin.node=btcd
    [btcd]
    btcd.rpcuser=foo
    btcd.rpcpass=bar

Launch again with `lnd`

To create a wallet:

    lncli create
    lncli unlock
    lncli --network=simnet getinfo
    lncli --network=simnet newaddress np2wkh

Make a note of identity_pubkey and the new address

# Additional

Launch second `lnd` instance with config file:

    $ mkdir alice
    $ cd alice

    $ cat lnd.conf
    [Application Options]
    debuglevel=info
    debughtlc=1

    datadir=data
    logdir=logs

    rpclisten=localhost:10001
    listen=localhost:10011
    restlisten=localhost:8001

    [Bitcoin]
    bitcoin.simnet=1
    bitcoin.active=1
    bitcoin.node=btcd

    [btcd]
    btcd.rpcuser=foo
    btcd.rpcpass=bar

    $ lnd --lnddir=.

Note, `lncli` shares `lnd` data directory but does not read `lnd.conf`

    lncli --lnddir=. --rpcserver=localhost:10001 create
    lncli --lnddir=. --rpcserver=localhost:10001 unlock
    lncli --lnddir=. --rpcserver=localhost:10001 --network=simnet getinfo

# Mine fake bitcoin for Bob, Alice, Charlie (and Satoshi)

    btcd --miningaddr=rrjSfLfNfXadDQdbvncbh8oaQZJv1onKp1

First one needs more then 300 to activate segwit

    btcctl generate 400

# Creating and funding channels

Connect nodes using `identity_pubkey` at (`@`) `listen` host/port

    lncli --network=simnet connect 0321646546582e1b16509e62c7302bfcde450fa669148e30709e3500e351cf7405@localhost:10011

Open and fund the channel

    lncli openchannel 03cce5cc833a6f4b8708f0540c25b0501af019573c20ecf8ef1db418644266c126 100000
    
Mine some fake blocks to validate transaction

    btcctl generate 6

Make payments

    lncli addinvoice --amt=10001

on another wallet

    lncli sendpayment --pay_req=

# Closing channel

    lncli listchannels

Make note of channel point

    "channel_point": "8aa85e0f9687555c6c2fa4e83dddc33b6e84b9301be0b73dcf8b574a0cc49ed6:0",

The channel point contains funding_txid and output_index

    lncli closechannel  --funding_txid=8aa85e0f9687555c6c2fa4e83dddc33b6e84b9301be0b73dcf8b574a0cc49ed6 --output_index=0

# RPC Web interface (not working)

```
cd ~/go/src/github.com/
mkdir mably
cd mably/
git clone https://github.com/mably/lncli-web.git
cd lncli-web/
npm install
```

Sample project (not working)

```
cd
mkdir ln-workspace
cd ln-workspace/
pip install virtualenv
virtualenv deskenv
source deskenv/bin/activate
```

```
git clone https://github.com/MaxFangX/lightning-coindesk
cd lightning-coindesk/
pip install -r requirements.txt 
pip install grpcio grpcio-tools googleapis-common-protos
python manage.py createsuperuser
python manage.py migrate
python manage.py runserver
```

Python RPC (not working)

```
cd ~/ln-workspace/
git clone https://github.com/googleapis/googleapis.git
curl -o rpc.proto -s https://raw.githubusercontent.com/lightningnetwork/lnd/master/lnrpc/rpc.proto
cat rpc.proto 
source deskenv/bin/activate
python -m grpc_tools.protoc --proto_path=googleapis:. --python_out=. --grpc_python_out=. rpc.proto
ls rpc*
mv rpc* lightning-coindesk/coindesk
pip install ipython
cd lightning-coindesk/
python manage.py shell
```

```
from coindesk import rpc_pb2 as ln, rpc_pb2_grpc as lnrpc
import grpc, os
os.environ["GRPC_SSL_CIPHER_SUITES"] = "HIGH+ECDSA"
channel = grpc.secure_channel('localhost:10002', grpc.ssl_channel_credentials(open('/Users/coder/Library/Application Support/Lnd/tls.cert', ‘rb’).read()))
stub = lnrpc.LightningStub(channel)
listchannels_resp = stub.ListChannels(ln.ListChannelsRequest())
```

Got error:

```
E1217 16:27:40.890693000 123145368068096 ssl_transport_security.c:940] Handshake failed with fatal error SSL_ERROR_SSL: error:10000410:SSL routines:OPENSSL_internal:SSLV3_ALERT_HANDSHAKE_FAILURE.
```

```
_Rendezvous: <_Rendezvous of RPC that terminated with (StatusCode.UNAVAILABLE, Connect Failed)>
```

# Solution with maccaroons

```
from coindesk import rpc_pb2 as ln, rpc_pb2_grpc as lnrpc
import grpc
import os
import binascii

class MacaroonMetadataPlugin(grpc.AuthMetadataPlugin):
    def __init__(self, macaroon):
        self.macaroon = macaroon
    def __call__(self, context, callback):
        callback([('macaroon', self.macaroon)], None)

class SignatureBackend(object):

    def authenticate(self, request, signature, csrf_token, username=None):

        os.environ["GRPC_SSL_CIPHER_SUITES"] = "HIGH+ECDSA"
        cert_creds = grpc.ssl_channel_credentials(open('/Users/coder/Library/Application Support/Lnd/tls.cert', 'rb').read())
        with open('/Users/coder/work/go/dev/bob/data/chain/bitcoin/simnet/admin.macaroon', 'rb') as f:
            macaroon_bytes = f.read()
        macaroon = binascii.hexlify(macaroon_bytes).decode()
        metadata_plugin = MacaroonMetadataPlugin(macaroon)
        auth_creds = grpc.metadata_call_credentials(metadata_plugin)
        channel = grpc.secure_channel(settings.LND_RPCHOST, grpc.composite_channel_credentials(cert_creds, auth_creds))
        # channel = grpc.insecure_channel(settings.LND_RPCHOST)
        stub = lnrpc.LightningStub(channel)

        verifymessage_resp = stub.VerifyMessage(ln.VerifyMessageRequest(msg=csrf_token, signature=signature))
```

# Trouble shooting errror on openchannel

Got an error on openchannel!

    [lncli] rpc error: code = Code(102) desc = Synchronizing blockchain

Here’s how I fixed it…
 
1: Generated new wallet and address
```
---------------BEGIN LND CIPHER SEED---------------
...
---------------END LND CIPHER SEED-----------------
roXr6iXNXhRbCxRHP7ZviMsiRCdANMuZG4
```

2: Restarted btcd using --miningaddr= the address above
3: With btcctl I generated 100 new blocks


# Random simnet seeds

password
passphrase
---------------BEGIN LND CIPHER SEED---------------
 1. abstract   2. liquid     3. inject    4. screen
 5. gain       6. balcony    7. caught    8. square
 9. edit      10. scout     11. engage   12. head  
13. frame     14. diary     15. approve  16. jazz  
17. canvas    18. hospital  19. spend    20. vital 
21. column    22. eye       23. object   24. equip 
---------------END LND CIPHER SEED-----------------

btcd --miningaddr=rjUUy5zfKCehPBivy3JtmpASban2yGnFG3
