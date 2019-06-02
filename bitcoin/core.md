bitcoin, bitcoincore, macos, mojave

# Building Bitcoin core on macOS X Mojave (no gui)

https://github.com/bitcoin/bitcoin/blob/master/doc/build-osx.md

Download and untar

    mkdir org.bitcoincore && cd org.bitcoincore
    curl -l https://bitcoincore.org/bin/bitcoin-core-0.18.0/bitcoin-0.18.0.tar.gz | tar -xz
    cd bitcoin-0.18.0


For a breakdown of GUI (qt) vs non-GUI dependencies 
https://github.com/bitcoin/bitcoin/blob/master/doc/build-unix.md

    brew install automake berkeley-db4 libtool boost miniupnpc openssl pkg-config python libevent zeromq


Configure and build the headless Bitcoin Core binaries as well as the GUI (if Qt is found). You can disable the GUI build by passing --without-gui to configure.

    ./autogen.sh
    ./configure

When the intention is to run only a P2P node without a wallet, In this case there is no dependency on Berkeley DB 4.8. Bitcoin Core may be compiled in disable-wallet mode with:

    ./configure --disable-wallet --disable-tests

Build

    make

It is recommended to build and run the unit tests:

    make check

Mining is also possible in disable-wallet mode using the getblocktemplate RPC call.

    sudo make install

## Running

Bitcoin Core is now available at `./src/bitcoind` Before running, you may create an empty configuration file:

    mkdir ~/"Library/Application Support/Bitcoin"
    touch ~/"Library/Application Support/Bitcoin/bitcoin.conf"
    chmod 600 ~/"Library/Application Support/Bitcoin/bitcoin.conf"
    open -a Xcode.app ~/"Library/Application Support/Bitcoin/bitcoin.conf"

The first time you run bitcoind, it will start downloading the blockchain. This process could take many hours, or even days on slower than average systems.

You can monitor the download process by looking at the debug.log file:

    tail -f $HOME/Library/Application\ Support/Bitcoin/debug.log

Config

    cat ~/Library/Application\ Support/Bitcoin/bitcoin.conf 
    testnet=1
    server=1
    daemon=1
    txindex=1
    rpcuser=satoshi
    rpcpassword=secret
    zmqpubrawblock=tcp://127.0.0.1:28332
    zmqpubrawtx=tcp://127.0.0.1:28332

Minimal config with testnet with transaction index (needed for lightning) and pruned mainnet for desktop usage

    daemon=1
    main.prune=550
    test.txindex=1
 
 # Wallet tricks
 
```
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -getinfo
{
  "version": 170100,
  "protocolversion": 70015,
  "walletversion": 169900,
  "balance": 0.00000000,
  "blocks": 0,
  "timeoffset": 0,
  "connections": 6,
  "proxy": "",
  "difficulty": 1,
  "testnet": true,
  "keypoololdest": 1549624736,
  "keypoolsize": 1000,
  "paytxfee": 0.00000000,
  "relayfee": 0.00001000,
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -getinfo
{
  "version": 170100,
  "protocolversion": 70015,
  "walletversion": 169900,
  "balance": 0.00000000,
  "blocks": 0,
  "timeoffset": 0,
  "connections": 7,
  "proxy": "",
  "difficulty": 1,
  "testnet": true,
  "keypoololdest": 1549624736,
  "keypoolsize": 1000,
  "paytxfee": 0.00000000,
  "relayfee": 0.00001000,
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getblockchaininfo
{
  "chain": "test",
  "blocks": 0,
  "headers": 392000,
  "bestblockhash": "000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943",
  "difficulty": 1,
  "mediantime": 1296688602,
  "verificationprogress": 3.277000693027316e-08,
  "initialblockdownload": true,
  "chainwork": "0000000000000000000000000000000000000000000000000000000100010001",
  "size_on_disk": 293,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": false
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "defined",
      "startTime": 1456790400,
      "timeout": 1493596800,
      "since": 0
    },
    "segwit": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    }
  },
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getblockchaininfo
{
  "chain": "test",
  "blocks": 0,
  "headers": 424000,
  "bestblockhash": "000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943",
  "difficulty": 1,
  "mediantime": 1296688602,
  "verificationprogress": 3.27700022245608e-08,
  "initialblockdownload": true,
  "chainwork": "0000000000000000000000000000000000000000000000000000000100010001",
  "size_on_disk": 293,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": false
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "defined",
      "startTime": 1456790400,
      "timeout": 1493596800,
      "since": 0
    },
    "segwit": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    }
  },
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getblockchaininfo
{
  "chain": "test",
  "blocks": 138543,
  "headers": 1456276,
  "bestblockhash": "00000000008abd8a2a7ae5cf6b9541018cf510eed46d70d45ebc2a58abec521c",
  "difficulty": 256,
  "mediantime": 1384359511,
  "verificationprogress": 0.02543006155045141,
  "initialblockdownload": true,
  "chainwork": "000000000000000000000000000000000000000000000000024f11d93a161471",
  "size_on_disk": 305136138,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": false
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "defined",
      "startTime": 1456790400,
      "timeout": 1493596800,
      "since": 0
    },
    "segwit": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    }
  },
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getblockchaininfo
{
  "chain": "test",
  "blocks": 139088,
  "headers": 1456276,
  "bestblockhash": "000000000021dee9fb344aecab50b133625dfea43dfbe3be803f4117c0d2415b",
  "difficulty": 256,
  "mediantime": 1384454075,
  "verificationprogress": 0.02596282857003664,
  "initialblockdownload": true,
  "chainwork": "000000000000000000000000000000000000000000000000025130dd591a3373",
  "size_on_disk": 308702897,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": false
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "defined",
      "startTime": 1456790400,
      "timeout": 1493596800,
      "since": 0
    },
    "segwit": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    }
  },
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ cp ~/Desktop/defaultwallet.bak wallet.bak
gasbru17:bitcoin-0.17.1 dl$ ls
COPYING        configure.ac
Makefile      contrib
Makefile.am      doc
Makefile.in      libbitcoinconsensus.pc
aclocal.m4      libbitcoinconsensus.pc.in
autogen.sh      libtool
autom4te.cache      share
build-aux      src
config.log      test
config.status      wallet.bak
configure
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listwallets
[
  ""
]
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listwalletdir
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listwalletsdir
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listdirwallets
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listdirwallet
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli help
== Blockchain ==
getbestblockhash
getblock "blockhash" ( verbosity ) 
getblockchaininfo
getblockcount
getblockhash height
getblockheader "hash" ( verbose )
getblockstats hash_or_height ( stats )
getchaintips
getchaintxstats ( nblocks blockhash )
getdifficulty
getmempoolancestors txid (verbose)
getmempooldescendants txid (verbose)
getmempoolentry txid
getmempoolinfo
getrawmempool ( verbose )
gettxout "txid" n ( include_mempool )
gettxoutproof ["txid",...] ( blockhash )
gettxoutsetinfo
preciousblock "blockhash"
pruneblockchain
savemempool
scantxoutset <action> ( <scanobjects> )
verifychain ( checklevel nblocks )
verifytxoutproof "proof"

== Control ==
getmemoryinfo ("mode")
help ( "command" )
logging ( <include> <exclude> )
stop
uptime

== Generating ==
generate nblocks ( maxtries )
generatetoaddress nblocks address (maxtries)

== Mining ==
getblocktemplate ( TemplateRequest )
getmininginfo
getnetworkhashps ( nblocks height )
prioritisetransaction <txid> <dummy value> <fee delta>
submitblock "hexdata"  ( "dummy" )

== Network ==
addnode "node" "add|remove|onetry"
clearbanned
disconnectnode "[address]" [nodeid]
getaddednodeinfo ( "node" )
getconnectioncount
getnettotals
getnetworkinfo
getpeerinfo
listbanned
ping
setban "subnet" "add|remove" (bantime) (absolute)
setnetworkactive true|false

== Rawtransactions ==
combinepsbt ["psbt",...]
combinerawtransaction ["hexstring",...]
converttopsbt "hexstring" ( permitsigdata iswitness )
createpsbt [{"txid":"id","vout":n},...] [{"address":amount},{"data":"hex"},...] ( locktime ) ( replaceable )
createrawtransaction [{"txid":"id","vout":n},...] [{"address":amount},{"data":"hex"},...] ( locktime ) ( replaceable )
decodepsbt "psbt"
decoderawtransaction "hexstring" ( iswitness )
decodescript "hexstring"
finalizepsbt "psbt" ( extract )
fundrawtransaction "hexstring" ( options iswitness )
getrawtransaction "txid" ( verbose "blockhash" )
sendrawtransaction "hexstring" ( allowhighfees )
signrawtransaction "hexstring" ( [{"txid":"id","vout":n,"scriptPubKey":"hex","redeemScript":"hex"},...] ["privatekey1",...] sighashtype )
signrawtransactionwithkey "hexstring" ["privatekey1",...] ( [{"txid":"id","vout":n,"scriptPubKey":"hex","redeemScript":"hex"},...] sighashtype )
testmempoolaccept ["rawtxs"] ( allowhighfees )

== Util ==
createmultisig nrequired ["key",...] ( "address_type" )
estimatesmartfee conf_target ("estimate_mode")
signmessagewithprivkey "privkey" "message"
validateaddress "address"
verifymessage "address" "signature" "message"

== Wallet ==
abandontransaction "txid"
abortrescan
addmultisigaddress nrequired ["key",...] ( "label" "address_type" )
backupwallet "destination"
bumpfee "txid" ( options ) 
createwallet "wallet_name" ( disable_private_keys )
dumpprivkey "address"
dumpwallet "filename"
encryptwallet "passphrase"
getaccount (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
getaccountaddress (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
getaddressbyaccount (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
getaddressesbylabel "label"
getaddressinfo "address"
getbalance ( "(dummy)" minconf include_watchonly )
getnewaddress ( "label" "address_type" )
getrawchangeaddress ( "address_type" )
getreceivedbyaccount (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
getreceivedbyaddress "address" ( minconf )
gettransaction "txid" ( include_watchonly )
getunconfirmedbalance
getwalletinfo
importaddress "address" ( "label" rescan p2sh )
importmulti "requests" ( "options" )
importprivkey "privkey" ( "label" ) ( rescan )
importprunedfunds
importpubkey "pubkey" ( "label" rescan )
importwallet "filename"
keypoolrefill ( newsize )
listaccounts (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
listaddressgroupings
listlabels ( "purpose" )
listlockunspent
listreceivedbyaccount (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
listreceivedbyaddress ( minconf include_empty include_watchonly address_filter )
listsinceblock ( "blockhash" target_confirmations include_watchonly include_removed )
listtransactions (label count skip include_watchonly)
listunspent ( minconf maxconf  ["addresses",...] [include_unsafe] [query_options])
listwallets
loadwallet "filename"
lockunspent unlock ([{"txid":"txid","vout":n},...])
move (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
removeprunedfunds "txid"
rescanblockchain ("start_height") ("stop_height")
sendfrom (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
sendmany "" {"address":amount,...} ( minconf "comment" ["address",...] replaceable conf_target "estimate_mode")
sendtoaddress "address" amount ( "comment" "comment_to" subtractfeefromamount replaceable conf_target "estimate_mode")
setaccount (Deprecated, will be removed in V0.18. To use this command, start bitcoind with -deprecatedrpc=accounts)
sethdseed ( "newkeypool" "seed" )
settxfee amount
signmessage "address" "message"
signrawtransactionwithwallet "hexstring" ( [{"txid":"id","vout":n,"scriptPubKey":"hex","redeemScript":"hex"},...] sighashtype )
unloadwallet ( "wallet_name" )
walletcreatefundedpsbt [{"txid":"id","vout":n},...] [{"address":amount},{"data":"hex"},...] ( locktime ) ( replaceable ) ( options bip32derivs )
walletlock
walletpassphrase "passphrase" timeout
walletpassphrasechange "oldpassphrase" "newpassphrase"
walletprocesspsbt "psbt" ( sign "sighashtype" bip32derivs )

== Zmq ==
getzmqnotifications
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listwallets
[
  ""
]
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli unloadwallet ""
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listwallets
[
]
gasbru17:bitcoin-0.17.1 dl$ ls
COPYING        autom4te.cache      contrib        src
Makefile      build-aux      doc        test
Makefile.am      config.log      libbitcoinconsensus.pc    wallet.bak
Makefile.in      config.status      libbitcoinconsensus.pc.in
aclocal.m4      configure      libtool
autogen.sh      configure.ac      share
gasbru17:bitcoin-0.17.1 dl$ mv wallet.bak mywallet.dat
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli loadwallet 
COPYING                    autogen.sh                 configure                  libbitcoinconsensus.pc.in  test/
Makefile                   autom4te.cache/            configure.ac               libtool                    
Makefile.am                build-aux/                 contrib/                   mywallet.dat               
Makefile.in                config.log                 doc/                       share/                     
aclocal.m4                 config.status              libbitcoinconsensus.pc     src/                       
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli loadwallet mywallet.dat 
error code: -18
error message:
Wallet mywallet.dat not found.
gasbru17:bitcoin-0.17.1 dl$ mv mywallet.dat ~/Library/Application\ Support/
.DS_Store                                Google/                                  com.apple.sharedfilelist/
AddressBook/                             Knowledge/                               com.apple.spotlight/
Bitcoin/                                 MobileSync/                              com.apple.spotlight.Shortcuts
CallHistoryDB/                           Quick Look/                              com.apple.touristd/
CallHistoryTransactions/                 SyncServices/                            dmd/
CloudDocs/                               accountsd/                               iCloud/
CoreParsec/                              com.apple.ProtectedCloudStorage/         icdd/
CrashReporter/                           com.apple.TCC/                           videosubscriptionsd/
DiskImages/                              com.apple.backgroundtaskmanagementagent/ 
Dock/                                    com.apple.sbd/                           
gasbru17:bitcoin-0.17.1 dl$ mv mywallet.dat ~/Library/Application\ Support/
.DS_Store                                Google/                                  com.apple.sharedfilelist/
AddressBook/                             Knowledge/                               com.apple.spotlight/
Bitcoin/                                 MobileSync/                              com.apple.spotlight.Shortcuts
CallHistoryDB/                           Quick Look/                              com.apple.touristd/
CallHistoryTransactions/                 SyncServices/                            dmd/
CloudDocs/                               accountsd/                               iCloud/
CoreParsec/                              com.apple.ProtectedCloudStorage/         icdd/
CrashReporter/                           com.apple.TCC/                           videosubscriptionsd/
DiskImages/                              com.apple.backgroundtaskmanagementagent/ 
Dock/                                    com.apple.sbd/                           
gasbru17:bitcoin-0.17.1 dl$ mv mywallet.dat ~/Library/Application\ Support/
.DS_Store                                Google/                                  com.apple.sharedfilelist/
AddressBook/                             Knowledge/                               com.apple.spotlight/
Bitcoin/                                 MobileSync/                              com.apple.spotlight.Shortcuts
CallHistoryDB/                           Quick Look/                              com.apple.touristd/
CallHistoryTransactions/                 SyncServices/                            dmd/
CloudDocs/                               accountsd/                               iCloud/
CoreParsec/                              com.apple.ProtectedCloudStorage/         icdd/
CrashReporter/                           com.apple.TCC/                           videosubscriptionsd/
DiskImages/                              com.apple.backgroundtaskmanagementagent/ 
Dock/                                    com.apple.sbd/                           
gasbru17:bitcoin-0.17.1 dl$ mv mywallet.dat ~/Library/Application\ Support/Bitcoin/
bitcoin.conf  blocks/       testnet3/     
gasbru17:bitcoin-0.17.1 dl$ mv mywallet.dat ~/Library/Application\ Support/Bitcoin/
bitcoin.conf  blocks/       testnet3/     
gasbru17:bitcoin-0.17.1 dl$ mv mywallet.dat ~/Library/Application\ Support/Bitcoin/testnet3/
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli loadwallet mywallet.dat 
error code: -18
error message:
Wallet mywallet.dat not found.
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli loadwallet mywallet.dat 
error code: -18
error message:
Wallet mywallet.dat not found.
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli loadwallet mywallet.dat 
{
  "name": "mywallet.dat",
  "warning": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listwallets
[
  "mywallet.dat"
]
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getwalletinfo
{
  "walletname": "mywallet.dat",
  "walletversion": 169900,
  "balance": 0.00000000,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 2,
  "keypoololdest": 1549613772,
  "keypoolsize": 1000,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 0,
  "paytxfee": 0.00000000,
  "hdseedid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "hdmasterkeyid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli walletpassphrase
error code: -1
error message:
walletpassphrase "passphrase" timeout

Stores the wallet decryption key in memory for 'timeout' seconds.
This is needed prior to performing transactions related to private keys such as sending bitcoins

Arguments:
1. "passphrase"     (string, required) The wallet passphrase
2. timeout            (numeric, required) The time to keep the decryption key in seconds; capped at 100000000 (~3 years).

Note:
Issuing the walletpassphrase command while the wallet is already unlocked will set a new unlock
time that overrides the old one.

Examples:

Unlock the wallet for 60 seconds
> bitcoin-cli walletpassphrase "my pass phrase" 60

Lock the wallet again (before 60 seconds)
> bitcoin-cli walletlock 

As json rpc call
> curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "walletpassphrase", "params": ["my pass phrase", 60] }' -H 'content-type: text/plain;' http://127.0.0.1:8332/

gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli walletpassphrase "passphrase" 6000
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getwalletinfo
{
  "walletname": "mywallet.dat",
  "walletversion": 169900,
  "balance": 0.00000000,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 2,
  "keypoololdest": 1549613772,
  "keypoolsize": 1000,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 1549632013,
  "paytxfee": 0.00000000,
  "hdseedid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "hdmasterkeyid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli dumpwallet
error code: -1
error message:
dumpwallet "filename"

Dumps all wallet keys in a human-readable format to a server-side file. This does not allow overwriting existing files.
Imported scripts are included in the dumpfile, but corresponding BIP173 addresses, etc. may not be added automatically by importwallet.
Note that if your wallet contains keys which are not derived from your HD seed (e.g. imported keys), these are not covered by
only backing up the seed itself, and must be backed up too (e.g. ensure you back up the whole dumpfile).

Arguments:
1. "filename"    (string, required) The filename with path (either absolute or relative to bitcoind)

Result:
{                           (json object)
  "filename" : {        (string) The filename with full absolute path
}

Examples:
> bitcoin-cli dumpwallet "test"
> curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "dumpwallet", "params": ["test"] }' -H 'content-type: text/plain;' http://127.0.0.1:8332/

gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli dumpwallet /dev/stdout 
error code: -8
error message:
/dev/stdout already exists. If you are sure this is what you want, move it out of the way first
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli dumpwallet deleteme.txt
{
  "filename": "/Users/dl/src/org.bitcoincore/bitcoin-0.17.1/deleteme.txt"
}
gasbru17:bitcoin-0.17.1 dl$ cat deleteme.txt 

```











```
# Wallet dump created by Bitcoin v0.17.1.0-gef70f9b52b851c7997a9f1a0834714e3eebc1fd8
# * Created on 2019-02-08T11:42:38Z
# * Best block at time of backup was 605691 (00000000000008071fb1b2d4ab0f895a5efb4f13156ab793983771c6ac78dc85),
#   mined on 2015-11-17T03:14:04Z

# extended private masterkey: tprv8ZgxMBicQKsPd3d9h7VPkij7FsrxrhxboXvEhpHTmA49zNGo6e6scU6J5sVwfEToHkKBtBK1wQGcpWxiE4rWbrpsEu63VRT4NPoCs8e6TkD

cQNtbpGUWhnzd1t1BpT3Wn4AVWMVPQkdZgkzPPkhTjei9igiJbvC 1970-01-01T00:00:01Z label=breadwallet2 # addr=moCUzQytxD3D5rvUQ8fUTTnSMPPPpdTG9P,2N7xYgiEtSzZFQVfYgSJRm5rxZjYC9VYhpq,tb1q23pt5qm5knyrl48w8k9sghtcdcz6sr67dh7vqj
cVtwxKKibFHHjWHVRzHF1dFqwpSbyJ3avdFL2KbjrXNcKVwuKJXY 1970-01-01T00:00:01Z label=breadwallet1 # addr=mtbTQwfLVgdUUNsLxQy2uJBYWRoLTRjQJx,2MyJdx8hgo3uKsCwTo4KjQmbSjaoAMqzm1h,tb1q3aeekt0v6w49w59t8nur4jmmlyysa0gshvp2h8
cUDpKpPComK6ZNd6essWz4iDgeDSDGXryu3KE1ywN6u7yaUA7r5C 1970-01-01T00:00:01Z label=breadwallet0 # addr=n3hkF3V74MtYidWwn6hyw8hSC6L5dK53ma,2MyFt8aE745CJgKrBUjY9TpdG8E9LNz4m1T,tb1q7dw5sjmv70frmgrt85x494ahcs2j07r527xrvk
cQ9jFVApiUdQGoiLJZpANLms1oJvGmjRchVhqTWtYHJmjb845jwQ 2019-02-08T06:49:34Z change=1 # addr=2MzNduNZYqA1CE1pvyh8uWFwPZqCPpnbHis hdkeypath=m/0'/1'/147'
...
0014f4ebb675f6537852893a2210ff4a421e943c79d8 0 script=1 # addr=2NGaZPEX4vTtiTNxMTKE8YzWy88hUYANTXh

# End of dump
```













```
gasbru17:bitcoin-0.17.1 dl$ cat deleteme.txt  | grep cR8zf5rXT3rHUG8YSKHRWwpEeFasWyRLZ7CgpwTteFw9tPFrdyXw
cR8zf5rXT3rHUG8YSKHRWwpEeFasWyRLZ7CgpwTteFw9tPFrdyXw 2019-02-08T08:16:11Z reserve=1 # addr=2N5jTsutC5gTy4zqvkw4dM16VcWhokayCQX hdkeypath=m/0'/0'/0'
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getwalletinfo
{
  "walletname": "mywallet.dat",
  "walletversion": 169900,
  "balance": 0.00000000,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 2,
  "keypoololdest": 1549613772,
  "keypoolsize": 1000,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 1549632013,
  "paytxfee": 0.00000000,
  "hdseedid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "hdmasterkeyid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getblockchaininfo
{
  "chain": "test",
  "blocks": 793642,
  "headers": 1456279,
  "bestblockhash": "0000000000290b64f86c82d3dc9a1f2bf9fbfd17b49fc4be317470fb2035f9a3",
  "difficulty": 19.3625586290948,
  "mediantime": 1461846631,
  "verificationprogress": 0.3282591405776893,
  "initialblockdownload": true,
  "chainwork": "00000000000000000000000000000000000000000000000a54f315d1b7ceb1ba",
  "size_on_disk": 6944350660,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": true
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "active",
      "startTime": 1456790400,
      "timeout": 1493596800,
      "since": 770112
    },
    "segwit": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    }
  },
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getblockchaininfo
{
  "chain": "test",
  "blocks": 1456297,
  "headers": 1456297,
  "bestblockhash": "0000000000000037deea67ffd3ee51e31f309f5c89ba8b4a243dfb8da2974f8e",
  "difficulty": 14570951.39759866,
  "mediantime": 1549632712,
  "verificationprogress": 0.9999931358119777,
  "initialblockdownload": false,
  "chainwork": "0000000000000000000000000000000000000000000000f17146da9f382ef9cf",
  "size_on_disk": 23353786687,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": true
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "active",
      "startTime": 1456790400,
      "timeout": 1493596800,
      "since": 770112
    },
    "segwit": {
      "status": "active",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 834624
    }
  },
  "warnings": "Warning: unknown new rules activated (versionbit 28)"
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getwalletinfo
{
  "walletname": "mywallet.dat",
  "walletversion": 169900,
  "balance": 0.12793052,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 2,
  "keypoololdest": 1549613772,
  "keypoolsize": 1000,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 0,
  "paytxfee": 0.00000000,
  "hdseedid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "hdmasterkeyid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getnewaddress -getinfo
2N5jTsutC5gTy4zqvkw4dM16VcWhokayCQX
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getnewaddress "some address" legacy
mmJUtzm23dBs2uSktQTjEYWZg2e7meSnc7
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getnewaddress "some address" bech32
tb1qlln052a36ugvfhrdjkyaxzfssh66vpm3gvelkt
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getaddressesbylabel "some address"
{
  "mmJUtzm23dBs2uSktQTjEYWZg2e7meSnc7": {
    "purpose": "receive"
  },
  "tb1qlln052a36ugvfhrdjkyaxzfssh66vpm3gvelkt": {
    "purpose": "receive"
  }
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getnewaddress "some address"
2NDHJpcyoLSzZs7n7bkYvADVw1u8ViBNWwJ
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getaddressesbylabel "some address"
{
  "mmJUtzm23dBs2uSktQTjEYWZg2e7meSnc7": {
    "purpose": "receive"
  },
  "2NDHJpcyoLSzZs7n7bkYvADVw1u8ViBNWwJ": {
    "purpose": "receive"
  },
  "tb1qlln052a36ugvfhrdjkyaxzfssh66vpm3gvelkt": {
    "purpose": "receive"
  }
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getnewaddress "some address" legacy
moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getaddressesbylabel "some address"
{
  "mmJUtzm23dBs2uSktQTjEYWZg2e7meSnc7": {
    "purpose": "receive"
  },
  "moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL": {
    "purpose": "receive"
  },
  "2NDHJpcyoLSzZs7n7bkYvADVw1u8ViBNWwJ": {
    "purpose": "receive"
  },
  "tb1qlln052a36ugvfhrdjkyaxzfssh66vpm3gvelkt": {
    "purpose": "receive"
  }
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getaddressesbylabel ""
error code: -11
error message:
No addresses with label 
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getaddressesbylabel "-getinfo"
{
  "2N5jTsutC5gTy4zqvkw4dM16VcWhokayCQX": {
    "purpose": "receive"
  }
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listlabels
[
  "-getinfo",
  "breadwallet0",
  "breadwallet1",
  "breadwallet2",
  "some address"
]
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli getaddressesbylabel "breadwallet0"
{
  "n3hkF3V74MtYidWwn6hyw8hSC6L5dK53ma": {
    "purpose": "receive"
  },
  "2MyFt8aE745CJgKrBUjY9TpdG8E9LNz4m1T": {
    "purpose": "receive"
  },
  "tb1q7dw5sjmv70frmgrt85x494ahcs2j07r527xrvk": {
    "purpose": "receive"
  }
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli listunspent
[
  {
    "txid": "b0311ed4cff0b133e41f6b77e2dcff84209837b3e32b0b99635d2bbb43f5a33b",
    "vout": 1,
    "address": "n3hkF3V74MtYidWwn6hyw8hSC6L5dK53ma",
    "label": "breadwallet0",
    "scriptPubKey": "76a914f35d484b6cf3d23da06b3d0d52d7b7c41527f87488ac",
    "amount": 0.11793052,
    "confirmations": 50,
    "spendable": true,
    "solvable": true,
    "safe": true
  },
  {
    "txid": "f48710b64e4816557a916dfb755f2c09e33e4d77842ea3017d46ec0a51e44ca4",
    "vout": 0,
    "address": "moCUzQytxD3D5rvUQ8fUTTnSMPPPpdTG9P",
    "label": "breadwallet2",
    "scriptPubKey": "76a9145442ba0374b4c83fd4ee3d8b045d786e05a80f5e88ac",
    "amount": 0.01000000,
    "confirmations": 50,
    "spendable": true,
    "solvable": true,
    "safe": true
  }
]
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet shutdown
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli --testnet shutdown
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet stop
Bitcoin server stopping
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet -getinfo
error: Could not connect to the server 127.0.0.1:18332

Make sure the bitcoind server is running and that you are connecting to the correct RPC port.
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -getinfo
error: Could not connect to the server 127.0.0.1:8332

Make sure the bitcoind server is running and that you are connecting to the correct RPC port.
gasbru17:bitcoin-0.17.1 dl$ src/bitcoind
Bitcoin server starting
gasbru17:bitcoin-0.17.1 dl$ src/bitcoind -testnet
Bitcoin server starting
gasbru17:bitcoin-0.17.1 dl$ src/bitcoind -testnet -getinfo
Error parsing command line arguments: Invalid parameter -getinfo
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet -getinfo
{
  "version": 170100,
  "protocolversion": 70015,
  "walletversion": 169900,
  "balance": 0.00000000,
  "blocks": 1456299,
  "timeoffset": 0,
  "connections": 2,
  "proxy": "",
  "difficulty": 14570951.39759866,
  "testnet": true,
  "keypoololdest": 1549636427,
  "keypoolsize": 1000,
  "paytxfee": 0.00000000,
  "relayfee": 0.00001000,
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getwalletbalance
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet walletbalance
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getwalletinfo
{
  "walletname": "",
  "walletversion": 169900,
  "balance": 0.00000000,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 0,
  "keypoololdest": 1549636427,
  "keypoolsize": 1000,
  "keypoolsize_hd_internal": 1000,
  "paytxfee": 0.00000000,
  "hdseedid": "1d295a2c2b62293770475423e3ec7f1e0f89f49c",
  "hdmasterkeyid": "1d295a2c2b62293770475423e3ec7f1e0f89f49c",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet listwallets
[
  ""
]
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet unloadwallet ""
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet loadwalley "mywallet.dat"
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet loadwallet "mywallet.dat"
{
  "name": "mywallet.dat",
  "warning": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getwalletbalance
error code: -32601
error message:
Method not found
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getwalletinfo
{
  "walletname": "mywallet.dat",
  "walletversion": 169900,
  "balance": 0.12793052,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 2,
  "keypoololdest": 1549613772,
  "keypoolsize": 995,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 0,
  "paytxfee": 0.00000000,
  "hdseedid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "hdmasterkeyid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet settxfee 1
true
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getwalletinfo
{
  "walletname": "mywallet.dat",
  "walletversion": 169900,
  "balance": 0.12793052,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 2,
  "keypoololdest": 1549613772,
  "keypoolsize": 995,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 0,
  "paytxfee": 1.00000000,
  "hdseedid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "hdmasterkeyid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet settxfee 0.00000001
true
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet settxfee 0.000000001
error code: -3
error message:
Invalid amount
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet settxfee 0.00000001
true
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getwalletinfo
{
  "walletname": "mywallet.dat",
  "walletversion": 169900,
  "balance": 0.12793052,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 2,
  "keypoololdest": 1549613772,
  "keypoolsize": 995,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 0,
  "paytxfee": 0.00000001,
  "hdseedid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "hdmasterkeyid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet sendtoaddress moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL  0.12793052 
error code: -13
error message:
Error: Please enter the wallet passphrase with walletpassphrase first.
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet walletpassphrase "passprhase" 60
error code: -14
error message:
Error: The wallet passphrase entered was incorrect.
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet walletpassphrase "passphrase" 60
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet sendtoaddress moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL  0.12793052 
error code: -4
error message:
Error: This transaction requires a transaction fee of at least 0.00000338
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet settxfee 0.00000338
true
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet sendtoaddress moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL  0.12793052 
error code: -4
error message:
Error: This transaction requires a transaction fee of at least 0.00000338
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet sendtoaddress moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL  0.12793052 
error code: -4
error message:
Error: This transaction requires a transaction fee of at least 0.00000338
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet sendtoaddress moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL  0.12793052  "test" "someone" true
error code: -13
error message:
Error: Please enter the wallet passphrase with walletpassphrase first.
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet walletpassphrase "passphrase" 60
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet sendtoaddress moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL  0.12793052  "test" "someone" true
b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet gettransaction b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb
{
  "amount": 0.00000000,
  "fee": -0.00000338,
  "confirmations": 0,
  "trusted": true,
  "txid": "b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb",
  "walletconflicts": [
  ],
  "time": 1549636821,
  "timereceived": 1549636821,
  "bip125-replaceable": "no",
  "comment": "test",
  "to": "someone",
  "details": [
    {
      "address": "moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL",
      "category": "send",
      "amount": -0.12792714,
      "label": "some address",
      "vout": 0,
      "fee": -0.00000338,
      "abandoned": false
    },
    {
      "address": "moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL",
      "category": "receive",
      "amount": 0.12792714,
      "label": "some address",
      "vout": 0
    }
  ],
  "hex": "0200000002a44ce4510aec467d01a32e84774d3ee3092c5f75fb6d917a5516484eb61087f4000000006a473044022015f4e17dfb22ea478232825ef80cdc4215a3caaf874f9add3742fb60741f4b3b022063a9e1b32064ee96039e826287ea9f92cb426780dadb6275a9b796a98b639e95012102479aeaaa378b73e1ded761e9f97be36b8b88fbd56741c29dc99d7611c12e6676feffffff3ba3f543bb2b5d63990b2be3b337982084ffdce2776b1fe433b1f0cfd41e31b0010000006a47304402204328a64ce377cafabadc7c47933ef22f54ed2176aa7a723c4d913d7dc859fa8e02200b985372818c68465a6f2d4c10dbc079e1af21dce88376f90c9959205a56fd1b0121024ccfe53593d764dc114a50835e9c5c6e18d7cb785f3bdb17791ea0a99744ea59feffffff018a33c300000000001976a9145630c788d2cd1efd8c429c2c2c936f8aca6f243688acab381600"
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet gettransaction b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb
{
  "amount": 0.00000000,
  "fee": -0.00000338,
  "confirmations": 0,
  "trusted": true,
  "txid": "b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb",
  "walletconflicts": [
  ],
  "time": 1549636821,
  "timereceived": 1549636821,
  "bip125-replaceable": "no",
  "comment": "test",
  "to": "someone",
  "details": [
    {
      "address": "moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL",
      "category": "send",
      "amount": -0.12792714,
      "label": "some address",
      "vout": 0,
      "fee": -0.00000338,
      "abandoned": false
    },
    {
      "address": "moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL",
      "category": "receive",
      "amount": 0.12792714,
      "label": "some address",
      "vout": 0
    }
  ],
  "hex": "0200000002a44ce4510aec467d01a32e84774d3ee3092c5f75fb6d917a5516484eb61087f4000000006a473044022015f4e17dfb22ea478232825ef80cdc4215a3caaf874f9add3742fb60741f4b3b022063a9e1b32064ee96039e826287ea9f92cb426780dadb6275a9b796a98b639e95012102479aeaaa378b73e1ded761e9f97be36b8b88fbd56741c29dc99d7611c12e6676feffffff3ba3f543bb2b5d63990b2be3b337982084ffdce2776b1fe433b1f0cfd41e31b0010000006a47304402204328a64ce377cafabadc7c47933ef22f54ed2176aa7a723c4d913d7dc859fa8e02200b985372818c68465a6f2d4c10dbc079e1af21dce88376f90c9959205a56fd1b0121024ccfe53593d764dc114a50835e9c5c6e18d7cb785f3bdb17791ea0a99744ea59feffffff018a33c300000000001976a9145630c788d2cd1efd8c429c2c2c936f8aca6f243688acab381600"
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet gettxoutsetinfo b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb
error code: -1
error message:
gettxoutsetinfo

Returns statistics about the unspent transaction output set.
Note this call may take some time.

Result:
{
  "height":n,     (numeric) The current block height (index)
  "bestblock": "hex",   (string) The hash of the block at the tip of the chain
  "transactions": n,      (numeric) The number of transactions with unspent outputs
  "txouts": n,            (numeric) The number of unspent transaction outputs
  "bogosize": n,          (numeric) A meaningless metric for UTXO set size
  "hash_serialized_2": "hash", (string) The serialized hash
  "disk_size": n,         (numeric) The estimated size of the chainstate on disk
  "total_amount": x.xxx          (numeric) The total amount
}

Examples:
> bitcoin-cli gettxoutsetinfo 
> curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "gettxoutsetinfo", "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:8332/

gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet gettxoutsetinfo 
^C
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet decoderawtransaction 0200000002a44ce4510aec467d01a32e84774d3ee3092c5f75fb6d917a5516484eb61087f4000000006a473044022015f4e17dfb22ea478232825ef80cdc4215a3caaf874f9add3742fb60741f4b3b022063a9e1b32064ee96039e826287ea9f92cb426780dadb6275a9b796a98b639e95012102479aeaaa378b73e1ded761e9f97be36b8b88fbd56741c29dc99d7611c12e6676feffffff3ba3f543bb2b5d63990b2be3b337982084ffdce2776b1fe433b1f0cfd41e31b0010000006a47304402204328a64ce377cafabadc7c47933ef22f54ed2176aa7a723c4d913d7dc859fa8e02200b985372818c68465a6f2d4c10dbc079e1af21dce88376f90c9959205a56fd1b0121024ccfe53593d764dc114a50835e9c5c6e18d7cb785f3bdb17791ea0a99744ea59feffffff018a33c300000000001976a9145630c788d2cd1efd8c429c2c2c936f8aca6f243688acab381600
{
  "txid": "b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb",
  "hash": "b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb",
  "version": 2,
  "size": 338,
  "vsize": 338,
  "weight": 1352,
  "locktime": 1456299,
  "vin": [
    {
      "txid": "f48710b64e4816557a916dfb755f2c09e33e4d77842ea3017d46ec0a51e44ca4",
      "vout": 0,
      "scriptSig": {
        "asm": "3044022015f4e17dfb22ea478232825ef80cdc4215a3caaf874f9add3742fb60741f4b3b022063a9e1b32064ee96039e826287ea9f92cb426780dadb6275a9b796a98b639e95[ALL] 02479aeaaa378b73e1ded761e9f97be36b8b88fbd56741c29dc99d7611c12e6676",
        "hex": "473044022015f4e17dfb22ea478232825ef80cdc4215a3caaf874f9add3742fb60741f4b3b022063a9e1b32064ee96039e826287ea9f92cb426780dadb6275a9b796a98b639e95012102479aeaaa378b73e1ded761e9f97be36b8b88fbd56741c29dc99d7611c12e6676"
      },
      "sequence": 4294967294
    },
    {
      "txid": "b0311ed4cff0b133e41f6b77e2dcff84209837b3e32b0b99635d2bbb43f5a33b",
      "vout": 1,
      "scriptSig": {
        "asm": "304402204328a64ce377cafabadc7c47933ef22f54ed2176aa7a723c4d913d7dc859fa8e02200b985372818c68465a6f2d4c10dbc079e1af21dce88376f90c9959205a56fd1b[ALL] 024ccfe53593d764dc114a50835e9c5c6e18d7cb785f3bdb17791ea0a99744ea59",
        "hex": "47304402204328a64ce377cafabadc7c47933ef22f54ed2176aa7a723c4d913d7dc859fa8e02200b985372818c68465a6f2d4c10dbc079e1af21dce88376f90c9959205a56fd1b0121024ccfe53593d764dc114a50835e9c5c6e18d7cb785f3bdb17791ea0a99744ea59"
      },
      "sequence": 4294967294
    }
  ],
  "vout": [
    {
      "value": 0.12792714,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 5630c788d2cd1efd8c429c2c2c936f8aca6f2436 OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a9145630c788d2cd1efd8c429c2c2c936f8aca6f243688ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL"
        ]
      }
    }
  ]
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet gettransaction b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb
{
  "amount": 0.00000000,
  "fee": -0.00000338,
  "confirmations": 2,
  "blockhash": "00000000000000dd7ac648daa3429ae527608a1cdfafaac9ada2a4a7bcfbbbcb",
  "blockindex": 85,
  "blocktime": 1549637136,
  "txid": "b76d706afe42758306e0403b4c2d7fcf29bd97954bb82a3e604a4d4a535452cb",
  "walletconflicts": [
  ],
  "time": 1549636821,
  "timereceived": 1549636821,
  "bip125-replaceable": "no",
  "comment": "test",
  "to": "someone",
  "details": [
    {
      "address": "moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL",
      "category": "send",
      "amount": -0.12792714,
      "label": "some address",
      "vout": 0,
      "fee": -0.00000338,
      "abandoned": false
    },
    {
      "address": "moNgqogpLSdnCPbZHJfQLPqCWJUPCRhoKL",
      "category": "receive",
      "amount": 0.12792714,
      "label": "some address",
      "vout": 0
    }
  ],
  "hex": "0200000002a44ce4510aec467d01a32e84774d3ee3092c5f75fb6d917a5516484eb61087f4000000006a473044022015f4e17dfb22ea478232825ef80cdc4215a3caaf874f9add3742fb60741f4b3b022063a9e1b32064ee96039e826287ea9f92cb426780dadb6275a9b796a98b639e95012102479aeaaa378b73e1ded761e9f97be36b8b88fbd56741c29dc99d7611c12e6676feffffff3ba3f543bb2b5d63990b2be3b337982084ffdce2776b1fe433b1f0cfd41e31b0010000006a47304402204328a64ce377cafabadc7c47933ef22f54ed2176aa7a723c4d913d7dc859fa8e02200b985372818c68465a6f2d4c10dbc079e1af21dce88376f90c9959205a56fd1b0121024ccfe53593d764dc114a50835e9c5c6e18d7cb785f3bdb17791ea0a99744ea59feffffff018a33c300000000001976a9145630c788d2cd1efd8c429c2c2c936f8aca6f243688acab381600"
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet walletpassphrase "passphrase" 60
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getwalletinfo
{
  "walletname": "mywallet.dat",
  "walletversion": 169900,
  "balance": 0.12792714,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 3,
  "keypoololdest": 1549613772,
  "keypoolsize": 1000,
  "keypoolsize_hd_internal": 1000,
  "unlocked_until": 1549637624,
  "paytxfee": 0.00000338,
  "hdseedid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "hdmasterkeyid": "96f015915be7dfdee10a8e2f0d7b25bb26002e64",
  "private_keys_enabled": true
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getblockchaininfo
{
  "chain": "test",
  "blocks": 1456301,
  "headers": 1456301,
  "bestblockhash": "00000000000000fa8dd47cc13aaed4714faa4e0e75757c40697b6f138f5d135a",
  "difficulty": 14570951.39759866,
  "mediantime": 1549633706,
  "verificationprogress": 0.9999913781370989,
  "initialblockdownload": false,
  "chainwork": "0000000000000000000000000000000000000000000000f173e1de916d7c431e",
  "size_on_disk": 23353961207,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": true
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "active",
      "startTime": 1456790400,
      "timeout": 1493596800,
      "since": 770112
    },
    "segwit": {
      "status": "active",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 834624
    }
  },
  "warnings": "Warning: unknown new rules activated (versionbit 28)"
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli  getblockchaininfo
{
  "chain": "main",
  "blocks": 238217,
  "headers": 562159,
  "bestblockhash": "0000000000000106283f04ed1c58f3d8a9450fc15f79587c51ae1be80f4d0966",
  "difficulty": 12153411.70977583,
  "mediantime": 1369667047,
  "verificationprogress": 0.0498202703073982,
  "initialblockdownload": true,
  "chainwork": "000000000000000000000000000000000000000000000046c9f07fd547b14ec8",
  "size_on_disk": 9611659095,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": false
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    },
    "segwit": {
      "status": "defined",
      "startTime": 1479168000,
      "timeout": 1510704000,
      "since": 0
    }
  },
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli  getblockchaininfo
{
  "chain": "main",
  "blocks": 240885,
  "headers": 562159,
  "bestblockhash": "000000000000002566d5e05c68c3f2f5a17ce466543fef2e7964a810ef20374b",
  "difficulty": 15605632.68128593,
  "mediantime": 1370937605,
  "verificationprogress": 0.05190951602642762,
  "initialblockdownload": true,
  "chainwork": "00000000000000000000000000000000000000000000004f20bc382cb478b5e0",
  "size_on_disk": 10052864979,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": false
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    },
    "segwit": {
      "status": "defined",
      "startTime": 1479168000,
      "timeout": 1510704000,
      "since": 0
    }
  },
  "warnings": ""
}
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getnewaddress "five" legacy
mu2yUDruL1GzsWG4rGghVdbtgGz1pJoWaS
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getnewaddress "six"
2N3XHeZS1uuuazNPv8Cg9pNFS8tQXxMSCqa
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli -testnet getnewaddress "seven" legacy
mnNxq32m8WsvBSYDWEKbTLA6jAiAsFzkf8
gasbru17:bitcoin-0.17.1 dl$ src/bitcoin-cli  getblockchaininfo
{
  "chain": "main",
  "blocks": 330288,
  "headers": 562163,
  "bestblockhash": "00000000000000000d35045913f54211cfc35b7fffa7a654943b970d3df90564",
  "difficulty": 39603666252.41841,
  "mediantime": 1416152770,
  "verificationprogress": 0.138819148061687,
  "initialblockdownload": true,
  "chainwork": "00000000000000000000000000000000000000000002c70aedd95b33c0e66bbc",
  "size_on_disk": 29915180357,
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    },
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": false
      }
    },
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": false
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    },
    "segwit": {
      "status": "defined",
      "startTime": 1479168000,
      "timeout": 1510704000,
      "since": 0
    }
  },
  "warnings": ""
}
```
