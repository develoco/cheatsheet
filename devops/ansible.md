ansible, docker, vault, secret, gpg

# Install and basic usage

Install with brew

    brew install ansible

To encrypt to stdout use:

    ansible-vault encrypt --output=- < decrypted.txt > encrypted.vault

To decrypt to stdout:

    ansible-vault decrypt --vault-password-file=pwd.tmp --output=- < encrypted.vault

# Rekeying, storing passphrase with gpg

To start off, you will probably want to generate a new Vault passphrase and re-key all your already-encrypted Vault files.

    $ pwgen -n 71 -C | head -n1 | gpg --armor --recipient GPG_ID -e -o vault_passphrase.gpg

You can view that actual vault passphrase using:

    $ gpg --batch --use-agent --decrypt vault_passphrase.gpg

Now that you have the new passphrase ready to go, re-key all your already-encrypted Vault files.

    $ grep -rl '^$ANSIBLE_VAULT.*' . | xargs -t ansible-vault rekey
    This command will ask you for the old and new vault passphrases and then attempt to re-key all the files that begin with the string $ANSIBLE_VAULT (usually indicative of an Ansible Vault encrypted file).

The next thing is to make decryption painless, which is where Ansible’s `--vault-password-file` flag comes in. The argument also accepts a script.

Create an executable file called `vault_pass.sh`:

    #!/bin/sh
    gpg --batch --use-agent --decrypt vault_passphrase.gpg

Now that all the pieces are in place, invoke ansible-vault manually and make sure that the re-keying worked as expected:

    $ ansible-vault --vault-password-file=vault_pass.sh view /path/to/an/encrypted/vault/file.yml
    
You could also make your life slightly easier by adding this to your ansible.cfg, in which case you could omit the --vault-password-file argument.

    [defaults]
    vault_password_file=vault_pass.sh
