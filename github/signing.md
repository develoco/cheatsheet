# Signing commit messages (macOs)

## Install GPG

    brew install gnupg

## Generate the key

https://help.github.com/en/articles/generating-a-new-gpg-key

    gpg --full-generate-key

```
gpg (GnuPG) 2.2.17; Copyright (C) 2019 Free Software Foundation, Inc.
...
Please select what kind of key you want:
   (1) RSA and RSA (default)
   ...
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits       
Please specify how long the key should be valid.
         0 = key does not expire
         ...
Key is valid for? (0) 
Key does not expire at all
Is this correct? (y/N) y
                        
GnuPG needs to construct a user ID to identify your key.

Real name: main.cc
Email address: 7463212+maincc@users.noreply.github.com
Comment:                                       
You selected this USER-ID:
    "main.cc <7463212+maincc@users.noreply.github.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O

We need to generate a lot of random bytes...

fgpg: /Users/you/.gnupg/trustdb.gpg: trustdb created
gpg: key 3FFA53694E0C87D3 marked as ultimately trusted
gpg: directory '/Users/you/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/Users/you/.gnupg/openpgp-revocs.d/13568F396A50C6902894B6A93FFA53694E0C87D3.rev'
public and secret key created and signed.

pub   rsa4096 2019-09-24 [SC]
      13568F396A50C6902894B6A93FFA53694E0C87D3
uid                      main.cc <7463212+maincc@users.noreply.github.com>
sub   rsa4096 2019-09-24 [E]
```

To verify the key you can encrypt a test message:

    echo "test" | gpg --clearsign

Find out the key ID, in this case it's `3FFA53694E0C87D3`:

    gpg --list-secret-keys --keyid-form LONG

```
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
/Users/you/.gnupg/pubring.kbx
----------------------------
sec   rsa4096/3FFA53694E0C87D3 2019-09-24 [SC]
      13568F396A50C6902894B6A93FFA53694E0C87D3
uid                 [ultimate] main.cc <7463212+maincc@users.noreply.github.com>
ssb   rsa4096/E56D8C58AF8AB05E 2019-09-24 [E]
```

    gpg --armor --export 3FFA53694E0C87D3

That Will produce something like:

```
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQINBF2J7HUBEADI0cb8DgiYTdee/pMbj3m8y/DvUeqnQJZ8LRbhBxmzKfULbKtj
...
8wfqepfmkReTULK/MNXGngnSXEOpHk7hIvSkdjJjpZkTUlNotNlh1mgfBvKRgC3q
9XdQJNiU1KwL9v1UnA==
=SGoe
-----END PGP PUBLIC KEY BLOCK-----
```

## Add the public key to your GitHub settings

https://help.github.com/en/articles/adding-a-new-gpg-key-to-your-github-account

## Install pinentry

Pinentry is needed to be able to enter your passphrase

    brew install pinentry-mac
    echo "pinentry-program /usr/local/bin/pinentry-mac" >> ~/.gnupg/gpg-agent.conf

Either reboot or do this:

    killall gpg-agent

## Configuring git

    git config --global user.signingkey 3FFA53694E0C87D3
    git config --global commit.gpgsign true

## Troubleshooting

### Locale warnings

```
Warning: Failed to set locale category LC_NUMERIC to en_PT.
Warning: Failed to set locale category LC_TIME to en_PT.
Warning: Failed to set locale category LC_COLLATE to en_PT.
Warning: Failed to set locale category LC_MONETARY to en_PT.
Warning: Failed to set locale category LC_MESSAGES to en_PT.
```

#### Solution

    echo "export LC_ALL=en_US.UTF-8" > .zshrc
    source .zshrc

### gpg failed to sign the data

    git commit -a

```
error: gpg failed to sign the data
fatal: failed to write commit object
```

https://stackoverflow.com/questions/39494631/gpg-failed-to-sign-the-data-fatal-failed-to-write-commit-object-git-2-10-0

https://stackoverflow.com/questions/41502146/git-gpg-onto-mac-osx-error-gpg-failed-to-sign-the-data

### Cant clearsign

     echo "test" | gpg --clearsign

```
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA256

test
gpg: signing failed: Inappropriate ioctl for device
gpg: [stdin]: clear-sign failed: Inappropriate ioctl for device
```

Possible solution:

    export GPG_TTY=$(tty)

https://github.com/keybase/keybase-issues/issues/2798
