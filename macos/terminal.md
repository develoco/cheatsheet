macintosh, mac, macos, terminal, terminalapp

https://github.com/herrbischoff/awesome-macos-command-line

# Finder

Show hidden files on finder

    defaults write com.apple.finder AppleShowAllFiles YES

List finder defaults

    defaults read com.apple.finder

# Apple File Systen (APFS)

List commands, list volumes, snapshots and cryptographic users

    diskutil apfs
    diskutil apfs list
    diskutil apfs listSnapshots disk1s1
    diskutil apfs listUsers disk1s1

# System

Install All Available Software Updates

    sudo softwareupdate -ia

# Add user to sudoers

Administrators are added to sudoers by default.

    su -l admin # Become administrator
    sudo touch /var/sudoers.d/other_users
    sudo visudo -f /var/sudoers.d/other_users

Add the following line

    someuser        ALL = (ALL) ALL
