# Multiple GitHub accounts

Generate a new private/public key pair with your GitHub email:

    ssh-keygen -t rsa -b 4096 -C "you@company.org"

Add the public key to GitHub: https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

You can use one GitHub account with `https` and your secondary account with _SSH_ using your default remote URL:

    git@github.com:[USER]/repo.git

In which case your `~/.ssh/config` would look like:

```
Host github.com              
  IdentityFile ~/.ssh/id_rsa.github
```

Or you can modify the remote URL slightly for multiple SSH accounts:

    [USER].github.com:[USER]/repo

With a corresponding SSH config:

```
Host *
  IdentitiesOnly yes # Prevents agent to sending all keys

Host [USER].github.com
  User git
  HostName github.com
  IdentityFile ~/.ssh/id_rsa.[USER]
  UseKeychain yes
  AddKeysToAgent yes
```
