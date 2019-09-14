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

Administrators are added to sudoers by default. First become administrator:

    su -l admin

Option 1: Edit the main sudoers file

    sudo visudo

Option 2: Create a new file in the sudoers directory

    su -l admin # Become administrator
    sudo mkdir /var/sudoers.d 
    sudo touch /var/sudoers.d/other_users
    sudo visudo -f /var/sudoers.d/other_users

Note this option appears to have some issues on macOS Catalina whereby the sudo command takes an unreasonable amount of time to complete.

Finally add the following line:

    someuser        ALL = (ALL) ALL
