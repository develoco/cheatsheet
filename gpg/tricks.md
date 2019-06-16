# Restore from a full backup

Import only keys from a backed up `.gnupg` directory:

    gpg --list-keys
    gpg --homedir ~/.gnupg_old --list-keys
    gpg --homedir ~/.gnupg_old --export-secret-keys > delete.me
    gpg --import delete.me
    gpg --list-keys
    rm delete.me
