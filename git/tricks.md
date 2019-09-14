# Git tricks

Fast-forward a git branch without checkout. Allows for seamless switching to master/develop from a branch that was merged in via PR

    git fetch origin develop:develop

Remove all local branches except develop branch

    git branch | grep -v "develop" | xargs git branch -D

Interactively rebase when you only want to have one single initial commit

    git rebase -i --root master

Get current git branch only:

    git rev-parse --abbrev-ref HEAD

Commit empty repo

    git --bare init

Resolve a merge conflict in favour of `ours` (or `theirs`)

    git checkout --ours path/to/file

Create an orphan branch

    git checkout --orphan newbranch
    git rm -rf .
    <do work>
    git add your files
    git commit -m 'Initial commit'

Set the author globally

    git config --global user.name "Mona Lisa"
    git config --global user.email email@address.com

Set the author per project. From the repo run:

    git config user.name "Mona Lisa"
    git config user.email email@address.com
    
Amend author

    git commit --amend --author="Author Name <email@address.com>"

Recipe for erasing repo history completely and safely

    git checkout --orphan newBranch
    git add -A  # Add all files and commit them
    git commit
    git branch -D master  # Deletes the master branch
    git branch -m master  # Rename the current branch to master
    git push -f origin master  # Force push master branch to github
    git gc --aggressive --prune=all     # remove the old files

Delete tag, branch, ref from remote (new way)

    git push --delete origin 0.0.4
    
## Old school command

    git push origin :0.0.4
    
# Clear local references to deleted remote branches

    git fetch -p

Or `--prune`

# Suggest git settings

If you want to suggest some git settings to your contributors a good way is to include a .gitconfig.recommended with a comment on how to include that in the project's real git config

```
# git config include.path ../.gitconfig.recommended

[commit]
    gpgsign = true
```

for instance if you want your devs to sign their commits you can do that one above

# Back-dating commits

To set both `GIT_AUTHOR_DATE` and `GIT_COMMITTER_DATE` in the past

```bash
GIT_COMMITTER_DATE="2019-06-02 12:00:00 +0000" git commit -m "Both author and committer dates in the past" --date="2019-06-02 12:00:00 +0000"
```

To verify use `git log --format=fuller`

```
commit e1b5de7928563a249e702391f43c98ce00196c20 (HEAD -> master)
Author:     foobar <foobar@baz.local>
AuthorDate: Sun Jun 2 12:00:00 2019 +0000
Commit:     foobar <foobar@baz.local>
CommitDate: Sun Jun 2 12:00:00 2019 +0000

    Both author and committer dates in the past
```

For bulk editting of previos commits see https://alexpeattie.com/blog/working-with-dates-in-git

## Back-dating tags

    GIT_COMMITTER_DATE="2019-09-14 12:00:29 +0000" git tag -s -m "Build #5" 1.0.1

Verify

    git show --format=fuller 1.0.1

# Use Xcode as editor

    git config --global core.editor "xed -w"

## Troubleshooting

If you get an error, this might be a workaround:

    git config --global core.editor "open -a Xcode.app -W -n"

But it will launch a new instance of the app that you then have to quit for git to continue with the commit
