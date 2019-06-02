github, wiki, gh, ghwiki, githubwiki

# Images

In your wiki page markup, embed an image link in the following format:

    [[/images/path/to/image.ext|ALT TEXT]]

Result

[[/images/guy.jpg|Guy Fieri]]

# How do I add files to a wiki page?

You need to clone the wiki repo and edit it on your system.

Add files to the `files directory (or subdirectory below it). For example: `files/project-presentation.pdf`.

In your wiki page markup, add the file link in the following format:

    [link text](files/path/to/file "ALT TEXT")

For example:

    [Magic Internet Money](files/bitcoin.pdf "Satoshi's paper")

# Experiments

Link examples

    [bar]
    [foo](foo)
    [foobar](foo/bar)
    [foo](./foo)
    [[bar]]
    [[foo/bar]]

    [git notes](./git)
    [subfolder example](./bitcoin/glacier.md)

Result

[bar]
[foo](foo)
[foobar](foo/bar)
[foo](./foo)
[[bar]]
[[foo/bar]]

[git notes](./git)
[subfolder example](./bitcoin/glacier.md)
