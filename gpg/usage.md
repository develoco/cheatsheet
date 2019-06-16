# Encrypting and decrypting documents

To encrypt a document the option --encrypt is used. You must have the public keys of the intended recipients.

```bash
alice% gpg --output doc.gpg --encrypt --recipient blake@cyb.org doc
```

To decrypt a message the option --decrypt is used. You need the private key to which the message was encrypted.

```bash
blake% gpg --output doc --decrypt doc.gpg
```

Documents may also be encrypted without using public-key cryptography.

```bash
alice% gpg --output doc.gpg --symmetric doc
```

# Managing keys

To communicate with others you must exchange public keys. To list the keys on your public keyring use the command-line option --list-keys.

    alice% gpg --list-keys

Exporting a public key

    alice% gpg --output alice.gpg --export alice@cyb.org

The key is exported in a binary format, but this can be inconvenient when the key is to be sent though email or published on a web page. GnuPG therefore supports a command-line option --armor[1] that that causes output to be generated in an ASCII-armored format similar to uuencoded documents. In general, any output from GnuPG, e.g., keys, encrypted documents, and signatures, can be ASCII-armored by adding the --armor option.

    alice% gpg --armor --export alice@cyb.org

A public key may be added to your public keyring with the --import option.

    alice% gpg --import blake.gpg


To encrypt a document the option --encrypt is used. You must have the public keys of the intended recipients. The --recipient option is used once for each recipient and takes an extra argument specifying the public key to which the document should be encrypted. The encrypted document can only be decrypted by someone with a private key that complements one of the recipients' public keys.

    alice% gpg --output doc.gpg --encrypt --recipient blake@cyb.org doc

To decrypt a message the option --decrypt is used. You need the private key to which the message was encrypted. Similar to the encryption process, the document to decrypt is input, and the decrypted result is output.

    blake% gpg --output doc --decrypt doc.gpg
    Enter passphrase:

Documents may also be encrypted without using public-key cryptography. Instead, only a symmetric cipher is used to encrypt the document.

    alice% gpg --output doc.gpg --symmetric doc
    Enter passphrase:

# Creating a new revokation certificate

Generate revokation certificate (invalidates public key if you lose passphrase for private key)

    gpg --output revoke.asc --gen-revoke mykey

# Validating imported keys

Once a key is imported it should be validated. GnuPG uses a powerful and flexible trust model that does not require you to personally validate each key you import. Some keys may need to be personally validated, however. A key is validated by verifying the key's fingerprint and then signing the key to certify it as a valid key. A key's fingerprint can be quickly viewed with the --fingerprint command-line option, but in order to certify the key you must edit it.

    alice% gpg --edit-key blake@cyb.org

Check the fingerprint:

    Command> fpr
    pub  1024D/9E98BC16 1999-06-04 Blake (Executioner) <blake@cyb.org>
                 Fingerprint: 268F 448F CCD7 AF34 183E  52D8 9BDE 1A08 9E98 BC16

A key's fingerprint is verified with the key's owner. This may be done in person or over the phone or through any other means as long as you can guarantee that you are communicating with the key's true owner. If the fingerprint you get is the same as the fingerprint the key's owner gets, then you can be sure that you have a correct copy of the key.
After checking the fingerprint, you may sign the key to validate it. Since key verification is a weak point in public-key cryptography, you should be extremely careful and always check a key's fingerprint with the owner before signing the key.

    Command> sign
             
    pub  1024D/9E98BC16  created: 1999-06-04 expires: never      trust: -/q
                 Fingerprint: 268F 448F CCD7 AF34 183E  52D8 9BDE 1A08 9E98 BC16

         Blake (Executioner) <blake@cyb.org>

    Are you really sure that you want to sign this key
    with your key: "Alice (Judge) <alice@cyb.org>"

Once signed you can check the key to list the signatures on it and see the signature that you have added. Every user ID on the key will have one or more self-signatures as well as a signature for each user that has validated the key.

    Command> check
    uid  Blake (Executioner) <blake@cyb.org>
    sig!       9E98BC16 1999-06-04   [self-signature]
    sig!       BB7576AC 1999-06-04   Alice (Judge) <alice@cyb.org>


# Making and verifying signatures

The command-line option --sign is used to make a digital signature. The document to sign is input, and the signed document is output.

    alice% gpg --output doc.sig --sign doc
    Enter passphrase:

The document is compressed before signed, and the output is in binary format.
Given a signed document, you can either check the signature or check the signature and recover the original document. To check the signature use the --verify option. To verify the signature and extract the document use the --decrypt option. The signed document to verify and recover is input and the recovered document is output.

    blake% gpg --output doc --decrypt doc.sig
    gpg: Signature made Fri Jun  4 12:02:38 1999 CDT using DSA key ID BB7576AC
    gpg: Good signature from "Alice (Judge) <alice@cyb.org>"

Clearsigned documents. A common use of digital signatures is to sign usenet postings or email messages. In such situations it is undesirable to compress the document while signing it. The option --clearsign causes the document to be wrapped in an ASCII-armored signature but otherwise does not modify the document.

    alice% gpg --clearsign doc

Result:

    -----BEGIN PGP SIGNED MESSAGE-----
    Hash: SHA1

    [...]
    -----BEGIN PGP SIGNATURE-----
    Version: GnuPG v0.9.7 (GNU/Linux)
    Comment: For info see http://www.gnupg.org

    iEYEARECAAYFAjdYCQoACgkQJ9S6ULt1dqz6IwCfQ7wP6i/i8HhbcOSKF4ELyQB1
    oCoAoOuqpRqEzr4kOkQqHRLE/b8/Rw2k
    =y6kj
    -----END PGP SIGNATURE-----

Detached signatures. There is a third method for signing a document that creates a detached signature. A detached signature is created using the --detach-sig option.

    alice% gpg --output doc.sig --detach-sig doc
    Enter passphrase:

Both the document and detached signature are needed to verify the signature. The --verify option can be to check the signature.

    blake% gpg --verify doc.sig doc
    gpg: Signature made Fri Jun  4 12:38:46 1999 CDT using DSA key ID BB7576AC
    gpg: Good signature from "Alice (Judge) <alice@cyb.org>"


# Generating passwords with pwgen

To start off, you will probably want to generate a new Vault passphrase and re-key all your already-encrypted Vault files.

    $ pwgen -n 71 -C | head -n1 | gpg --armor --recipient GPG_ID -e -o vault_passphrase.gpg

You can view that actual vault passphrase using:

    $ gpg --batch --use-agent --decrypt vault_passphrase.gpg

# Links

https://www.gnupg.org/gph/en/manual/c14.html
