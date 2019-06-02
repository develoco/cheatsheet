homebrew, mac, macos, osx, brew, packagemanager, repository

# Cask for gui apps

Using cask to install an app

    brew cask install docker

# Troubleshooting brew with new macOS admin user

brew install mosh
# touch: /usr/local/Homebrew/.git/FETCH_HEAD: Permission denied
# ...
# Error: The following directories are not writable by your user:

sudo brew install mosh
# Error: Running Homebrew as root is extremely dangerous and no longer supported.

sudo chown -R $(whoami) /usr/local/Homebrew 
sudo chown $(whoami) /usr/local/etc/bash_completion.d
sudo chown -R $(whoami) /usr/local/lib/pkgconfig
sudo chown -R $(whoami) /usr/local/lib/python3.7/site-packages
sudo chown -R $(whoami) /usr/local/share/aclocal
sudo chown -R $(whoami) /usr/local/share/doc
sudo chown -R $(whoami) /usr/local/share/info
sudo chown -R $(whoami) /usr/local/share/locale
sudo chown -R $(whoami) /usr/local/share/man
sudo chown -R $(whoami) /usr/local/share/man/man1
sudo chown -R $(whoami) /usr/local/share/man/man2
sudo chown -R $(whoami) /usr/local/share/man/man3
sudo chown -R $(whoami) /usr/local/share/man/man4
sudo chown -R $(whoami) /usr/local/share/man/man5
sudo chown -R $(whoami) /usr/local/share/man/man6
sudo chown -R $(whoami) /usr/local/share/man/man7
sudo chown -R $(whoami) /usr/local/share/zsh
sudo chown -R $(whoami) /usr/local/share/zsh/site-functions
sudo chown -R $(whoami) /usr/local/var/log

ls -la /usr/local
# notice the directories owned by the previous admin

sudo chown -R $(whoami) /usr/local/Caskroom /usr/local/Cellar /usr/local/Frameworks /usr/local/bin /usr/local/etc /usr/local/include /usr/local/lib /usr/local/opt /usr/local/sbin /usr/local/share /usr/local/var

brew install mosh
# it should now work