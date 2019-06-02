macos, mojave, finder

# Tricks

## Flush cache

Mavericks and later

    sudo killall -HUP mDNSResponde

Previously

    dscacheutil -flushcache

## Exclude folders from iCloud

To exclude from iCloud drive use `tmp` as folder name. For other folders or specific files add a `.nosync` extension.

Notes:
 - Hidden files are sync'ed normally
 - Creating a `.nosync` empty file inside does not prevent the container folder from syncronizing

## Commands

Any executable without extension or with the `.command` will be run from finder when double-clicked

# Keychain access, security

Export keys and identities (no passwords)

    security export -k login.keychain

# Finder

Show hidden files on finder

    defaults write com.apple.finder AppleShowAllFiles YES

List finder defaults
    
    defaults read com.apple.finder

# Extended attributes

They show up as an `@` sign `ls -la hosts`

    -r--r--r--@ 1 root  wheel  351 Mar 11 10:56 hosts

To list the attribs `xattr hosts`

    com.apple.lastuseddate#PS

To remove all

    xattr -c hosts

Verify the `@` sign is gone

    sudo xattr -c hosts
    -r--r--r-- 1 root  wheel  351 Mar 11 10:56 hosts
