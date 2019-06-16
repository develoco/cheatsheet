gpg, pgp, encryption

# Getting started

Install GPG

    brew install gnupg
    
For password management also install `pwgen`

## Install pinentry

Pinentry is needed to be able to enter your passphrase

    brew install pinentry-mac
    echo "pinentry-program /usr/local/bin/pinentry-mac" >> ~/.gnupg/gpg-agent.conf

Either reboot your system or do this:

    killall gpg-agent

## Alternatively (not recommended) 

    brew install gpg

Generate key pair (passphrase must include a digit) and revokation certificate

    gpg --gen-key
