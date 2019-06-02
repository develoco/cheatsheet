# Security command (Keychain)


List keychains (same visible on keychain access app)

    security list-keychains
    
Keychains are normally stored at:

    ~/Library/Keychains/
    /Library/Keychains/System.keychain

To see which one is the default:

    security default-keychain

To create a new keychain in the default folder:

    % security create-keychain                     
    keychain to create: personal 

Will create in `~/Library/Keychains/personal-db`

To show info:

    security show-keychain-info personal

To lock and unlock the keychain:

    security unlock-keychain personal
    security lock-keychain personal

Export keys and identities (no passwords)

    security export -k login.keychain

Add generic password with `-U` to replace existing entry

    security add-generic-password -a "account" -s "Service" -U -w "Secret Code" myKeychain        

Find a single generic password

    find-generic-password -s "Service" -w myKeychain
    
Find internet password

    security find-internet-password -gs "github.com"

Result:

```
keychain: "/Users/dl/Library/Keychains/login.keychain-db"
version: 512
class: "inet"
attributes:
    0x00000007 <blob>="github.com"
    0x00000008 <blob>=<NULL>
    "acct"<blob>="gitclone@icloud.com"
    ...
    "cdat"<timedate>=0x32303139303230353135353831375A00  "20190205155817Z\000"
    ...
    "mdat"<timedate>=0x32303139303230353135353831375A00  "20190205155817Z\000"
    ...
    "srvr"<blob>="github.com"
    ...
password: "secret"

```
Dump all passwords

    security dump-keychain -d myKeychain

## Certificates

Import a normal (non root/CA) certificte (defaults un-trusted)

    security import myCA.pem -k personal

Add root cert `myCA.pem` to your login (or some other) keychain and grant it trust permissions.

    security add-trusted-cert -r trustRoot -k login myCA.pem

Or add it system-wide with `-d` (need administrator password)

    security add-trusted-cert -d -r trustRoot -k login myCA.pem
