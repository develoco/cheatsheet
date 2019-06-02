bitcoin, bip, bip32, bip44, bip39, bitcoinbip, wallet, hdwallet, hierarchical, deterministic, hierarchicaldeterministic

https://iancoleman.io/bip39/
  Mnemonic Code Converter

https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki

https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki

# Mnemonic code for generating deterministic keys (BIP 39)

# Hierarchical Deterministic Wallets (BIP 32)

# Multi-Account Hierarchy for Deterministic Wallets (BIP 44)

# Testnet experiment

Warming up

```
bitcoin-cli listwallets
bitcoin-cli createwallet defaultwallet
bitcoin-cli createwallet secondwallet
bitcoin-cli unloadwallet secondwallet
bitcoin-cli listwallets
bitcoin-cli listwalletdir
bitcoin-cli get walletinfo
bitcoin-cli unloadwallet defaultwallet
bitcoin-cli loadwallet secondwallet
```
## Generate addresses independently

https://iancoleman.io/bip39/

**bip39** mnemonic: `noble ivory naive measure bulb able quote erupt century economy disease erode`
**bip32** derivation path:
`m/0'/0`  (bread wallet)

## Find testnet faucets to fund adddresses

Load address 1 one time
Load address 2 twice
Load address 3 three times

https://coinfaucet.eu/en/btc-testnet/
We sent 0.11793052 bitcoins to address
`n3hkF3V74MtYidWwn6hyw8hSC6L5dK53ma`
tx: `b0311ed4cff0b133e41f6b77e2dcff84209837b3e32b0b99635d2bbb43f5a33b`
https://live.blockcypher.com/btc-testnet/tx/b0311ed4cff0b133e41f6b77e2dcff84209837b3e32b0b99635d2bbb43f5a33b
Send coins back, when you don't need them anymore to the address `mv4rnyY3Su5gjcDNzbMLKBQkBicCtHUtFB`

https://testnet-faucet.mempool.co
Transaction sent
TxID: f48710b64e4816557a916dfb755f2c09e33e4d77842ea3017d46ec0a51e44ca4
Address: moCUzQytxD3D5rvUQ8fUTTnSMPPPpdTG9P
Amount: 0.01
Give back mkHS9ne12qx9pS9VojpwU5xtRd4T7X7ZUt

## Import private keys, encrypt and backup

```
bitcoin-cli importprivkey "cUDpKpPComK6ZNd6essWz4iDgeDSDGXryu3KE1ywN6u7yaUA7r5C" "breadwallet0" false

bitcoin-cli getaddressesbylabel breadwallet0
#{
#  "n3hkF3V74MtYidWwn6hyw8hSC6L5dK53ma": {
#    "purpose": "receive"
#  },
#  ...
#}


bitcoin-cli importprivkey "cVtwxKKibFHHjWHVRzHF1dFqwpSbyJ3avdFL2KbjrXNcKVwuKJXY" "breadwallet1" false

bitcoin-cli listlabels
#[
#  "breadwallet0",
#  "breadwallet1"
#]


bitcoin-cli importprivkey "cQNtbpGUWhnzd1t1BpT3Wn4AVWMVPQkdZgkzPPkhTjei9igiJbvC" "breadwallet2" true

# On another tab
tail ~/Library/Application\ Support/Bitcoin/testnet3/debug.log
# 2019-02-08T07:57:39Z [defaultwallet] Still rescanning. At block 1381987. Progress=0.727705
# 2019-02-08T08:00:00Z [defaultwallet] AddToWallet b0311ed4cff0b133e41f6b77e2dcff84209837b3e32b0b99635d2bbb43f5a33b  new
# 2019-02-08T08:00:00Z [defaultwallet] AddToWallet f48710b64e4816557a916dfb755f2c09e33e4d77842ea3017d46ec0a51e44ca4  new


Bigmac:~ coder$ bitcoin-cli getbalance
0.12793052
Bigmac:~ coder$ bitcoin-cli getunconfirmedbalance
0.00000000
bitcoin-cli getwalletinfo


bitcoin-cli encryptwallet "passphrase"
wallet encrypted; The keypool has been flushed and a new HD seed was generated (if you are using HD). You need to make a new backup.

bitcoin-cli encryptwallet "passphrase"
wallet encrypted; The keypool has been flushed and a new HD seed was generated (if you are using HD). You need to make a new backup.

bitcoin-cli backupwallet defaultwallet.bak
# will be saved in the working directory of the bitcoind process (as in from where it was launched)

# Leave no trace
rm -rf ~/Library/Application\ Support/Bitcoin/testnet3/defaultwallet
```
